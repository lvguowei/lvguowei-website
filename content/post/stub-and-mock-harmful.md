+++
title = "Are Stubs and Mocks Harmful?"
date = "2016-12-18T19:53:45+02:00"
tags = [ "TDD", "mocks", "stubs" ]
categories = ["programming"]
+++

I stumbled upon this [video](https://www.youtube.com/watch?v=EaxDl5NPuCA), and boy it is so amazing! (if you ignore the annoying audience asking non-stop some annoying questions). This is clearly one of the most inspiring videos I have ever watched. So I must take some notes down and spread the idea as well.

I deeply believe that it is actually easy to make things complicated, on the contrary, it is hard to make things simple and elegant. I found some resonance from the speaker.

Why the speaker thinks that using stubs and mocks are actually harmful?

## Stubs v.s. Mocks
There is a great [essay](http://martinfowler.com/articles/mocksArentStubs.html) by Martin Fowler about this topic, go read it before you continue if you haven't done so.

## Case study 1: Clumsy Input

Here is a bloated configuration interface.

{{< highlight java >}}
public interface Config {

  // Database stuff
  String getDBHost();
  int getDBPort();
  int getMaxThreads();
  int getConnectionTimeout();
  
  // Potato settings
  String getDefaultPotatoVarieties();
  int getMaxPotatoes();
  double getPotatoShinniness();
  
  // Sacrificial settings
  int getBSGoadCount();
  int getBSChickenCount();
  int getBSSheepCount();
}
{{< /highlight >}}

Here is a class that uses the configuration interface.

{{< highlight java >}}

public class PotatoService {

  public PotatoService(Config config) {
    this.potatoVariety = config.getDefaultPotatoVarieties();
    this.maxPotatoes = config.getMaxPotatoes();
  }
  
  public Salad makePotatoSalad() {...}
  
}

{{< /highlight >}}

Now we want to test the `makePotatoSalad` function in `PotatoService`. One can easily think of using a `Config` stub.

{{< highlight java >}}

public class PotatoServiceTest {
  Config config = Mock(Config.class);
  
  @Before
  public void before() {
    // This is a stub
    when(config.getDefaultPotatoVarieties()).thenReturn("pontiac");
    when(config.getMaxPotatoes()).thenReturn(33);
  }
  
  @Test
  public void testMakingSalad() {
    PotatoService service = new PotatoService(config);
    Assert.equals(service.makePotatoSalad(), ...);
  }
}

{{< /highlight >}}

There are several observations. 

First, we stub out the config because it is hard to create and contains stuff that the SUT(System Under Test) doesn't care. So it is better to break up the fat interface into smaller ones.
But this is not the end of the story.

Stare at the `PotatoService` class a bit harder we will find that it actually needs only two things: `potatoVariety` and `maxPotatoes`. Does it matter where they come from? Why does this class needs to know about the `Config` at all? 

We can rewrite the `PotatoService` as follows:

{{< highlight java >}}

public class PotatoService {

  public PotatoService(String variety, int max) {
    this.potatoVariety = variety;
    this.maxPotatoes = max;
  }
  
  public Salad makePotatoSalad() {...}
  
}

{{< /highlight >}}

How to pass in the `variety` and `max` is someone else's business, probably some factory or DI framework. And here is the test:

{{< highlight java >}}

public class PotatoServiceTest {
  
  @Test
  public void testMakingSalad() {
    // No stubs anymore!
    PotatoService service = new PotatoService("pontiac", 33);
    Assert.equals(service.makePotatoSalad(), ...);
  }
}

{{< /highlight >}}

## Case study 2: Unnecessary Mutable State

In this example, a `Customer` can buy from some `VendingMachine` from his `Wallet`.

{{< highlight java >}}

public interface Wallet {
  int removeCoins(int amount);
  int getAmount();
}

public interface VendingMachine {
  void insertCoins(int amount);
  Can collectCan();
  int getStoredCash();
}

public interface Customer {
  void buyDrink();
}

{{< /highlight>}}

The tests look like this:

{{< highlight java >}}
public class CustomerTest {
  Wallet wallet = mock(Wallet.class);
  VendingMachine vendingMachine = mock(VendingMachine.class);
  
  @Before
  public void before() {
    // These are stubs
    when(wallet.removeCoins(3)).thenReturn(3);
    when(vendingMachine.collectCan()).thenReturn(new CokeCan());
  }
  
  @Test
  public void testBuyDrink() {
    Customer c = new Customer(wallet, vendingMachine);
    c.buyDrink();
    // These are mocks
    verify(wallet).removeCoins(3);
    verify(vendingMachine).insertCoins(3);
    verify(vendingMachine).collectCan();
  }
}
{{< /highlight >}}

Here the SUT is separated, but the stubs and mocks are being too tied to the implementation details. These tests are testing behaviours rather than the final states. What we really care is after all these, what are the final state of the wallet, the vendingMachine and the customer. The wallet and vendingMachine must have the correct amount of money in the end, and the customer gets a can of drink. We care less about what functions are called in what order and such.

The true reason we need the stubs and mocks to help us is that these classes are mutable, then change. So we need to catch the trace of how they changed over time. But if they immutable. then we don't need to trace anymore, we can just see its values. This concept should be very familiar to you if you have some functional programming background. So these classes can be rewritten as follows:

{{< highlight java >}}

public interface Wallet {
  int getAmount();
  Wallet removeCoins(int amount);
}

public interface VendingMachine {
  Optional<Can> getCanInTray();
  int getStoredCash();
  VendingMachine insertCoins(int amount);
  VendingMachine collectCan();
}

public interface Customer {
  Wallet getWallet();
  List<Can> getCansHeld();
  Pair<VendingMachine, Customer> buyDrink(VendingMachine vendingMachine);
}

{{< /highlight >}}

Then the tests becomes this:

{{< highlight java >}}

public class CustomerTest {
  @Test
  public void testBuyDrink() {
    Customer c = new Customer(new Wallet(23));
    VendingMachine vm = new VendingMachine(10, 30);
    
    Pair<VendingMachine, Customer> result = c.buyDrink(vm);
    Customer c2 = result.second();
    VendingMachine vm2 = result.first();
    
    Assert.equals(20, c2.getWallet().getAmount());
    Assert.equals(9, vm2.getCanInTray().size());
    Assert.equals(33, vm2.getStoredCash());
  }
}

{{< /highlight >}}


## Case study 3: Essential effects
This example shows how to finally deal with the side effects. Sending emails, writing databases and such. We cannot or don't want to test them directly, so using mocks can be helpful here. Like the following example:

{{< highlight java >}}

public interface EmailSender {
  void sendEmail(String address, Email email);
}

public class SpecialOffers {
  private final EmailSender sender;
  
  void sendSpecialOffers(Customer customer) {
    if (!customer.isUnsubscribed()) {
      String content = "Something...";
      sender.sendEmail(c.getEmailAddress(), new Email(content));
    }
  }
}

{{< /highlight >}}

When we test, we don't really want to send out emails, so we are going to mock the email sender.

{{< highlight java >}}

public class SpecialOfferTest {
  EmailSender sender = mock(EmailSender.class);
  
  public testSendEmail() {
    SpeicalOffers offers = new SpeicalOffers(sender);
    
    offers.sendSpecialOffers(new Customer(false, "Bob", "foo@bar.com"));
    
    // mock
    verify(sender).send("foo@bar.com", new Email("Something..."));
  }
}

{{< /highlight >}}

So what we really want to test is the **intent** of sending an email. Can we separate out the **intent** by itself?

{{< highlight java >}}

public interface SendEmailIntent {
  String getAddress();
  Email getEmail();
}

public interface interpreter {
  void interpret(SendEmailIntent i);
}

public class SpecialOffers {
  Optional<SendEmailIntent> sendSpecialOffers(Customer c) {
    if (!c.isUnsubscribed()) {
      String content = "Something...";
      return Optional.of(new SendEmailIntent(c.getEmailAddress(), new Email(content)));
    } else {
      return Optional.empty();
    }
  }
}

{{< /highlight >}}

Now the tests become:

{{< highlight java >}}

public class SpecialOfferTest {
  
  @Test
  public void testSendEmail() {
    SpecialOffers offers = new SpeicalOffers();
    
    SendEmailIntent intent = offers.sendEmail(new Customer(false, "Bob", "foo@bar.com")).get();
    
    Assert.equals("foo@bar.com", intent.getAddress());
    Assert.equals("Something...", intent.getEmail().getText());
  }
}

{{< /highlight >}}


These examples really opened my eye as to how you can leverage some of the good parts from FP into OO programming.
