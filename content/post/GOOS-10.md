+++
categories = ["GOOS Book Distilled"]
description = "A follow through of the great book Growing Object-Oriented Software, Guided by Tests with code"
featured = "featured-goos.jpg"
featuredpath = "/img"
title = "GOOS Book Distilled Part 9"
keywords = ["Growing Object Oriented Software Guided by Tests", "TDD"]
date = 2018-02-25T19:52:11+02:00
+++

>This is a series of blog posts going through the great book [**Growing Object Oriented Software Guided By Tests**](https://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627), typing in code chapter by chapter, trying to add some of my own understanding where things may not be easy to grasp in the book. I highly recommand you get a copy of the book and follow along with me. Happy coding.

This post will cover the whole Chapter 14.

## First a Failing Test

The acceptance test we are going to add is `sniperWinsAnAuctionByBiddingHigher`. I got a bit confused about this test when I first read it. So I think it is better to clarify what it does and why. There are actually 2 concepts in this test: *WINNING* and *WON*. So this test basically can be summarise as follows:

1. The beginning is the same as the first acceptance test, start fake auction server, sniper joins and makes a bid.

2. When the sniper receives a `PRICE` message, and it is the one from the sniper itself, then the sniper is in *WINNING* state. Note that we don't even have to check the price in the message, because if it is from the sniper itself, it must be so that the sniper is winning, and if it is not from the sniper, it must be so that other bidder bids a higher price.

2. If the sniper is in *WINNING* state, and the auction closes, then it will be in *WON* state.

## Who Knows about Bidders?

To be able to tell if the `PRICE` message received is from the sniper itself. We have to put the sniper's ID into the `PRICE` itself. So when the sniper receives the message, it can check that against its own sniper ID. We also includes a sniper ID field in the `AuctionMessageTranslator`, and put the ID checking logic in it. And include this piece of info when notifying the listener about current price.

[Source code](https://github.com/lvguowei/GOOS/commit/176add884866a042a0e607b99c3852e18df481b6)

## The Sniper Has More To Say

The end-to-end test failes at *not showing WINNING state when receiving a PRICE message from itself*.
To fix this, in `AuctionSniper`, when receiving a `currentPrice` call, check it is from itself. If yes, then show *WINNING* on UI. If not, bids and show *BIDDING*.

## The Sniper Acquires Some State

The end-to-end test still fails at: not showing *WON* when the auction closes.

In order to do this, the sniper has to know what state it is in when the auction closes.


One interesting observation is how the 3 unit tests in `AuctionSniperTest` are written: `reportsLostIfAuctionClosesImmediately`, `reportsLostIfAuctionClosesWhenBidding` and `reportsWonIfAuctionClosesWhenWinning`. Pay attention to the parts: `whenBidding` and `whenWinning`. How to know the state of the sniper? One option is to create a getter for the state. But we do not want to do that because it exposes too much details about the implementation of the sniper. So we uses a feature in JMock called `States`. It works as follows: when `sniperListener`'s `sniperBidding()` is called, we set the state to *bidding*, and when `sniperWinning()` is called, set the state to *winning*. In this way, we kind of get the state of the sniper from how it is calling its listener.

[Source code](https://github.com/lvguowei/GOOS/commit/44073ae743d8873f48cea6b8d3f51a3228c49a7a)

## The Sniper Wins

The end-to-end test still fails at: not showing *WON* when the auction closes.

So we introduce a boolean called `isWinning` in `AuctionSniper`. And set it according to the `currentPrice()`.

And when `auctionClosed()` is called, check this boolean and call `sniperWon()` or `sniperLost()` accordingly.

[Source code](https://github.com/lvguowei/GOOS/commit/c3976c7615fe3d8a0f02d8ca4ba5f81cf7ee55a3)
