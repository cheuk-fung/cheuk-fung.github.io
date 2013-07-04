---
layout: blog
title: Find The Bottleneck of Programs with Gprof
location: NKU, China
post: true

excerpt: A brief personal note on profiling.
---

What's Profiling
----------------

> In software engineering, profiling ("program profiling", "software
> profiling") is a form of dynamic program analysis that measures, for
> example, of space (memory) or time complexity of program, the usage of
> particular instructions, or frequency and duration of function calls. The
> most common use of profiling information is to aid program optimization.

<p style="text-align:right">
&#8212; via <a href='http://en.wikipedia.org/wiki/Software_performance_analysis'>Wikipedia</a>
</p>


What's Gprof
------------

Gprof is a performance analyzing tool, aka, profiler, which can display call
graph profile data.


Usage of Gprof
--------------

1. Compile the program using `gcc` with the `-pg` option.
2. Run it and get a file named `gmon.out` containing dynamic call graph and
   profile.
3. Run `gprof ./program` to view the flat profile and call graph with detailed
   explanation of each field.


Visualization
-------------

To visualize the output of `gprof`, the following tools are recommended.

### [Gprof2Dot][] ###
Convert profiling output to a dot graph.

[Gprof2Dot]: https://code.google.com/p/jrfonseca/wiki/Gprof2Dot

### [Graphviz][] ###
A package of open-source tools for drawing graphs specified in DOT language
scripts.

[Graphviz]: http://www.graphviz.org

I moved `gprof2dot.py` to `/usr/bin` as `gprof2dot` for convenient, therefore,
I can generate a png or many other format file by:

    gprof ./program | gprof2dot | dot -Tpng -o a.png

Additionally,

> By default gprof2dot.py generates a partial call graph, excluding nodes and
> edges with little or no impact in the total computation time. If you want
> the full call graph then set a zero threshold for nodes and edges via the -n
> / --node-thres and -e / --edge-thres options, as:

>     gprof2dot.py -n0 -e0


What About Memory Information
-----------------------------

Gprof can't tell us about this. That's why I need [Valgrind][], a suite of
tools for memory debugging, memory leak detection and profiling.

[Valgrind]: http://valgrind.org

Memcheck is the default tool of Valgrind, so, to check memory usage and detect
memory leak, run:

    valgrind ./program

To run the other tools, cachegrind for instance:

    valgrind --tool=cachegrind ./program

And now, it's not a big deal to find the bottleneck of programs.
