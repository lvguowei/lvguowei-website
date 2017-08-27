+++
categories = ["Design and Architecture"]
description = "How Command Pattern has evolved overtime"
featured = "featured-command.png"
featuredpath = "/img"
title = "The Evolution of Command Pattern (II)"
date = 2017-08-27T10:41:19+03:00
+++

In [Part I](https://www.lvguowei.me/post/the-evolution-of-command-pattern/), we discussed the original Command Pattern from the GoF Design Patterns book. In Part II, let's talk about the improved version from another less known book: [Pattern-Oriented Software Architecture Vol.1](https://www.amazon.com/Pattern-Oriented-Software-Architecture-System-Patterns/dp/0471958697/ref=sr_1_1?ie=UTF8&qid=1503820061&sr=8-1&keywords=pattern+oriented+software+architecture) (POSA in short).

{{< img-post "/img" "posa1.jpg" "POSA" "center" >}}

In this book, there is a **Command Processor** pattern, which is based on the Command Pattern in GoF book. The most important difference is the newly introduced `CommandProcessor`.

In the original Command Pattern, it defines how to create `Commands`, and each `Command` has an `execute` and `undo` methods. But it doesn't say anything about how to manage these Commands.

The basic idea of this `CommandProcessor`, is to provide a single place where the commands can be executed and managed.

Now let's add a `CommandProcessor` in the previous example.

Let's say we want to add a universal **Undo** and **Redo** mechanism into the application. To implement this, we need to keep track of all the executed Commands somewhere, `CommandProcessor` seems to be the perfect choice.

First, let's make the actions of **Undo** and **Redo** into Commands as well. Since we don't really want the Undo Command to be able undo itself, we divide all the Commands into two categories, *undoable* and *not undoable*.

{{< highlight java >}}

class UndoCommand extends Command {

    @Override
    void execute() {
        CommandProcessor.instance.undoLastCmd();
    }

    @Override
    void undo() {
    }

    @Override
    boolean undoable() {
        return false;
    }
}

{{< /highlight >}}

{{< highlight java >}}

class RedoCommand extends Command {
    @Override
    void execute() {
        CommandProcessor.instance.redoLastUndone();
    }

    @Override
    void undo() {
    }

    @Override
    boolean undoable() {
        return false;
    }
}

{{< /highlight >}}

Now, let's implement the `CommandProcessor`. To be able to Undo and Redo, we need two stacks. One for storing the done Commands, and one for storing the undone Commands. When the user want to undo the last action, we pop the last Command from the **done stack**, undo it, and put it to the **undone stack**. When the user want to redo the undone command, we pop the last Command from the **undone stack**, and use the CommandProcessor to execute it again, so that it will end up in **done stack** after it is executed.

Here is the code.

{{< highlight java >}}

class CommandProcessor {

    static final CommandProcessor instance = new CommandProcessor();

    private Stack<Command> doneStack;
    private Stack<Command> undoneStack;

    private CommandProcessor() {
        doneStack = new Stack<>();
        undoneStack = new Stack<>();
    }

    void doCommand(Command command) {
        command.execute();
        if (command.undoable()) {
            doneStack.push(command);
        }
    }

    void undoLastCmd() {
        if (doneStack.isEmpty()) return;
        Command lastDoneCmd = doneStack.pop();
        lastDoneCmd.undo();
        undoneStack.push(lastDoneCmd);
    }

    void redoLastUndone() {
        if (undoneStack.isEmpty()) return;
        Command lastUndone = undoneStack.pop();
        doCommand(lastUndone);
    }
}

{{< /highlight >}}

Now in `Main` we can test it out.

{{< highlight java >}}

public class Main {

    public static void main(String[] args) {
        Canvas canvas = new Canvas();
        printCanvas(canvas);

        Command drawCircle = new DrawCircle(canvas);
        Command drawRec = new DrawRectangle(canvas);

        CommandProcessor.instance.doCommand(drawCircle);
        CommandProcessor.instance.doCommand(drawRec);

        printCanvas(canvas);

        Command undoCmd = new UndoCommand();
        CommandProcessor.instance.doCommand(undoCmd);
        printCanvas(canvas);

        Command redoCmd = new RedoCommand();
        CommandProcessor.instance.doCommand(redoCmd);
        printCanvas(canvas);
    }

    private static void printCanvas(Canvas canvas) {
        System.out.println(canvas.toString());
    }
}
{{< /highlight >}}

We can do all sorts of things in the `CommandProcessor`, like logging Commands, or delay the execution of certain Commands, and even execute the Commands in another thread, etc.

That wraps up the Part II, in Part III, we will talk about a more advance variant of this pattern, and how Android uses it to implement its Service component. Stay tuned!
