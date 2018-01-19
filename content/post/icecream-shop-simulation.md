+++
categories = ["Concurrent Programming"]
description = "An example that shows how to use semaphore to code complicated concurrent program"
featured = "featured-icecream-shop.jpg"
featuredpath = "/img"
title = "Icecream Shop Simulation"
date = 2018-01-19T21:08:08+02:00
+++

I have been watching the good old Stanford CS course *Programming Paradigms*. The first half of the course deals with C and serves as great material for learning C. Then the course introduced threads, and concurrent programming in C with some homebrew library. The most complicated example given is this program that simulated the icecream shop. Here are the description of the problem:


>This program simulates the daily activity in an ice cream store. The actors in this
>simulation are the clerks who make ice cream cones, the manager who supervises the
>work of the clerks, the customers who want to buy the cones, and the cashier who rings
>up each customer. A different thread is launched for each of the players.

>Each customer wants to order a few ice cream cones, wait for them to be made, then get
>in line to pay the cashier, and leave after paying. The customer is in a big hurry and
>doesn't want to wait for one clerk to make several cones, so instead the customer
>dispatches one clerk thread independently for each cone. Once the customer has gotten
>their cones made, they get in line for the cashier and wait for their turn. After paying the
>cashier, the customer leaves.

>Each clerk thread makes exactly one ice cream cone. The clerk scoops up a cone and
>then has to have the manager take a look at it to make sure it is just right. If the cone
>doesn't pass muster, it is thrown away and the clerk has to make another. Once a cone
>passes, the clerk hands the cone to the customer and is done.

>The manager sits idle until a clerk needs a cone inspected. When it gets an inspection
>request, the inspector determines if it passes and lets the clerk know how the cone fared.
>The manager is done when all cones have been inspected.

>In order to avoid fights breaking out, the line for the cashier must be maintained in FIFO
>order. Thus after getting their cones, a customer "takes a number" to mark their place in
>the cashier queue. The cashier then always processes customers from this queue in order
>by number.

>The cashier sits idle while there are no customers in line. When a customer is ready to
>pay, the cashier handles the bill. Once the bill is paid, the customer can leave. The
>cashier should handle the customers according to position in the queue. Once all
>customers have paid, the cashier is finished.

To understand the solution, you can watch this lecture video 

{{< youtube ynwh5O3jVRM >}}


I ported this to a java version, here is the code:

*Main.java*

{{< highlight java >}}
package com.company;

import java.util.concurrent.Semaphore;
import java.util.concurrent.ThreadLocalRandom;

public class Main {
    // total number of cones that all customers need
    private static int totalCones;

    public static final int NUM_CUSTOMERS = 10;

    // stuff for customer queuing to pay
    public static final Semaphore lineLock = new Semaphore(1);
    public static int nextPlaceInLine = 0;
    public static Semaphore lineCustomerReady = new Semaphore(0);
    public static Semaphore[] lineCustomers = new Semaphore[NUM_CUSTOMERS];

    // stuff for manager inspecting cones made by clerks
    public static Semaphore inspectionAvailable = new Semaphore(1);
    public static Semaphore inspectionRequested = new Semaphore(1);
    public static Semaphore inspectionFinished = new Semaphore(1);
    public static boolean inspectionPassed;


    public static void main(String[] args) throws InterruptedException {

        for (int i = 0; i < NUM_CUSTOMERS; i++) {
            lineCustomers[i] = new Semaphore(1);
        }

        for (int i = 0; i < NUM_CUSTOMERS; i++) {
            int numCones = ThreadLocalRandom.current().nextInt(1, 5);
            System.out.println("Customer " + i + " takes " + numCones + " cones");
            new Customer(numCones).start();
            totalCones += numCones;
        }

        System.out.println("========= Total number of cones " + totalCones + "==========");

        Cashier cashier = new Cashier();
        cashier.start();

        new Manager(totalCones).start();

        cashier.join();
    }
}

{{< /highlight >}}

*Manager.java*

{{< highlight java >}}
package com.company;

import java.util.concurrent.ThreadLocalRandom;

public class Manager extends Thread {

    private int totalNeeded;

    private int numPerfect = 0;

    Manager(int totalNeeded) {
        this.totalNeeded = totalNeeded;
    }

    @Override
    public void run() {
        while (numPerfect < totalNeeded) {
            try {
                Main.inspectionRequested.acquire();
                Main.inspectionPassed = inspectCone();
                if (Main.inspectionPassed) {
                    numPerfect++;
                    System.out.println("[MANAGER] passed a cone");
                } else {
                    System.out.println("[MANAGER] failed a cone");
                }
                Main.inspectionFinished.release();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        System.out.println("[MANAGER] goes home");
    }

    private boolean inspectCone() throws InterruptedException {
        Thread.sleep(ThreadLocalRandom.current().nextInt(1000, 2000));
        return Math.random() < 0.5;
    }
}

{{< /highlight >}}

*Clerk.java*

{{< highlight java >}}

package com.company;

import java.util.concurrent.Semaphore;
import java.util.concurrent.ThreadLocalRandom;

public class Clerk extends Thread {

    private Semaphore done;

    Clerk(Semaphore done) {
        this.done = done;
    }

    @Override
    public void run() {
        boolean passed = false;
        while (!passed) {
            try {
                makeCone();
                Main.inspectionAvailable.acquire();
                Main.inspectionRequested.release();
                Main.inspectionFinished.acquire();
                passed = Main.inspectionPassed;
                Main.inspectionAvailable.release();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        done.release();
    }

    private void makeCone() throws InterruptedException {
        Thread.sleep(ThreadLocalRandom.current().nextInt(1000, 2000));
        System.out.println("[CLERK] makes a cone");
    }
}
{{< /highlight >}}

*Cashier.java*

{{< highlight java >}}
package com.company;

import java.util.concurrent.ThreadLocalRandom;

public class Cashier extends Thread {

    @Override
    public void run() {
        for (int i = 0; i < Main.NUM_CUSTOMERS; i++) {
            try {
                Main.lineCustomerReady.acquire();
                checkout(i);
                Main.lineCustomers[i].release();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        System.out.println("[CASHIER] goes home");
    }

    private void checkout(int i) throws InterruptedException {
        Thread.sleep(ThreadLocalRandom.current().nextInt(1000, 5000));
        System.out.println("[CASHIER] checks out customer " + i);
    }
}
{{< /highlight >}}

*Customer.java*

{{< highlight java >}}

package com.company;

import java.util.concurrent.Semaphore;
import java.util.concurrent.ThreadLocalRandom;

public class Customer extends Thread {

    private int numConesWanted;

    Customer(int numConesWanted) {
        this.numConesWanted = numConesWanted;
    }

    @Override
    public void run() {
        Semaphore clerksDone = new Semaphore(0);

        for (int i = 0; i < numConesWanted; i++) {
            new Clerk(clerksDone).start();
        }

        try {
            browse();
            clerksDone.acquire(numConesWanted);
            // get the a number
            Main.lineLock.acquire();
            int myPlace = Main.nextPlaceInLine++;
            Main.lineLock.release();

            System.out.println("[CUSTOMER] " + myPlace + " goes in line");

            // notify cashier ready
            Main.lineCustomerReady.release();

            // wait for cashier to finish
            Main.lineCustomers[myPlace].acquire();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    private void browse() throws InterruptedException {
        Thread.sleep(ThreadLocalRandom.current().nextInt(0, 2000));
    }
}
{{< /highlight >}}

My question is that can this kind of complex concurrency problem be modeled by something like RxJava?
