+++
date = "2016-12-18T09:54:57+02:00"
title = "Coffee Maker - An OOD case study"
tags = [ "OOD", "uncle bob" ]
categories = ["programming"]
+++

This case study is to show case how to design classes that interact with each other to form a component.

The problem is to implement a software component that controls a coffee maker.

# Requirement

> The Mark IV Special makes up to 12 cups of coffee at a time. The user places a filter
> in the filter holder, fills the filter with coffee grounds, and slides the filter holder into its receptacle. The user then pours up to 12 cups of water into the water strainer and presses
> the Brew button. The water is heated until boiling. The pressure of the evolving steam
> forces the water to be sprayed over the coffee grounds, and coffee drips through the filter
> into the pot. The pot is kept warm for extended periods by a warmer plate, which only
> turns on if there is coffee in the pot. If the pot is removed from the warmer plate while
> water is being sprayed over the grounds, the flow of water is stopped so that brewed coffee
> does not spill on the warmer plate. The following hardware needs to be monitored or controlled:

> - The heating element for the boiler. It can be turned on or off.
> - The heating element for the warmer plate. It can be turned on or off.
> - The sensor for the warmer plate. It has three states: warmerEmpty, potEmpty, potNotEmpty .
> - A sensor for the boiler, which determines whether there is water present. It has two states: boilerEmpty or boilerNotEmpty .
> - The Brew button. This is a momentary button that starts the brewing cycle. It has an indicator that lights up when the brewing cycle is over and the coffee is ready.
> - A pressure-relief valve that opens to reduce the pressure in the boiler. The drop in pressure stops the flow of water to the filter. It can be opened or closed.

# A Bad Design

The most common mistake is to design the objects as a direct map to the Mark IV Offee Maker as following.

{{< figure src="/img/coffee-maker-bad.jpg" >}}

There are several problems about this design:

## Missing methods

In the diagram there is no methods. When there is no methods shown in the diagrams, the system is not partitioned based on behaviour, which can leads to big mistakes.

## Vapor classes

Take a closer look at class `Light`, it might look something like this:

{{< highlight java >}}

public class Light {
    public void on() {
    CoffeeMakerAPI.api.
        setIndicatorState(CoffeeMakerAPI.INDICATOR_ON);
    }
    public void off() {
    CoffeeMakerAPI.api.
        setIndicatorState(CoffeeMakerAPI.INDICATOR_OFF);
    }
}

{{< /highlight >}}

This class simply forwards the API calls and there are no variables. 
The same applies to other classes as well, they don't have much value to exist. The `CoffeeMaker` class can call the api methods directly anyway.

## Imaginary Abstractions

It is quite obvious that the interfaces in this design are pretty useless and poor.
First of all, no other classes use these interfaces, they only use the implementations of these interfaces.

Then, there are no common code in those interfaces.

## God class

If there are a lot of vapor classes and useless interfaces, then the true logit must be all in the `CoffeeMaker` class, which makes it a God class.

The reason that leads to this bad design is to simply find some verbs and nouns in the description and map them to classes without analyzing and abstracting the behaviour of the system.

# A Better Design

First we have to forget about Mark IV Coffee Maker and thinking only about high level coffee making.

We will have 3 classes: `UserInterface`, `HotWaterSource` and `ContainmentVessel`.

{{< figure src="/img/coffee-maker-good.jpg" >}}

For the complete implementation please check [source code with tests] (https://github.com/lvguowei/coffee-maker-case-study).
