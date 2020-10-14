---
layout: post
title: A basic python debugger
---

As a learning exercise I set myself the goal of building a basic debugger in Python. My hope was to deliver on the following functionality:

* Step in, over and out
* Break at line number
* Continue execution

This post will focus on getting the basic stepping through code functionality (step in) working. If you want to skip ahead and see the finished project checkout this [repository.](https://github.com/davidkdickson/pydbg)

## Setting a trace function
As I began to develop my solution I was pleased to identify `sys.settrace(tracefunc)`
a function within the `sys` module that sets the system’s trace function. The
`tracefunc` callback you set will be triggered by the python virtual machine every
time it enters or exits a function, processes a line of code or runs into an exception. Thus one way to implement stepping through source code would be to set this callback to my own implementation.

Below `trace_calls` shows an example implementation of such a callback that is set by
calling `break_point`.

{% highlight python %}
import sys
import inspect

class Pydbg:
    def print_source(self, frame, event, arg):
        (file, line, func, code, idx) = \
                inspect.getframeinfo(frame)
        line = f'{file}:{line}:{func}\n--> {code[idx]}'
        print(line)

    def get_command(self):
        return 's'

    def trace_calls(self, frame, event, arg):
        self.print_source(frame, event, arg)
        self.cmd = self.get_command()

        if self.cmd == 's':
            return self.trace_calls

        self.print_source(frame, event, arg)

        raise 'Unknown command'

    def break_point(self):
        sys.settrace(self.trace_calls)


if __name__ == "__main__":
    pydbg = Pydbg()

    def fibonacci(n):
        if n in [0, 1]:
            return n
        return fibonacci(n - 1) + fibonacci(n - 2)

    pydbg.break_point()
    fibonacci(2)
{% endhighlight %}

Once the breakpoint is set all subsequent calls will invoke the trace function which in turn calls `print_source` that will output the current module name, function, line number and the current line of source code that is executed.

