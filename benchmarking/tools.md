# These are the benchmark libraries that uses for benchmarking Deku

## Micro-benchmark vs Macro-benchmark vs Profiling

- Micro: benchmarking OCaml functions of some parameter(s) representing the typical size of the problem (for instance, size of an array to iterate on, number of iteration of a loop, etc.)

- Macro: benchmarking whole program, the aim of marco-benchmarking is to measure the performance of the particular compiler that generated it. Macro-benchmarking do generally not have a parameter.

- Profiling: As macro benchmarking, profiling help to measure the performance of program. 

---
## Micro benchmark: core_bench

https://github.com/janestreet/core_bench
https://blog.janestreet.com/core_bench-micro-benchmarking-for-ocaml/

--- 

## Profiling

There are many ways of profiling OCaml code.

Dynamic analysis vs. Static analysis
- Dynamic analysis: observe an executing program and take live measurements. Dynamic profiler operate in a variety of ways: some by interjecting counting code into the program, other take samples of its activity at a high-frequency, and others run the program in a simulated environment with built-in monitoring.
- Static analysis: examines the source and predicts behavior.

### perf record profiling

https://www.brendangregg.com/perf.html

`perf` is a statistical profiler on Linux that can profile a binary without needing instrumentation

Pros:
- it is fast as it samples the running program
- the cli can be very quick way to getting a callchain to start from a big program

Limitations:
- sometimes can get strange annotations
- it is statistical so you don't get full coverage or call counts
- the OCaml runtime functions can be confusing if you are not familiar with them

### Visualize the perf.data

#### Flamegraphs

https://github.com/brendangregg/FlameGraph

#### Speedscope

https://www.speedscope.app/

#### Hotspot

https://github.com/KDAB/hotspot

### Valgrind callgrind
Valgrind Callgrind is a program that can profile your code and report on its resources usage (where "resource" is memory, CPU cycles, network bandwidth, and so on). It will helps to detect memory issues. The Valgrind framework support a variety of runtime analysis tools, including:
- `memcheck`: detects memory erros/leaks, 
- `massif`: reports on heap usage, 
- `helgrind`: detects multithreaded race conditions, and
- `callgrind/cachegrind`: profile CPU/cache performance.

The Valgrind profiling tools are `cachegrind` and `callgrind`. 
- `cachegrind`: simulates the L1/L2 caches and counts cache misses/hits
- `callgrind`: counts function calls and the CPU instructions executed within each call and builds a function `callgraph`. `callgrind` includes a cache simulation feature adopted from `cachegrind`. So you can use `callgrind` for both CPU and cache profiling. The `callgrind` tool works by instrumenting your program with extra instructions that record activity and keep counters.

https://web.stanford.edu/class/archive/cs/cs107/cs107.1196/resources/callgrind


Pros:
- the function calls counts are accurate
- the instruction count information is very accurate
- you can instrument all the way to basic block instructions and jumps taken
- `kcachegrind` gives you a graphical front end which can sometimes be easier to navigate
- with the `fn-skip` options you can loop through the `caml_c_call` to get a callchain from OCaml through underlying libraries (including the OCaml runtime)

Limitations:
- quite slow to run
- not supported out of the box; right now to get the function skip you will need to compile your own `valgrind` 
- recursive functions give confusing total function cost scores which should be ignored
- the OCaml runtime functions can be confusing if you are not familiar with them


### Valgrind memcheck
https://web.stanford.edu/class/archive/cs/cs107/cs107.1196/resources/valgrind

It is a tool that detects memory leaks and memory errors. 

#### Memory Erros vs. memory leaks

Memory leaks: When a program dynamically allocates memory and forgets to later free it, it creates a leak. A memory leak generally won't cause a program to misbehave, crash, or give wrong answers, and is not an urgent situation.

Memory error: on the other hand, is a red alert. Reading unitialized memory, writing past the end of a piece of memory, accessing freed memory, and other memory errors can have significant consequences. Memory errors should never be treated casually or ignored. 

### OCaml landmarks
`landmarks` allow you to instrument at the OCaml level and gives visibility into call chains, call counts and memory allocations in blocks.

There are couple of ways to instrument a program: manually, using manual ppx extentions and fully automatic using a ppx mode.

Pros:
- it is very quick to setup automatic profiling and then determine which functions are the heavy hitters
- it is easy to selectively profile bits of OCaml code you are interested in

Limitations:
- the timing information includes the overhead of any landmarks within another landmark
- the allocation information includes the overhead of any landmarks within another landmark
- the addition of the landmark will change the compiled code and can remove inlining opportunities that would occur in a release binary
- can slown down your binary in some cases

https://github.com/LexiFi/landmarks

### ocaml_benchmark (marco-benchmark)

Benchmark measure/compare run-time of OCaml functions. This library provides functions to measure and compare the run-time of functions. It is inspired by the Perf module of the same name.

https://chris00.github.io/ocaml-benchmark/doc/benchmark/Benchmark/index.html

### Magic-trace
`magic-trace` collects and displays high-resolution traces of what a process is doing.

- figure out why an application running in production handles some requests slowly while simutaneously handling a sea of uninteresting requrest,
- look at what the code is actually doing instead of what we think it is doing
- get a history of what the application was doing before it crashed, instead of mere stacktrace at the final instance,
- ...

`magic-trace`:
- has 2%-10% overhead
- does not require qpplication changes to use
- trace every function call with ~40ns resolution, and
- renders a timeline of call stacks going back (a configurable) ~10ms.

`magic-trace` uses Intel Processor Trace to snapshot a ring buffer to all control flow leading up to a chosen point in time.

https://github.com/janestreet/magic-trace/wiki


---
# Real Word OCaml documentation

## Chapter 23: Memory representation of values: https://dev.realworldocaml.org/runtime-memory-layout.html
- Why do OCaml types disappear at runtime: https://dev.realworldocaml.org/runtime-memory-layout.html#why-do-ocaml-types-disappear-at-runtime


## Chapter 24: Understanding the Garbage collector:
https://dev.realworldocaml.org/garbage-collector.html


## Chapter 26: The compiler backend: bytecode and native code
https://dev.realworldocaml.org/compiler-backend.html


Summarizing the File extensions: https://dev.realworldocaml.org/compiler-backend.html#summarizing-the-file-extensions

