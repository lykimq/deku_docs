---
tags: sidechains, benchmark
---
[Linked from](https://hackmd.io/nQxZ6nPfTeSy6kbxbvnn7Q)
# Benchmarking define KPIs, TODO's and Notes

NOTE: They don't actually care about the specific numbers. They just want to give us some code and know if it's fast. And if it's not, what we can do to change that. 


* [ ] Write [KPI document](https://hackmd.io/nMexB83cSE6j0NGKhq31og) detailing what we have done so far, and giving analysis on the metrics and information we've gathered (Deadline :bomb: 13/05/2022)
  * [ ] Include additional potential benchmarks and KPI's ([this](https://gitlab.com/tezos/tezos/-/merge_requests/2439/diffs#4b04f01b706691f30f2792537ce904fe08ca3f59), [this](https://github.com/marigold-dev/deku/pull/539/files#diff-39a5f23428cca3f49f16c9a268558a8477ffca412616609e848974c406130d57R115))
  * [ ] What benchmarks (if any!) do we want to include in a continuous integration setup on github? ([janestreet](https://www.youtube.com/watch?v=7AO4wz6gI3Q&t))
    * [ ] ^ will require some sense of standardization, and might go against the entire point of benchmarking. 
    
* [ ] Setup benchmark to run in CI (Deadline :bomb: 30/05/2022) 
  * [ ] Determine what benchmarks will be used for this
  * [ ] Standardization?
  * [ ] Actually implement it in github actions
  * [ ] Should we have a dedicated machine to run our benchmarking? We will get variable results if don't use something standardized. 
  * [ ] We need an extremely clean script to run all the benchmarks. It should be something like one line to run everything and get the output in a usable format. 

* [ ] Run the code and build the KPIs (Deadline :bomb: 1/06/2022) 

* [ ] Have fancy output (graph & script) (Deadline: :bomb: 20/06/2022)
  * [ ] Will our current libraries work for this?
  * [ ] If not, can we modify the libraries to work like this?
  * [ ] If not, is it worth it to build a new benchmarking/perf library to ensure benchmarking goes how we want? (this can be for reference [prepare-benchmarking](https://gist.github.com/Dieterbe/a52c95a9603507670eb39274544ee1a8)).


---
# I. Objectives for benchmarks (KPIs)
We want to have several benchmarks for:

- Define KPI (key performance indicator) => what numbers matters for “marketing” (sub-section TPS)
- Continuous how new features impact the benchmarks (regular reports) (@lykimq: this we can use `current_bench`) (@lykimq: section benchmark libraries)


## I.1 Tooling

TODO: List out the libraries we're using and what they can measure
Reference: [benchmarks libraries use in deku](https://hackmd.io/DE83Pi6WSzq-911XOPSAmA)


## I.2 TPS

- So what is the TPS (marketing/vanity number) for Deku? This is the [end-end smoke test](https://github.com/marigold-dev/deku/pull/539/) of Daniel. (Remarks: we can ask him for more details).
- Benchmarking the TPS by using micro-bench, macro-bench: for example, transfers/deposit/withdraw with different kind of contracts, etc. to see how much time that each one takes? and then compare them. (as in [MR 2439](https://gitlab.com/tezos/tezos/-/merge_requests/2439))

---
## I.3 Benchmarked Deku Functions

### I.3.1 Ledger
Benchmark functions in [`src/deku_core/ledger.mli`](https://github.com/marigold-dev/deku/blob/quyen%40lib_landmarks/src/deku_core/ledger.mli):


| Function | Micro (TBD) | Macro (TBD) |
| -------- | -------- | -------- |
| make ticket |      |      |
| make address |      |      |
| make tezos address |      |      |
| deposit: one ticket, one address|      |      |
| balance: one deposit |      |      |
| transfer 1 |      |      |
| withdraw 1 |      |      |

### I.3.2 Validators

Benchmark functions in [`src/protocol/validators.mli`](https://github.com/marigold-dev/deku/blob/quyen%40lib_landmarks/src/protocol/validators.mli)

| Function | Micro (TBD) | Macro (TBD) |
| -------- | -------- | -------- |
| make validator |      |      |
| setup one validator |      |      |
| current validator |      |      |
| remove validator |      |      |
| hash |      |      |

### I.3.3 Tezos RPC
**Remark**: potential use for finding bottenecks (profiling, etc.) because this is where we are interactive with the Deku node.
TODO: What other functions can we run with tezos RPC (see section "Benchmark the Tezos RPC more fully").
It is a benchmark of functions defined in [`src/tezos_rpc/`](https://github.com/marigold-dev/deku/tree/quyen%40lib_landmarks/src/tezos_rpc)

| Function | Micro (TBD) | Macro (TBD) |
| -------- | -------- | -------- |
| fetch block operations |      |      |
| fetch block header |      |      |



### I.3.4 Patricia
 It is a benchmark for incremental Patricia tree.
 We can test this with a very big tree and then see the result of the benchmarks.
 
 The current bench only take the size of the tree is 10.
 TODO: What if we make the tree **huge**
 
 
| Function | Micro (TBD) | Macro (TBD) |
| -------- | -------- | -------- |
| add and find tree |      |      |
| hash tree |      |      |
| hash values |      |      |

### I.3.5 Tezos internal operations

Benchmark functions in [`tezos_interop`](https://github.com/marigold-dev/deku/tree/quyen%40lib_landmarks/src/tezos_interop)

TODO: The [mligo] functions aren't getting called during the benchmarking. See in [sandbox](https://github.com/marigold-dev/deku/blob/quyen%40lib_landmarks/sandbox.sh#126) on line 126 how the contract gets originated there (see section "Benchmark the consensus, discovery, and other mligo contracts").

| Function | Micro (TBD) | Macro (TBD) |
| -------- | -------- | -------- |
| key: to_string |      |      |
| key: of_string |      |      |
| key: hash |      |      |
| secret: to_string |      |      |
| secret: of_string |      |      |
| verify signature |      |      |
| contract hash: to_string |      |      |
| contract hash: of_string|      |      |
| address: to_string |      |      |
| address: of_string |      |      |
| ticket: to_string |      |      |
| ticket of_string |      |      |
| address: of_string |      |      |
| operation hash: to_string, of_string |      |      |
| forge transaction taquito |      |      |
| forge transaction bytes |      |      |
| pack: int 1 |      |      |
| pack: int -1 |      |      |
| pack: bytes 0x |      |      |
| pack: bytes 050001  |      |      |
| pack: pair: (1, 0x) |      |      |
| pack: (1, (0xAA, -1)) |      |      |
| pack: list empty |      |      |
| pack: list int 1 |      |      |
| pack: list pair |      |      |
| pack: key |      |      |
| pack:key_hash |      |      |
| pack: address implicit |      |      |
| pack: address originated |      |      |
| pack: to_bytes int  |      |      |
| pack: to_bytes bytes |      |      |
| pack: to_bytes list |      |      |
| pack: to_bytes pair|      |      |
| pack: to_bytes key |      |      |
| pack: to_bytes key_hash |      |      |
| pack: to_bytes address implicit |      |      |
| pack: to_bytes address originated |      |      |
| [mligo] consensus: address_exn |      |      |
| [mligo] consensus: key_hash_exn |      |      |
| [mligo] consensus: hash_validators |      |      |
| [mligo] consensus: hash_block |      |      |
| [mligo] consensus: hash_withdraw_handle |      |      |
| [mligo] discovery |      |      |

**Remarks**: There are several ligo contracts in this folder, we may want to get the benchmark for different contracts. 

---
## I.4 Benchmark the consensus, discovery, and other mligo contracts
**Remark**: This is a future goal, and cannot be achieved on our current timeline 

In the folder for [tezos_interop](https://github.com/marigold-dev/deku/tree/quyen%40lib_landmarks/src/tezos_interop), there are mligo contracts like [`discovery`](https://github.com/marigold-dev/deku/blob/quyen%40lib_landmarks/src/tezos_interop/discovery.mligo) and [`consensus`](https://github.com/marigold-dev/deku/blob/quyen%40lib_landmarks/src/tezos_interop/consensus.mligo). We should benchmark these. Use the [sandbox](https://github.com/marigold-dev/deku/blob/quyen%40lib_landmarks/sandbox.sh#126) to see how these are called. We can also write larger pieces of OCaml code to call the contracts for benchmarking instead of using such scripts. For reference, look to [PR 539](https://github.com/marigold-dev/deku/pull/539).

---
## I.5 Benchmark the Tezos RPC more fully
**Remark**: This is a future goal, and cannot be achieved on our current timeline 

Currently we're only benchmarking `fetch block operations` and `fetch block header`, but we should also benchmark everything else in [this file](https://github.com/marigold-dev/deku/tree/quyen%40lib_landmarks/src/tezos_rpc).

For example, we have an `execute` function in [`inject_operations.mli`](https://github.com/marigold-dev/deku/blob/quyen%40lib_landmarks/src/tezos_rpc/inject_operations.mli). We should see how long it takes this function to run when we try to execute different amounts of operations. 

---
## I.6 Costly internals 

- What are the costly functions? (@lykimq: this we need to re-rewrite `core_bench` and put it into a `for loop` for specific functions, it is the same with `landmarks` and `ocaml-benchmarks`).
- We can use a library like perf to find bottlenecks in deku, and then use a micro-benchmarking library like `core_bench` to try and optimize them)
 
---
## I.7 Hardware consumption

- Do we have memory leaks? (@lykimq: more research on this).
- What is the minimal hardware / recommanded hardware to run Deku?  (@lykimq: this we have to bench most/all of the functions (micro and marco benchmarks), end-to-end tests for transfer/deposit/withdrawal/etc.) (@swerve: we should probably do this after we do everything else).

---
## I.8 Infra-structure

### I.8.1 Script

### I.8.2 Setup environments to run benchmarks


---

## I.9 Output the results of benchmark

### I.9.1 Graph

Find the way to get the pretty graph output. 

- Getting the profiling for `./sandbox.sh start` (running Deku node by `perf report -g -- ./sandbox.sh start`, and use flamegraph/speedscope.app/hotspot as a viewer).


### I.9.2 Website

Taking the results of different benchmarks, together with graphs to a website.

---
## I.10 How do we interpret the results of benchmarking


---

# II. Benchmarks libraries
These libraries will be added (if we can interate them in Deku) after we have done the Objective #3

These are the benchmarks libraries that we can try to interate:
- current_bench: https://github.com/ocurrent/current-bench
- sandmarks: https://github.com/ocaml-bench/sandmark
- orun: https://github.com/ocaml-bench/orun
- operf-micro: https://www.typerex.org/operf-micro.html (need to see if core_bench is better than this).
- operf-marco: https://www.typerex.org/operf-macro.html
- ppx_bench: https://github.com/janestreet/ppx_bench
- 

---
# III. References
- A note compare different Benchmark libraries in OCaml: https://github.com/ocaml-bench/notes/blob/master/apr19.md
- Profiling notes: https://github.com/ocaml-bench/notes/blob/master/profiling_notes.md
- Benchmarks libraries use in Deku: https://hackmd.io/DE83Pi6WSzq-911XOPSAmA
- Link Objective 3: https://hackmd.io/rIL0NWJ-SVeAetNt9szBVQ#Objective3
- Sidechain benchmarks track: https://hackmd.io/S3S-vaRxSDmyZnJuQV__jw
- Benchmarks libraries in OCaml: https://hackmd.io/hsvHl6xaStuQirUt9dNzlg?view
