---
layout: post
title: Debugging Segfaults in Multithreaded Tensorflow
description: How to nail down this mysterious one-liner error
tags:
- name: tensorflow
- name: multithreading
- name: concurrency
- name: debugging
category: tech
permalink: /blog/:title.html
---
```
Segmentation fault (core dumped)
```

This **one-liner error** (and no traceback) was all I got while my Tensorflow model crashed in the middle of the run.


This error left me in the dark as all the Stackoverflow solutions didn't fix it. Furthermore, debugging errors from multithreading or tensorflow alone is already tricky; unknown error from any possible combination of those two could be painful and almost impossible to debug.

I fixed this error somewhat less painfully than I expected with these **four different debuggers**.

## 1. Faulthandler
[Faulthandler](https://docs.python.org/dev/library/faulthandler.html) was the debugger that helped me the most. This is a module that dumps Python tracebacks explicitly. The most helpful feature is that it catches and prints tracebacks of **all the threads** when the error occurs, thus possibly .

### How to use
Debugging with `faulthandler` is very easy. You can import `faulthandler` and enable it within your script or run it in CLI.

#### 1. Within the script
```python
import faulthandler

def main():
    faulthandler.enable()
```
#### 2. CLI
```
python -X faulthandler {... the rest of the command ...}
```

### Outputs
![_config.yml]({{ site.baseurl }}/img/posts/faulthandler_logs.png)
(*Note: Some of the error log is redacted for privacy purposes*)

I ran the program several times with this debugger and found out that there was a thread that had the same traceback throughout the runs. It turned out that the error was happening at totally unexpected lines! THis debugger helped me nail down **which thread and which line** the error is thrown from.

*side note :  I wondered why `faulthandler` is not run as default in Python since it is so useful, fast and easy to use. But then I came across this [stackoverflow post](https://stackoverflow.com/questions/21733856/python-is-there-a-downside-to-using-faulthandler). Quote on quote: It is not safe to use faulthandler in case a file descriptor stored by faulthandler is replaced*

### Documentation
[https://docs.python.org/dev/library/faulthandler.html](https://docs.python.org/dev/library/faulthandler.html)


## 2. /usr/bin/time
to be continued
