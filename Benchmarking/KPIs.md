---
tags: sidechains, benchmark
---

[Linked from](https://hackmd.io/nMexB83cSE6j0NGKhq31og)

# Key Performance Indicators (KPI's) for Benchmarking Deku

Link to the objective hackmd

Deadline: 


## Overview:
Our key performance indicators will be benchmarking Deku's TPS (transactions per second), finding the minimum hardware requirements for running Deku, and benchmarking many of Deku's internal functions, like hashing and validating signatures. 

## Benchmarking TPS
TODO: This is a ton of work, make it clear that this is all not neccesarily feasible at once. But that these are the long-term goals. 

TODO: Explain how to conceptualize TPS
TPS is a measure of throughput and latency for a blockchain. It shows how quickly transactions can make their way into consensus. It is in some sense a measure of the overall load capacity of a blockchain. It is often cited in comparison to other blockchains, or to illustrate the TPS required for various services. For example, Tezos currently has ~60 TPS, Ethereum has ~15, and the credit card provider Visa uses ~1,700 TPS for their services. 

TODO: Get waaayy more specific about the kinds of tasks that we can break this into. i.e. "According to nomadic labs We can try benchmarking in two distinct ways, a gas based benchmark, and a technical based benchmark". If we can take a technical look at each of those posts and figure out what we can reuse, or need to reuse, then we can include that information here. 

By using some of [Daniel's initial work](https://github.com/marigold-dev/deku/pull/539/), and some [guidance](https://research-development.nomadic-labs.com/tps-evaluation.html) provided by Nomadic Labs, we can estimate TPS to learn much Deku can scale Tezos. 

## Internal Benchmarking and Performance Profiling

### Top down (Profiling)

You don't just profile once, you profile across all the kinds of system resources that might be acting as bottlenecks. 

In particular, if we identify a need that none of the libraries that currently exist will satisfy, we may need to develop our own profiling library to target this. 

There are different kind of profiling: the whole program, memory, C heap, etc.  

TODO: Do we actually want a bunch of runtimes? Or do we just want to do performance profiling? -> We want to just do performance, and add benchmarks when that would be helpful. 

TODO: specific tasks that we want to do profiling

- Profiling the Deku node run (the startup script)
- Transfer
    - Normal transfer
    - Transfer with different contracts
- Withdraw
- ...

**This is the first step**
We want to have performance profiling infrastructure in-place so we can **detect what functions** might be slowing things down and then use our benchmarking infrastructure to compare changes that we make to those bottlenecking functions. 

### Bottom-up (Benchmarking)

Micro-bench, macro benchmarking

Benchmarking the internal deku functions will be useful because it gives an initial starting point for performance profiling, and allows for historical comparison of performance. 

Diagnosing bottlenecks in the code will be much easier if we have some idea of what places run relatively quicker or slower. This is why performance infrastructure like `perf` is helpful. 

On the other hand, if a PR gets introduced that slows the codebase down, we can immediately diagnose what's slowing the codebase down by comparing the new runtimes of the codebase to the old runtimes -- Allowing us to isolate the ill-performing function. 


### Benchmarking tools
....
