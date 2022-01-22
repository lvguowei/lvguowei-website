+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Some unexpected behavior of LiveData(Kotlin really)"
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "WTF Livedata?! (or Kotlin)"
date = 2022-01-22T20:31:24+02:00
+++

LiveData is convenient, powerful and super easy to use. But, there are still some quirks that could possibly leads to some heads banging against walls.

# How many times can I observe?

In theory, we can observe livedata with multiple observers, just like the good old pub-sub pattern, right? Well, let's test this out.

Let's create a super simple livedata.


{{< highlight kotlin >}}
class MainViewModel : ViewModel() {
    private val _data = MutableLiveData<Int>()
    val data: LiveData<Int> = _data

    fun update(count: Int) {
        _data.value = count
    }
}
{{< /highlight >}}

Then observe it 10 times, and print some log when the data changes.

{{< highlight kotlin >}}
override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)
        viewModel = ViewModelProvider(this).get(MainViewModel::class.java)

        observe()
        viewModel.update(10)
    }

    /**
     * Observe 10 times, so should get notified 10 times
     */
    private fun observe() {
        (1..10).forEach { _ ->
            viewModel.data.observe(viewLifecycleOwner, object : Observer<Int> {
                override fun onChanged(t: Int?) {
                    Log.d("test", "$t")
                }
            })
        }
    }
{{< /highlight >}}

So we should see 10 log entries, right? Let's run it and ... What?? only 2??!!


{{< highlight kotlin >}}
2022-01-22 20:16:53.877 29723-29723/com.example.livedatakeng D/test: 10
2022-01-22 20:16:53.877 29723-29723/com.example.livedatakeng D/test: 10
{{< /highlight >}}

This is a quite common problem with logging actually, and fortunately this has nothing to do with our livedata.
If the timestamp and log content are the same, then the logging system will simply drop the duplicates and keep only 2...

We can verify this by adding some random number to the log text.

{{< highlight kotlin >}}
Log.d("test", "$t ${Random.nextInt()}")
{{< /highlight >}}

Now we can seee 10 logs:

{{< highlight kotlin >}}
2022-01-22 20:51:27.201 29904-29904/com.example.livedatakeng D/test: 10 -225143864
2022-01-22 20:51:27.201 29904-29904/com.example.livedatakeng D/test: 10 -604955811
2022-01-22 20:51:27.201 29904-29904/com.example.livedatakeng D/test: 10 1850140488
2022-01-22 20:51:27.201 29904-29904/com.example.livedatakeng D/test: 10 -11708792
2022-01-22 20:51:27.202 29904-29904/com.example.livedatakeng D/test: 10 -1384104185
2022-01-22 20:51:27.202 29904-29904/com.example.livedatakeng D/test: 10 -918821627
2022-01-22 20:51:27.202 29904-29904/com.example.livedatakeng D/test: 10 1334570051
2022-01-22 20:51:27.202 29904-29904/com.example.livedatakeng D/test: 10 1356791937
2022-01-22 20:51:27.202 29904-29904/com.example.livedatakeng D/test: 10 -807523911
2022-01-22 20:51:27.203 29904-29904/com.example.livedatakeng D/test: 10 1316473667
{{< /highlight >}}

But, this is not the end of the story ...

# Kotlin lambda problem?

If you try this yourself, you will probably notice that Android Studio suggests to use lambda for creating new Observer objects:

{{< img-post "/img" "kotlin-lambda.png" "Kotlin Lambda" "center" >}}

Sure, why not? just two clicks!

{{< highlight kotlin >}}
private fun observe() {
    (1..10).forEach { _ ->
        viewModel.data.observe(viewLifecycleOwner) { t ->
            Log.d("test", "$t ${Random.nextInt()}")
        }
    }
}
{{< /highlight >}}

Perfect! Now let's run again:

{{< highlight kotlin >}}
2022-01-22 21:28:40.708 30111-30111/com.example.livedatakeng D/test: 10 1434888092
{{< /highlight >}}

What!!?? Only one log now!

Let's decompile to java code and see what is happening:


{{< highlight kotlin >}}
var10000.getData().observe(this.getViewLifecycleOwner(), (Observer)MainFragment$observe$1$1.INSTANCE))
{{< /highlight >}}

We can see that the Observer becomes a single instance. That is to say that we are using the same observer 10 times, no wonder we only receive 1 log print.

Well, you could say that in reality nobody will do this, but there is no harm in knowing more, right?

