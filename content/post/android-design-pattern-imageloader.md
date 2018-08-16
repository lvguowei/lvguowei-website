+++
author = "Guowei Lv"
categories = ["Android Development"]
description = "Develop a image loader guided by design patterns"
featured = ""
featuredpath = "/img"
title = "Android Design Pattern - Build an Image Loader"
date = 2018-08-16T12:29:18+03:00
draft = true
+++

[SOLID Principles](https://en.wikipedia.org/wiki/SOLID) and **Design Patterns** play an important role in Android development.

Lets take a look at how to design and implement an image loader step by step. This example comes from the book *Android Source Code Design Patterns - Analysis and Practice*. I also rewrote all the code from Java to Kotlin and made some bug fixes.

{{< figure src="/img/android-source-design-patterns.jpg" >}}


# Step 1: Make it work

I prefer to write working code first before diving too deep into advanced design patterns.

Let's start simple. Our first version of image loader will just use in memory cache to cache images loaded from the Internet, we will ignore cache validation for now.

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


