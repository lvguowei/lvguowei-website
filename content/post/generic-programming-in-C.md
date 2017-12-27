---
categories: ["C Programming"]
description: "How to do generic programming in C"
keywords: ["C Programming", "Generic Programming", "Programming Paradigms"]
featured: "featured-c-programming.jpg"
featuredpath: "/img"
title: "Generic Programming in C"
date: 2017-12-27T21:43:47+02:00
---

Generic programming is an important idea in programming. In languages like Java and C++, it can be done easily. In this post, I show how to do it in plain old C.

We use the classic stack implementation. First, we implement an int version.

This is `stack.h`:

{{< highlight c >}}

typedef struct {
  int *elems;
  int logLength;
  int allocLengh;
} stack;

void StackNew(stack *s);
void StackDispose(stack *s);
void StackPush(stack *s, int value);
int StackPop(stack *s);

{{< /highlight >}}

This is `stack.c`:

{{< highlight c >}}

#include "stack.h"
#include <stdio.h>
#include <stdlib.h>

void StackNew(stack *s) {
  s->logLength = 0;
  s->allocLengh = 4;
  s->elems = (int *)malloc(sizeof(int) * 4);
}

void StackDispose(stack *s) { free(s->elems); }

void StackPush(stack *s, int value) {
  if (s->logLength == s->allocLengh) {
    s->allocLengh *= 2;
    s->elems = (int *)realloc(s->elems, s->allocLengh * sizeof(int));
  }

  s->elems[s->logLength] = value;
  s->logLength++;
}

int StackPop(stack *s) {
  s->logLength--;
  return s->elems[s->logLength];
}

{{< /highlight >}}

Now we make it generic.

Here is `stack_gen.h`:

{{< highlight c >}}

typedef struct {
  void *elems;
  int elemSize;
  int logLength;
  int allocLength;
  void (*freefn)(void *);
} stack;

void StackNew(stack *s, int elemSize, void(*freefn)(void *));
void StackDispose(stack *s);
void StackPush(stack *s, void *elemAddr);
void StackPop(stack *s, void *elemAddr);

{{< /highlight >}}

Here is `stack-gen.c`:

{{< highlight c >}}

#include "stack_gen.h"
#include <assert.h>
#include <stdlib.h>
#include <string.h>

static void StackGrow(stack *s) {
  s->allocLength *= 2;
  s->elems = realloc(s->elems, s->allocLength * s->elemSize);
}

void StackNew(stack *s, int elemSize, void (*freefn)(void *)) {
  s->elemSize = elemSize;
  s->logLength = 0;
  s->allocLength = 4;
  s->elems = malloc(4 * elemSize);
  s->freefn = freefn;
  assert(s->elems != NULL);
}

void StackDispose(stack *s) {
  if (s->freefn != NULL) {
    for (int i = 0; i < s->logLength; ++i) {
      s->freefn((char *)s->elems + i * s->elemSize);
    }
  }
  free(s->elems);
}

void StackPush(stack *s, void *elemAddr) {
  if (s->logLength == s->allocLength) {
    StackGrow(s);
  }
  void *target = (char *)s->elems + s->logLength * s->elemSize;
  memcpy(target, elemAddr, s->elemSize);
  s->logLength++;
}

void StackPop(stack *s, void *elemAddr) {
  s->logLength--;
  void *source = (char *)s->elems + s->logLength * s->elemSize;
  memcpy(elemAddr, source, s->elemSize);
}

{{< /highlight >}}

This is the code from the course *Programming Paradigms* (lecture 5 - 7) by Stanford. This post serves as a bookmark for me and I'm not gonna explain it better than the professor. So if you don't understand, just watch the video, it is good material for learning C.
