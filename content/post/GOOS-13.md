+++
categories = ["GOOS Book Distilled"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 12"
date = 2018-03-17T21:42:01+02:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

In this post, we start Chapter 16 *Sniping for Multiple Items*.

## A Tale of Two Items

First an acceptance test.

The test is very similar to one auction item.

1. Create 2 `FakeAuctionServer`s.

2. Start selling on both auction servers.

3. Application start bidding in two auctions. Asserts both auction servers receive join request.

4. Auction server 1 reports other bidder bidding, then asserts that it receives a higher bid from application.

5. Auction server 2 reports other bidder bidding, then asserts that it receives a hight bid from application.

6. Auction server 1 reports the price where application as the current bidder, asserts that the application shows `WINNING`.

7. Auction server 2 reports the price where application as the current bidder, asserts that the application shows `WINNING`.

8. Both auction servers announces close.

9. Asserts that both auction has won in the application

[Source code](https://github.com/lvguowei/GOOS/commit/fc10773c916be8fa1248b1ceffa9d2d26be2dfef)

## The ApplicationRunner

Now we need to pass the following to the `main()`:

1. Credentials for the XMPP Connection, including `XMPP_HOSTNAME`, `SNIPER_ID` and `SNIPER_PASSWORD`.

2. The auctions' `item-id`s.

## A Diversion, Fixing the Failure Message

Use `hasProperty` method in hamcrest to create a better error message.

{{< highlight java >}}
assertThat(message, hasProperty("body", messageMatcher));
{{< /highlight >}}

{{< highlight java >}}
/**
   * Creates a matcher that matches when the examined object has a JavaBean property
   * with the specified name.
   * <p/>
   * For example:
   * <pre>assertThat(myBean, hasProperty("foo"))</pre>
   * 
   * @param propertyName
   *     the name of the JavaBean property that examined beans should possess
   */
   public static <T> org.hamcrest.Matcher<T> hasProperty(java.lang.String propertyName) {
    return org.hamcrest.beans.HasProperty.<T>hasProperty(propertyName);
  }
{{< /highlight >}}

[Source code](https://github.com/lvguowei/GOOS/commit/cab5713345194d2d1f38774c99cc77c94f5d5c2b)

## Restructuring the Main

Change the `Main` so that it extracts the auction servers and joins all of them.

[Source code](https://github.com/lvguowei/GOOS/commit/ac6cbd8e6eea5958ea6d786fa647f821a27b2abb)

## Extending the TableModel

Now the `SnipersTableModel` needs to hold a list of `SnaperSnapshot`s. And a method to add one on the Swing's thread.

Add 2 unit tests: 1. notifiesListenersWhenAddingASniper 2. holdsSnipersInAdditionOrder

Now the acceptance test should pass.

[Source code](https://github.com/lvguowei/GOOS/commit/238d70cc490cf6bed7ccf8bc667062a4c15ef430)
