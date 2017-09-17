+++
categories = ["Android Development"]
description = "Write maintainable RecyclerView in Android"
keywords = ["RecyclerView", "Android", "Maintainable Code"]
featured = "featured-recyclerview.png"
featuredpath = "/img"
featuredalt = ""
title = "How to write maintainable RecyclerView"
date = 2017-09-17T18:39:39+03:00
+++

Google is famous for making complicated things. `RecyclerView` is not an exception. It is more flexible than the previous `ListView` I understand, but that also means that us developers need to understand more and do more work. I have seen gigantic adapters that have very complicated logic especially when there are multiple types that the `RecyclerView` is trying to handle. I think I happen to know one way to organize things a bit better so one can easily find what he/she is looking for. I will demonstrate it by writing an UI for a imaginary chat app.

# Requirement for the Chat App

1. Messages sent by you are on the right side of the screen, and they have certain colors for background and text.
2. Messages sent by the other user are shown on the left side of the screen with certain colors for background and text.
3. In the beginning we show the date and time of the first message.
4. If the gap between two messages are more than an hour, then we show the date and time for the latter message.
5. Click on the message will show a Toast of the message's text.

The end result should look like this:

{{< img-post "/img" "chat-screen.png" "Chat Screen" "center" >}}

# Basic idea

The basic idea is very simple, we have a list of data objects, and we want to show them in a list of views. And somehow we should be able to say this type of data will be shown in this type of view.

# Data objects

I recommend that we create new classes for data that will be shown in the RecyclerView. **Don't just add new logic into existing objects e.g. from api calls**, your future self will thank you.

Let's create a parent class for all types data in the chat screen:

{{< highlight java >}}

public abstract class MessageItem {

    static final int LEFT = 0;
    static final int RIGHT = 1;
    static final int TIME_STAMP = 2;

    public abstract int getType();
}

{{< /highlight >}}

Let's also imagine that we get the following messages from some api call:
{{< highlight java >}}

class MessageDTO {

    final boolean sentByMe;
    final String message;
    long createdAt;

    MessageDTO(String message, boolean sentByMe, long createdAt) {
        this.message = message;
        this.sentByMe = sentByMe;
        this.createdAt = createdAt;

    }
}
{{< /highlight >}}

*Note: DTO stands for Data Transfer Object, meaning it is only designed to use for efficient data transfer, like http api calls, but may not be optimized for your application's business logic, like display in a RecyclerView.*

Now we see that left message and right message are both messages anyway, so let's define a base class for them:

{{< highlight java >}}

abstract class BaseMessageItem extends MessageItem {

    MessageDTO messageDTO;

    BaseMessageItem(MessageDTO messageDTO) {
        this.messageDTO = messageDTO;
    }

    String getMessage() {
        return messageDTO.message;
    }

    long getDate() {
        return messageDTO.createdAt;
    }

}
{{< /highlight >}}

Then classes for left and right messages can inherit from it:

{{< highlight java >}}
class MessageLeftItem extends BaseMessageItem {

    MessageLeftItem(MessageDTO messageDTO) {
        super(messageDTO);
    }

    @Override
    public int getType() {
        return MessageItem.LEFT;
    }
}

{{< /highlight >}}

{{< highlight java >}}

class MessageRightItem extends BaseMessageItem {

    MessageRightItem(MessageDTO messageDTO) {
        super(messageDTO);
    }

    @Override
    public int getType() {
        return MessageItem.RIGHT;
    }
}
{{< /highlight >}}

Then we can define class for time stamp:

{{< highlight java >}}

class MessageDateItem extends MessageItem {

    final long createdAt;

    MessageDateItem(long createdAt) {
        this.createdAt = createdAt;
    }

    @Override
    public int getType() {
        return MessageItem.TIME_STAMP;
    }
}

{{< /highlight >}}

# ViewHolders

Then we can define for each type of data object, its `ViewHolder`.

Firstly, a base `ViewHolder` since RecyclerView requires it:

{{< highlight java >}}
abstract class BaseViewHolder extends RecyclerView.ViewHolder {

    BaseViewHolder(View itemView) {
        super(itemView);
    }

    abstract void bindItem(MessageItem item);
}
{{< /highlight >}}

Note that we have an abstract method for how to populate this ViewHolder with its data object.


Then the ViewHolder for left messages:

{{< highlight java >}}
class MessageLeftViewHolder extends BaseViewHolder {

    TextView messageTextView;

    OnClickListener listener;

    MessageLeftViewHolder(View itemView, OnClickListener listener) {
        super(itemView);
        messageTextView = itemView.findViewById(R.id.message_text);
        this.listener = listener;
    }

    @Override
    void bindItem(MessageItem item) {
        MessageLeftItem messageItem = (MessageLeftItem) item;

        messageTextView.setText(messageItem.getMessage());

        messageTextView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (listener != null) {
                    int pos = getAdapterPosition();
                    if (pos != RecyclerView.NO_POSITION) {
                        listener.onItemClicked(pos);
                    }
                }
            }
        });
    }
}

{{< /highlight >}}

Here we have to pay special attention to the click listener. In order to get the corresponding data from the adapter, we need to call `getAdapterPosition()` and check if it is `NO_POSITION`. If you don't do this, there will be random crashes, ask Google for the reason, I couldn't figure out it by myself and don't really care -_-b

*The important thing here is that we can safely cast the `MessageItem` back to the specific `MessageLeftItem`. This is quite typical in OO design that we temporally lost the type of the object(like get object from database). The easiest way to handle it is to cast it back to whatever type it should be. This type of type casting is not considered a bad thing.*

Similarly, for the right message:

{{< highlight java >}}
class MessageRightViewHolder extends BaseViewHolder {

    TextView messageTextView;

    OnClickListener listener;

    MessageRightViewHolder(View itemView, OnClickListener listener) {
        super(itemView);

        messageTextView = itemView.findViewById(R.id.message_text);
        this.listener = listener;
    }

    @Override
    void bindItem(MessageItem item) {
        MessageRightItem messageItem = (MessageRightItem) item;

        messageTextView.setText(messageItem.getMessage());

        messageTextView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (listener != null) {
                    int pos = getAdapterPosition();
                    if (pos != RecyclerView.NO_POSITION) {
                        listener.onItemClicked(pos);
                    }
                }
            }
        });
    }
}

{{< /highlight >}}

Then we can define ViewHolder for time stamp object:

{{< highlight java>}}
class MessageDateViewHolder extends BaseViewHolder {

    TextView dateTextView;

    MessageDateViewHolder(View itemView) {
        super(itemView);
        dateTextView = itemView.findViewById(R.id.message_date);
    }

    @Override
    void bindItem(MessageItem item) {
        MessageDateItem dateItem = (MessageDateItem) item;

        dateTextView.setText(TimeUtils.convertTime(dateItem.createdAt));
    }
}
{{< /highlight >}}


# The Adapter

The adapter is very simple in this case:

{{< highlight java>}}

class ChatAdapter extends RecyclerView.Adapter<BaseViewHolder> {

    private List<MessageItem> items = new ArrayList<>();

    private OnClickListener listener;

    void setListener(OnClickListener listener) {
        this.listener = listener;
    }

    void setMessages(List<MessageDTO> messages) {
        items.clear();
        if (messages.isEmpty()) return;

        items.add(new MessageDateItem(messages.get(0).createdAt));

        for (int i = 0; i < messages.size(); i++) {
            MessageDTO curr = messages.get(i);
            if (i - 1 > 0) {
                MessageDTO prev = messages.get(i - 1);
                long diff = curr.createdAt - prev.createdAt;
                long diffInHours = TimeUnit.HOURS.convert(diff, TimeUnit.MILLISECONDS);
                if (diffInHours > 1) {
                    items.add(new MessageDateItem(curr.createdAt));
                }
            }
            if (curr.sentByMe) {
                items.add(new MessageRightItem(curr));
            } else {
                items.add(new MessageLeftItem(curr));
            }
        }
    }

    MessageItem getItem(int postion) {
        return items.get(postion);
    }

    @Override
    public BaseViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view;
        switch (viewType) {
            case MessageItem.LEFT:
                view = LayoutInflater.from(parent.getContext()).inflate(R.layout.message_left, parent, false);
                return new MessageLeftViewHolder(view, listener);
            case MessageItem.RIGHT:
                view = LayoutInflater.from(parent.getContext()).inflate(R.layout.message_right, parent, false);
                return new MessageRightViewHolder(view, listener);
            case MessageItem.TIME_STAMP:
                view = LayoutInflater.from(parent.getContext()).inflate(R.layout.message_date, parent, false);
                return new MessageDateViewHolder(view);
            default:
                throw new RuntimeException("Unknown type");
        }
    }

    @Override
    public int getItemViewType(int position) {
        return items.get(position).getType();
    }

    @Override
    public void onBindViewHolder(BaseViewHolder holder, int position) {
        holder.bindItem(items.get(position));
    }

    @Override
    public int getItemCount() {
        return items.size();
    }

    interface OnClickListener {
        void onItemClicked(int position);
    }

}
{{< /highlight >}}

Note that getItemViewType(), onBindViewHolder() and onCreateViewHolder() is very simple. Most of the logic is inside the `ViewHolder`s.

That's all about the important stuff, for the fully working code please check out [here](https://github.com/lvguowei/ChatRecyclerView).
