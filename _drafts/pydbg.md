---
layout: post
title: A basic python debugger
---

As a learning exercise I set myself the goal of building a basic debugger in Python. My hope was to deliver on the
following functionality:

* Step in, over and out
* Break at line number
* Continue execution

This post will focus on getting the basic stepping through code functionality working. If you want to skip ahead and see
the finished project checkout this [repository.](https://github.com/davidkdickson/pydbg)

## Setting a trace function
As I began to develop my solution I was pleased to identify `sys.settrace(tracefunc)` a function within the `sys` module
that sets the system’s trace function. The `tracefunc` callback you set will be triggered by the python virtual machine
every time it enters or exits a function, processes a line of code or runs into an exception. Each of these callbacks
also give full access to the current stack frame and code. Thus one way to implement stepping through source code would
be to set this callback to my own implementation.

Below shows an example implementation thats sets such a callback and steps through a recursive fibonacci implementation.

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

In this code once the breakpoint is set all subsequent calls will invoke the trace function which in turn calls
`print_source` to output the current line of source code being executed. Notice that `trace_calls` returns a reference
to itself. This is because `settrace` causes the set trace function to be invoked whenever a new local scope is entered
is expected to return a reference to whatever function should be used for further tracing in that new scope. In the
example above it was sufficient to use the same tracing function. If you return None you turn off tracing for that new
scope.

## Conclusion
`settrace` allows for incredible introspection from within a program and really makes it very simple to build out a
basic debugger. It was fun exploring how this function worked and helped improve my understanding of the Python runtime.
When I compare what it took me to build similar functionality in [C++](https://github.com/davidkdickson/basicdbg) it was
like night and day in terms of complexity and effort.


