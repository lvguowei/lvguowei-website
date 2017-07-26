+++
title = "Android Kata View Property Animator"
date = "2017-03-28T20:49:08+03:00"
tags = ["programming kata", "android"]
categories = ["Android Development"]
keywords = ["Android", "kata"]
description= "Android coding kata"
featured= "featured-android-kata.png"
featuredalt= "Android coding kata"
featuredpath= "/img"
+++

Since I have been doing programming kata, why not adopt the same kata concept in Android programming?

Over the years I have accumulated some useful tools / tricks that I can show in kata form.

The goal is to keep it simple and easy to grasp and to the point.

This is the first one, which shows how to use [ViewPropertyAnimator](https://developer.android.com/reference/android/view/ViewPropertyAnimator.html) to animate show and hide of the FAB button.

Here is how the end result looks.

{{< figure src="/img/animate-fab.gif" >}}

Things to note:

1. The properties are animated simultaneously.
2. Use the App Compat version to better support older android versions.

Now the code:

{{< highlight java >}}
public class MainActivity extends AppCompatActivity {

    private FloatingActionButton fab;

    private boolean isVisible = true;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        fab = (FloatingActionButton) findViewById(R.id.fab);
        Button animateButton = (Button) findViewById(R.id.animate);

        animateButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (isVisible) {
                    animateHide();
                } else {
                    animateShow();
                }
            }
        });

        setSupportActionBar(toolbar);
    }

    private void animateShow() {
        ViewCompat.animate(fab)
                .alpha(1f)
                .scaleX(1f)
                .scaleY(1f)
                .translationZ(30f)
                .setDuration(800)
                .setInterpolator(new OvershootInterpolator())
                .setListener(new ViewPropertyAnimatorListener() {
                    @Override
                    public void onAnimationStart(View view) {
                    }

                    @Override
                    public void onAnimationEnd(View view) {
                        isVisible = true;
                    }

                    @Override
                    public void onAnimationCancel(View view) {
                    }
                })
                .start();
    }

    private void animateHide() {
        ViewCompat.animate(fab)
                .alpha(0f)
                .scaleX(0f)
                .scaleY(0f)
                .translationZ(1f)
                .setDuration(800)
                .setInterpolator(new AnticipateOvershootInterpolator())
                .setListener(new ViewPropertyAnimatorListener() {
                    @Override
                    public void onAnimationStart(View view) {
                    }

                    @Override
                    public void onAnimationEnd(View view) {
                        isVisible = false;
                    }

                    @Override
                    public void onAnimationCancel(View view) {
                    }
                })
                .start();
    }
}

{{< /highlight >}}

You can also experiment with other Interpolators. Happy coding.

~Fin~

