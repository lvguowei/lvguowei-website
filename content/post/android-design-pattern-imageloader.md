+++
author = "Guowei Lv"
categories = ["Android Development"]
keywords = ["Android Development", "Image Cache", "Jake Wharton", "DiskLruCache", "Solid Principles"]
description = "Develop a image loader guided by design patterns"
featured = ""
featuredpath = "/img"
title = "Android Design Pattern - Build a SOLID Image Loader"
date = 2018-08-16T12:29:18+03:00
draft = false
+++

[SOLID Principles](https://en.wikipedia.org/wiki/SOLID) and **Design Patterns** play an important role in Android development.

Lets take a look at how to design and implement an image loader step by step. This example comes from the book *Android Source Code Design Patterns - Analysis and Practice*. I also rewrote all the code from Java to Kotlin and made some bug fixes.

{{< figure src="/img/android-source-design-patterns.jpg" >}}


# Step 1: Make it work

>Requirement: Our first version of image loader will just use in-memory cache to cache images loaded from the Internet, we will ignore cache validation for now.

I prefer to write working code first before diving too deep into advanced design patterns. So let's put everything together.

{{< highlight kotlin >}}
package com.example.lv.imageloader

import android.graphics.Bitmap
import android.graphics.BitmapFactory
import android.os.Handler
import android.os.Looper
import android.util.LruCache
import android.widget.ImageView
import java.net.HttpURLConnection
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

object ImageLoader {
    private var imageCache: LruCache<String, Bitmap>
    private var executorService: ExecutorService = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors())
    private val uiHandler: Handler = Handler(Looper.getMainLooper())

    init {
        val maxMemory: Long = Runtime.getRuntime().maxMemory() / 1024
        val cacheSize: Int = (maxMemory / 4).toInt()

        imageCache = object : LruCache<String, Bitmap>(cacheSize) {
            override fun sizeOf(key: String?, bitmap: Bitmap?): Int {
                return (bitmap?.rowBytes ?: 0) * (bitmap?.height ?: 0) / 1024
            }
        }
    }

    fun displayImage(url: String, imageView: ImageView) {
        val cached = imageCache.get(url)
        if (cached != null) {
            updateImageView(imageView, cached)
            return
        }

        imageView.tag = url
        executorService.submit {
            val bitmap: Bitmap? = downloadImage(url)
            if (bitmap != null) {
                if (imageView.tag == url) {
                    updateImageView(imageView, bitmap)
                }
                imageCache.put(url, bitmap)
            }
        }
    }

    private fun updateImageView(imageView: ImageView, bitmap: Bitmap) {
        uiHandler.post {
            imageView.setImageBitmap(bitmap)
        }
    }

    private fun downloadImage(url: String): Bitmap? {
        var bitmap: Bitmap? = null
        try {
            val url = URL(url)
            val conn: HttpURLConnection = url.openConnection() as HttpURLConnection
            bitmap = BitmapFactory.decodeStream(conn.inputStream)
            conn.disconnect()
        } catch (e: Exception) {
            e.printStackTrace()
        }

        return bitmap
    }
}
{{< /highlight >}}

The `ImageLoader` class only has one public method, `displayImage`. It is very easy to use and understand though it has some potential problems. This class is clearly doing several things: **download images**, **display images**, **image caching** and **overall logic** that manipulates the previous tasks.

Based on the **Simple Responsibility Principle**, this is not good. But at this moment, we still have too little information to do the refactoring confidently.

# Step 2: Double Caches

>Requirement: Add a disk cache so that when loading images, first use the in-memory cache, if not found then go the disk cache, if still not found then go the Internet.

Now we have the new requirement at hand, we have a better idea what to do next.

First, let's create a interface for all kinds of caches.

{{< highlight kotlin >}}

interface ImageCache {
    fun put(url: String, bitmap: Bitmap)
    fun get(url: String): Bitmap?
    fun clear()
}

{{< /highlight >}}

Notice that we also add a method to clear the cache.

Now we can implement a `DiskCache` that cache downloaded images into the cache directory on Android.
{{< highlight kotlin >}}

class DiskCache(context: Context) : ImageCache {
    private var cache: DiskLruCache = DiskLruCache.open(context.cacheDir, 1, 1, 10 * 1024 * 1024)

    override fun get(url: String): Bitmap? {
        val key = url.md5()
        val snapshot: Snapshot? = cache.get(key)
        return if (snapshot != null) {
            val inputStream: InputStream = snapshot.getInputStream(0)
            val buffIn = BufferedInputStream(inputStream, 8 * 1024)
            BitmapFactory.decodeStream(buffIn)
        } else {
            null
        }
    }

    override fun put(url: String, bitmap: Bitmap) {
        val key: String = url.md5()
        var editor: DiskLruCache.Editor? = null
        try {
            editor = cache.edit(key)
            if (editor == null) {
                return
            }
            if (writeBitmapToFile(bitmap, editor)) {
                cache.flush()
                editor.commit()
            } else {
                editor.abort()
            }
        } catch (e: IOException) {
            try {
                editor?.abort()
            } catch (ignored: IOException) {
            }
        }
    }

    override fun clear() {
        cache.delete()
    }

    private fun writeBitmapToFile(bitmap: Bitmap, editor: DiskLruCache.Editor): Boolean {
        var out: OutputStream? = null
        try {
            out = BufferedOutputStream(editor.newOutputStream(0), 8 * 1024)
            return bitmap.compress(Bitmap.CompressFormat.PNG, 100, out)
        } finally {
            out?.close()
        }
    }
}

{{< /highlight >}}

We uses the [DiskLruCache by Jake Wharton](https://github.com/JakeWharton/DiskLruCache) to do the heavy lifting for us internally.

There are some changes that have to be made to the `MemoryCache ` to be compliant with the `ImageCache` interface.

{{< highlight kotlin >}}

class MemoryCache : ImageCache {
    private val cache: LruCache<String, Bitmap>
    init {
        val maxMemory: Long = Runtime.getRuntime().maxMemory() / 1024
        val cacheSize: Int = (maxMemory / 4).toInt()

        cache = object : LruCache<String, Bitmap>(cacheSize) {
            override fun sizeOf(key: String?, bitmap: Bitmap?): Int {
                return (bitmap?.rowBytes ?: 0) * (bitmap?.height ?: 0) / 1024
            }
        }
    }

    override fun put(url: String, bitmap: Bitmap) {
        cache.put(url, bitmap)
    }

    override fun get(url: String): Bitmap? {
        return cache.get(url)
    }

    override fun clear() {
        cache.evictAll()
    }
}

{{< /highlight >}}

Now comes the interesting part: implement a `DoubleCache` that uses both `MemoryCache` and `DiskCache`.

{{< highlight kotlin >}}

class DoubleCache(context: Context) : ImageCache {

    private val memCache = MemoryCache()
    private val diskCache = DiskCache(context)

    override fun get(url: String): Bitmap? {
        return memCache.get(url) ?: diskCache.get(url)
    }

    override fun put(url: String, bitmap: Bitmap) {
        memCache.put(url, bitmap)
        diskCache.put(url, bitmap)
    }

    override fun clear() {
        memCache.clear()
        diskCache.clear()
    }
}

{{< /highlight >}}

The new `ImageLoader` now becomes:

{{< highlight kotlin >}}

object ImageLoader2 {
    private lateinit var cache: ImageCache
    private var executorService: ExecutorService = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors())
    private val uiHandler: Handler = Handler(Looper.getMainLooper())

    fun setCache(cache: ImageCache) {
        this.cache = cache
    }

    fun displayImage(url: String, imageView: ImageView) {
        val cached = cache.get(url)
        if (cached != null) {
            updateImageView(imageView, cached)
            return
        }
        imageView.tag = url
        executorService.submit {
            val bitmap: Bitmap? = downloadImage(url)
            if (bitmap != null) {
                if (imageView.tag == url) {
                    updateImageView(imageView, bitmap)
                }
                cache.put(url, bitmap)
            }
        }
    }

    fun clearCache() {
        this.cache.clear()
    }

    private fun updateImageView(imageView: ImageView, bitmap: Bitmap) {
        uiHandler.post {
            imageView.setImageBitmap(bitmap)
        }
    }

    private fun downloadImage(url: String): Bitmap? {
        var bitmap: Bitmap? = null
        try {
            val url = URL(url)
            val conn: HttpURLConnection = url.openConnection() as HttpURLConnection
            bitmap = BitmapFactory.decodeStream(conn.inputStream)
            conn.disconnect()
        } catch (e: Exception) {
            e.printStackTrace()
        }
        return bitmap
    }
}

{{< /highlight >}}

I also wrote a demo client code to use our `ImageLoader2`:

{{< highlight kotlin >}}

class MainActivity : AppCompatActivity() {
    lateinit var imageView: ImageView
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        imageView = findViewById(R.id.imageView)
        ImageLoader2.setCache(DoubleCache(applicationContext))
    }

    override fun onResume() {
        super.onResume()
        ImageLoader2.displayImage("https://picsum.photos/200/300", imageView)
    }

    override fun onDestroy() {
        super.onDestroy()
        ImageLoader2.clearCache()
    }
}
{{< /highlight >}}

# SOLID Principles

Now let's look at the SOLID principles and how our new `ImageLoader` follows it.

### Single Responsibility Principle

Now that we have `ImageCache` that only handle image caches.

### Open Close Principle

If now we need to add new types of caches, we just need to write new code (implement another `ImageCache`) instead of modifying existing `ImageLoader` class.

### Liskov Substitution Principle

All the `ImageCache` can be interchangeably used in the `ImageLoader` class.

### Interface Segregation Principle

The `ImageCache` has only 3 relevant interface methods that the `ImageLoader` needs to know.

### Dependency Inversion Principle

The `ImageLoader` now depends on the abstract `ImageCache` interface. And the different concrete image cache implementations also only depends on the `ImageCache` interface. And there is no dependency between the `ImageLoader` class and all `ImageCache`implementations.

# Conclusion

This is by no means a production ready library, but the aim is to how to use SOLID Principles to guide our design.
