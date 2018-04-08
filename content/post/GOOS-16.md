+++
categories = ["Object Oriented Design"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 14"
date = 2018-04-06T21:34:52+03:00
+++

This post covers *Chapter 17* of the book: *Teasing Apart Main*.

## Finding a Role

I think one of the most important skills that a programmer needs to train on, is the instinct of what and where to improve. The ability to see the flaws and have a clear vision of what he/she would like the software to work. And having the vision sometimes is more important than knowing how to achieve there. So how to have the vision in the first place? The answer is to be exposed to good stuff as much as possible. Really, this is true in developing all the skills. Once you know how you would like the final product to be, getting there is only a matter of time and practice. It's like if you pair programming with a master programmer, you don't need to remember every single move of his, but you do need to pay special attention to the high level vision that guides him there, like what makes he make the change? What bothers him normally and how to improve? and so on and so forth.

Enough rambling now back to our auction sniper project.

We feel that the main becomes a kitchen sink. It makes us uncomfortable and needs to be refactored. What are the things that are making us uncomfortable? This is a really good question and the author of the book anwsered it beautifully. First we should have a vision for a good **Main** of any program. What should **Main** do and what should it contain? The general idea is that **Main** should really only knows the top high level of the project. Initiate them, connect them and kick start the program then let go. That's it. Having that in mind and take a look at our **Main** should make you frown. It has the `Chat` from smack library, Swing related code, `SnipersTableModel`, `AuctionMessageTranslator` to name a few. They are too low level stuff and should be hidden from main.

## Extracting the Chat

The goal is to remove any reference of `Chat` from `Main`. We can move `Chat` into `XMPPAuction`, this makes sense because the `XMPPAuction` needs a `Chat` connection to do stuff anyways.

[Isolating the Chat](https://github.com/lvguowei/GOOS/commit/faa9da92796e94b1e9257198a0457c41bd6e1b0b)

[Encapsulating the Chat](https://github.com/lvguowei/GOOS/commit/0d8c16bc067b5db2e5bda04de9e85a70eed674f6)

[Writing a new Test](https://github.com/lvguowei/GOOS/commit/a91d8bf3a08229f8268b83ea812767e62b9d93d4)

## Extracting the Connection

The goal is to remove any reference of `XMPPConnection` from `Main`. We create a `AuctionHouse` which serves as a factory for `Auction`. The `AuctionHouse` is a singleton, it contains a `XMPPConnection`. To make `Auction`s, the only thing it needs is an `itemId`. In this way, we hide the `XMPPConnection` inside the `AuctionHouse`.

[Source code](https://github.com/lvguowei/GOOS/commit/18a2e255d85293c621020596a0fb82011ec7122a)

## Extracting the SnipersTableModel

To be honest, this is where I finally lost it in the book, just too many classes and interfaces. I find it that sometimes OO design can be really confusing, because it doesn't give a clear view of the sequence that things happen. So to clear our mind, let's start all over again and think what is really going on beyond classes and interfaces.

The application starts. Shows the UI window. The window has an empty **table view**, a **text field** and a **join button**. Now let's talk about these views. Firstly, the empty **table view** has to know that a new sniper is added and adds it to its rows. After adding a new sniper, it also has to be able to update that row when the sniper's state changes. Secondly, when the **join button** gets clicked, we should extract the `item id` from **text field**, create a `Chat` with it to the server then send a `JOIN` message. Also we need to start listening for `CLOSE` and `PRICE` messages in the chat. When a `CLOSE` message is received, we need to display whether our sniper has won or not based on some conditions. When a `PRICE` message is received, if the price is from the current sniper, then that means our sniper is winning, otherwise, we need to send a higher bid to the server, and also showing that the sniper is bidding.

Believe it or not, this is all the sniper application has to do so far. Now let's go through the classes and interfaces.

### XMPPAuction

The `XMPPAuction` class implements `Auction` interface. Which contains only 3 methods: `bid(int price)`, `join()`, and `AddAuctionEventListener(AuctionEventListener listener)`. So we can think of its responsibilities contains:

1. Making a bid, by sending a `BID` message to the chat.

2. Joining an auction, by sending a `JOIN` message to the chat.

3. Listening to chat messages sent from the server, by adding an `AuctionEventListener`, this listener is passed to `AuctionMessageTranslator`. This listener will be notified when the auction is closed or there is a price update.

{{< highlight java >}}

public class XMPPAuction implements Auction {
    private final Announcer<AuctionEventListener> auctionEventListeners = Announcer.to(AuctionEventListener.class);
    private final Chat chat;
    
    ...
    
    public XMPPAuction(XMPPConnection connection, String itemId) {
        chat = connection.getChatManager().createChat(
                auctionId(itemId, connection),
                new AuctionMessageTranslator(connection.getUser(), auctionEventListeners.announce()));
    }

    @Override
    public void bid(int price) {
        sendMessage(String.format(BID_COMMAND_FORMAT, price));
    }

    @Override
    public void join() {
        sendMessage(JOIN_COMMAND_FORMAT);
    }

    @Override
    public void addAuctionEventListener(AuctionEventListener auctionEventListener) {
        auctionEventListeners.addListener(auctionEventListener);
    }
    
    ...
}

{{< /highlight >}}

The `XMPPAuction` is a pretty low level component in the application, it handles sending XMPP messages and listening to messages sent from the server, including parsing the messages and creating a chat and etc. These are pretty low level functionalities. So we can imagine there must be higher level components that uses it as a base. 


### XMPPAuctionHouse

Since the application supports multiple auctions, so there must be multiple `XMPPAuction` instances. They all share the same `XMPPConnection`, actually the application needs only one `XMPPConnection`. The only difference is the `item id`. So the `XMPPAuctionHouse` is a factory, upon initiate it creates a connection to the server, then it uses that connection plus a given `item id` to create `XMPPAuction`s.

So the `XMPPAuctionHouse` maintains a XMPP connection, and create `XMPPAuction`s using it and the given `item id`.

{{< highlight java >}}

public class XMPPAuctionHouse implements AuctionHouse {
    private final XMPPConnection connection;

    private XMPPAuctionHouse(XMPPConnection connection) {
        this.connection = connection;
    }

    @Override
    public Auction auctionFor(String itemId) {
            return new XMPPAuction(connection, itemId);
    }

    public static XMPPAuctionHouse connect(String hostname, String username, String password) throws XMPPException {
        XMPPConnection connection = new XMPPConnection(hostname);
        connection.connect();
        connection.login(username, password, AUCTION_RESOURCE);
        return new XMPPAuctionHouse(connection);
    }

    public void disconnect() {
        connection.disconnect();
    }
}

{{< /highlight >}}

### AuctionSniper

We mentioned that `XMPPAuction` will notify to the outside when the auction is closed and the current price updates. But who will be listening to these? The answer is `AuctionSniper`.

{{< highlight java >}}

public class AuctionSniper implements AuctionEventListener {

    private SniperSnapshot snapshot;
    private final Announcer<SniperListener> listeners = Announcer.to(SniperListener.class);
    private Auction auction;

    AuctionSniper(String itemId, Auction auction) {
        this.auction = auction;
        this.snapshot = SniperSnapshot.joining(itemId);
    }

    public void addSniperListener(SniperListener listener) {
        this.listeners.addListener(listener);
    }

    @Override
    public void auctionClosed() {
        snapshot = snapshot.closed();
        notifyChange();
    }

    @Override
    public void currentPrice(int price, int increment, PriceSource priceSource) {
        switch (priceSource) {
            case FromSniper:
                snapshot = snapshot.winning(price);
                break;
            case FromOtherBidder:
                int bid = price + increment;
                auction.bid(bid);
                snapshot = snapshot.bidding(price, bid);
                break;
        }
        notifyChange();
    }

    private void notifyChange() {
        listeners.announce().sniperStateChanged(snapshot);
    }

    public SniperSnapshot getSnapshot() {
        return snapshot;
    }
}

{{< /highlight >}}

The `AuctionSniper` itself has its listeners. But it only has one method: `sniperStateChanged(snapshot)`. What does this tell you? It is pretty clear that `AuctionSniper` takes what it is listening to and transforms the information into a form that better suits out application. To be more specific, it takes in the `currentPrice()` and `auctionClosed()`, and creates and keeps a `SniperSnapshot` and notify to outside only this snapshot.

### SniperPortfolio

Since we know that there will be multiple `AuctionSniper`s, who owns them? The anwser is `SniperPortfolio`. It has a list of `AuctionSniper`s, and when a new one is added, it notifies the outside.

{{< highlight java >}}

public class SniperPortfolio implements SniperCollector {

    public interface PortfolioListener extends EventListener {
        void sniperAdded(AuctionSniper sniper);
    }

    private final List<AuctionSniper> snipers = new ArrayList<>();
    private final Announcer<PortfolioListener> announcer = Announcer.to(PortfolioListener.class);

    @Override
    public void addSniper(AuctionSniper sniper) {
        snipers.add(sniper);
        announcer.announce().sniperAdded(sniper);
    }

    public void addPortfolioListener(PortfolioListener listener) {
        announcer.addListener(listener);
    }
}

{{< /highlight >}}

This class is simple and has only one clear responsibility.


