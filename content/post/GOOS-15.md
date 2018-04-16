+++
categories = ["Object Oriented Design"]
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "Some explanation of the Announcer"
date = 2018-03-30T09:48:48+02:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

In the GOOS book, the author at some point mentioned that a `Announcer` class from [JMock](https://github.com/jmock-developers/jmock-library/blob/master/jmock-example/src/main/org/jmock/example/announcer/Announcer.java) is used. The rational of the decision is not given. So this left me puzzled for some time until I finally did some research on it. Let us see first a problem.

Imagine you have a big listener class.

{{< highlight java >}}
public interface MyListener {
  void onNameChanged(String name);
  void onAgeChanged(int age);
  void onAddressChanged(String address);
  void onPhoneChanged(String phone);
  void onPostalCodeChanged(String code);
  void onCompanyChanged(String company);
  void onLanguageChanged(String lang);
}
{{< /highlight >}}

Now in some place, you need a list of them so that you can notify all of them when some event is triggered.

{{< highlight java >}}
public class MyClass {

  private List<MyListener> listeners = new ArrayList<>();
  
  public void addListener(MyListener listener) {
    listeners.add(listener);
  }
  
  public void removeListener(MyListener listener) {
    listeners.remove(listener);
  }
  
  void onNameChanged(String name) {
    for (MyListener listener : listeners) {
      listener.onNameChanged(name);
    }
  }
  void onAgeChanged(int age) {
    for (MyListener listener : listeners) {
        listener.onAgeChanged(age);
    }
  }
  void onAddressChanged(String address) {
    for (MyListener listener : listeners) {
      listener.onAddressChanged(address);
    }
  }
  void onPhoneChanged(String phone) {
    for (MyListener listener : listeners) {
      listener.onPhoneChanged(phone);
    }
  }
  void onPostalCodeChanged(String code) {
    for (MyListener listener : listeners) {
      listener.onPostalCodeChanged(code);
    }
  }
  void onCompanyChanged(String company) {
    for (MyListener listener : listeners) {
      listener.onCompanyChanged(company);
    }
  }
  void onLanguageChanged(String lang) {
    for (MyListener listener : listeners) {
      listener.onLanguageChanged(lang);
    }
  }
}
{{< /highlight >}}

Phew! That's quite some code. Actually they look very repeatitive. And they takes up a lot of space in `MyClass`. Can we improve this? Yes, we can use the *Composition Pattern* to create a `CompositeListener`:

{{< highlight java >}}
public class CompositeListener implements MyListener {

  private List<MyListener> listeners = new ArrayList<>();
  
  public void addListener(MyListener listener) {
    listeners.add(listener);
  }
  
  public void removeListener(MyListener listener) {
    listeners.remove(listener);
  }
  
  @Override
  void onNameChanged(String name) {
    for (MyListener listener : listeners) {
      listener.onNameChanged(name);
    }
  }
  
  @Override
  void onAgeChanged(int age) {
    for (MyListener listener : listeners) {
        listener.onAgeChanged(age);
    }
  }
  
  @Override
  void onAddressChanged(String address) {
    for (MyListener listener : listeners) {
      listener.onAddressChanged(address);
    }
  }
  
  @Override
  void onPhoneChanged(String phone) {
    for (MyListener listener : listeners) {
      listener.onPhoneChanged(phone);
    }
  }
  
  @Override
  void onPostalCodeChanged(String code) {
    for (MyListener listener : listeners) {
      listener.onPostalCodeChanged(code);
    }
  }
  
  @Override
  void onCompanyChanged(String company) {
    for (MyListener listener : listeners) {
      listener.onCompanyChanged(company);
    }
  }
  
  @Override
  void onLanguageChanged(String lang) {
    for (MyListener listener : listeners) {
      listener.onLanguageChanged(lang);
    }
  }
}
{{< /highlight >}}

And now in `MyClass` we just use `CompositeListener` instead:

{{< highlight java >}}
public class MyClass {

  private CompositeListener compositeListener = new CompositeListener();
  
  public void addListener(MyListener listener) {
    compositeListener.addListener(listener);
  }
  
  public void removeListener(MyListener listener) {
    compositeListener.removeListener(listener);
  }
  
  public void doWork() {
  // some work
  compositeListener.onPhoneChanged(phone);
  // some more work
  compositeListener.onLanguageChanged("en");
  // ...
  }
}

{{< /highlight >}}

Now the `MyClass` is cleaner, so it is a good improvement. But if you look closer, we just moved all the ugliness into `CompositeListener`, and things really haven't changed that much.

Can we do better still? The anwser is yes, but we need some black magic, *reflection* for help. This is the reason to have `Announcer`.

First, let me paste the code of `Announcer` here for easier reference.

{{< highlight java >}}

public class Announcer<T extends EventListener> {
    private final T proxy;
    private final List<T> listeners = new ArrayList<T>();


    public Announcer(Class<? extends T> listenerType) {
        proxy = listenerType.cast(Proxy.newProxyInstance(
                listenerType.getClassLoader(),
                new Class<?>[]{listenerType},
                new InvocationHandler() {
                    public Object invoke(Object aProxy, Method method, Object[] args) throws Throwable {
                        announce(method, args);
                        return null;
                    }
                }));
    }

    public void addListener(T listener) {
        listeners.add(listener);
    }

    public void removeListener(T listener) {
        listeners.remove(listener);
    }

    public T announce() {
        return proxy;
    }

    private void announce(Method m, Object[] args) {
        try {
            for (T listener : listeners) {
                m.invoke(listener, args);
            }
        }
        catch (IllegalAccessException e) {
            throw new IllegalArgumentException("could not invoke listener", e);
        }
        catch (InvocationTargetException e) {
            Throwable cause = e.getCause();

            if (cause instanceof RuntimeException) {
                throw (RuntimeException)cause;
            }
            else if (cause instanceof Error) {
                throw (Error)cause;
            }
            else {
                throw new UnsupportedOperationException("listener threw exception", cause);
            }
        }
    }

    public static <T extends EventListener> Announcer<T> to(Class<? extends T> listenerType) {
        return new Announcer<T>(listenerType);
    }
}
{{< /highlight >}}

First thing to notice is that this is a generic class, so it can be used for all listeners, this is very helpful so that we don't need to create a composite listener for every single listener that we have.

All the magic happens in the `proxy` field. As the name suggests, it serves as a proxy. And it is of the **same type** as the real listener class. So basically this `proxy` is a composite listener in disguise. The outer world sees it as a single listener, but when invoke any method on it, it will instead call all the listeners inside it.

It has the same idea as our composite listener, but with much less code.

Now the `MyClass` looks like:

{{< highlight java >}}
public class MyClass {

  private Announcer<MyListener> announcer = Announcer.to(MyListener.class);
  
  public void addListener(MyListener listener) {
    announcer.addListener(listener);
  }
  
  public void removeListener(MyListener listener) {
    announcer.removeListener(listener);
  }
  
  public void doWork() {
  // some work
  announcer.announce().onNameChanged("Bob");
  // some more work
  announcer.announce().onAgeChanged(19);
  // ...
  }
}
{{< /highlight >}}

And there is no other wrapper classed needed anymore. Neat!
