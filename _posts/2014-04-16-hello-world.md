---
fullview: false
layout: post
title: Hello World!
permalink: hello-world
categories: []
tags: []
---

I have been wanting to start a blog for a while... and it's finally happening! In fact, I'm currently working on my first post about CPython string interning and I hope to publish it in a few days.

Meanwhile, I will leave you with two neat Python tricks I discovered last year in Raymond Hettinger's PyCon talk: ["Transforming Code into Beautiful, Idiomatic Python"](https://www.youtube.com/watch?v=OSGv2VnC0go).

<!--more-->

#### The `ignored` context manager

Suppose you want to create a directory, but this directory might already exist:

{% highlight python %}
import os

try:
    os.makedirs('/foo/bar')
except OSError:
    pass
{% endhighlight %}

Now, let's define the `ignored` context manager as follows:

{% highlight python %}
from contextlib import contextmanager

@contextmanager
def ignored(*exceptions):
    try:
        yield
    except exceptions:
        pass
{% endhighlight %}

Finally, this allows you to write:

{% highlight python %}
import os

with ignored(OSError):
    os.makedirs('/foo/bar')
{% endhighlight %}

__Nota bene__: this is probably not the best example since you can handle this exception better by doing the following:

{% highlight python %}
import errno
import os

try:
    os.makedirs('/foo/bar')
except OSError as e:
    if e.errno != errno.EEXIST:
        raise e
{% endhighlight %}

Still, I am sure you will find appropriate use cases for this context manager.

#### Read N bytes at a time from a buffer

This time, suppose you want to read 1024 bytes at a time from a file:

{% highlight python %}

with open('/foo/bar/qux') as f:
    block = f.read(1024)
    
    while block:
        # do something with block
        block = f.read(1024)
{% endhighlight %}

Instead, Raymond suggests:

{% highlight python %}
from functools import partial

with open('/foo/bar/qux') as f:
    for block in iter(partial(f.read, 1024), ''):
        # do something with block
{% endhighlight %}

I guess this can be extended to many other uses, be creative!
