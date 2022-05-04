
# KPIs: Key Performance Indicators for benchmarking Deku, more specifically the Tendermint consensus

Prioritization order:

* end to end load testing (including TPS)
* performance profiling
* micro-benchmarking

:bomb: Deadline: 13-May-2022

## Summary of KPIs

KPIs 1: Local

| No. | Name               | Tool     | Status   |
| ----| ------------------ | -------- | -------- |
| 1.1   | [TPS, gas-based, tech-based](https://research-development.nomadic-labs.com/tps-evaluation.html) | prometheus, profiling | TBD (top priority) | 
| 1.2   | Normal transaction | End-end  | TBD      |
| 1.3   | Smart contract call| End-end  | TBD      |
| 2   | Memory consumption | perf, memtrace, core_bench  | TBD      |
| 3   | Minimal block times | End-end  | TBD      |
| 4.1   | State hash function | core_bench, perf  | TBD      |
| 4.2   | State hash with parallel | core_bench, perf  | TBD      |
| 5   | Verify signatures | core_bench, perf  | TBD      |
| 6   | Accounts, tickets | End-end, core_bench  | TBD      |


KPIs 2 : Network
|  No.| Name               | Tool     | Status   |
|-----| ------------------ | -------- | -------- |
| 1.1   | Normal transaction | End-end  | TBD      |
| 1.2   | Smart contract call| End-end  | TBD      |
| 2   | Memory consumption | perf, memtrace, core_bench  | TBD      |
| 3   | Minimal block times | End-end  | TBD      |
| 4.1   | State hash function | core_bench, perf  | TBD      |
| 4.2   | State hash with parallel | core_bench, perf  | TBD      |
| 5   | Verify signatures | core_bench, perf  | TBD      |
| 6   | Accounts, tickets | End-end, core_bench  | TBD      |
| 7   | Validators on different hardware | End-end  | TBD      |

KPIs 3: profiling

| No. | Name               | Tool     | Status   |
| ----| ------------------ | -------- | -------- |
| 1   | Start validator | profiling  | TBD      |
| 2   | Validate operations | profiling  | TBD      |


---

There is a gossip network to communicate between nodes. This is not discussed yet in this document.

There are two environments we need to benchmark:
- Deploy and interact with the Deku chain
- Locally without interacting with the Deku chain

For transactions, there are two kinds of transactions we are taking into account:
- Normal transaction (A sends 10 xtz to B)
- Smart contract call (A call a smart contract C)

Deku run on RAM and reply on hardware to run and store states.

---

# KPIs 1: Locally

A local environment existing on a single machine. We are using this benchmark:

1. Normal transaction: 
- How much does it take to do one transaction? 
- Then we increase the number of transactions? 
- How many operations it is considered as full?
2. Smart contract call: 
- How many transactions we can do?
- How large of a contract can we call? 
- We can meansure it with different smart contract, etc.
3. Memory consumption:
- To determine memory consumption over Deku, we will need to determine how much memory (minimal) is required for transactions and smart contract calls. The smart contract calls will need to tested at varying sizes to see the largest and smallest that things can get.
- For Deku, "minimal hardware" has a high lower-bound. We intentionally want to only support higher-tier cloud computers.
4. Minimal block time: 
- What is the minimal block time? This information reference to a given throughput.
- This will depend on **block gas limits** and **block size**. So we need to know those first before we can understand the minimal block times.

Some of the user perpectives questions:
- Can Deku support my desired throughput?
- Can Deku support my desired latency given that throughput?
- Can Deku support my desired finality at that throughput?

5. How many Gb can we hash the state root hash of a block per second:

    It is the state hash of the final block on Deku. We need to hash it and then use it to communicate with the Tezos chain. 
- How big is this state can be?
- There are probably these variables that will determine:
    + block time, gas limits, and block size; 
    + network bandwidth, and validator speed.

    We should max out these two variables separately in benchmarking to see what their effect on capacities will be. 

5.1. State hashing run without parallel

5.2. State hashing run with parallel

- Parallel hashing means that if we have 1K items to hash, instead of hashing them all serially, we can divide up the work and have 64 (or whatever the number) threads hashing them all at the same time.
- We can emply this hashing to speed up the creation of Merkle roots as well.

At the moment, this is not in the priority. Because currently, the hashing is already done in parallel, just on a single parallel core.
This may not be the bottleneck.

6. For a block to be accepted, it needs enough numbers of signature signed by validators, we can benchmark these signatures (verifying function)
7. Measure about the number of accounts, tickets, etc. Deku can have, this will depend on the size of RAM (hardware), but we can weight to see if one account, or ticket take how much then we can measure this number.

---

# KPIs 2: Deploy with the network

The same items as the local on KPI 1 but with the interactions done on a Deku's chain.

**Additionally**, identify hardware configurations on cloud. Run validators on **different hardware configurations.**

The senario can be:

- 4 validators (base case):
    - hardware configurations: 
        - minimal hardware configuration: find out the minimal hardware configuration that are required for validators to run on the cloud. 
        - Then change to run validators with different hardware and then compare the results.
    - operations: benchmarking to see how many operations (deposits, transfers, withdrawals) it can validate, at what minimal hardware configuration that it can handle for different number of operations. For instance, transfer 10, 1000 , etc. keep on increasing to see requirements.

- Increase the number of validators (growing case): like the base case, but increase the number of validators (for instance: 5, etc. up to 15, 21 because the consensus currently in Deku will not be effected after that).

---

# KPIs 3: Profiling 
- We **should** run profiling during our end to end tests, and during TPS estimation in order to find obvious bottlenecks.
- We **shouldn't** initially run profiling on individual functions. 
- We **should** initially run profiling on actions we would expect a user to take. 

For instance to answer these questions: 
- how long does it take to start a validator
- how long for a validator to:
    - validate operations (deposits, transfers, withdrawals)?
    - if I increase the number of validators how fast or slow is that? 

---





<!--
## KPI 3.3: Snapshot
Remark: is it a good place here? 

    - when we take snapshot? 
    - A snapshot has how many blocks? 
    - how to transfer snapshot to a database? 
    - what is that database to store snapshot? 
    - how fast/slow to call the snapshot? (do we need it?)

-->

<!--
# KP 4: Data structure requirements

TODO: what can I benchmark for this?

`src/node/tendermint_data.ml`

https://github.com/marigold-dev/deku/pull/433 (at section of Data structure requirements)

To work, Tendermint requires specific data structures:
- an `input_log`: that stores the incoming messages a consensus instance receives.
- an `output_log`: that stores the decision made by a consensus instance.


## KP 4.1: Input log
- How many incoming message in input log? `msg_log` holds the consensus message broadcasted to the network. 
- `timeouts` function holds timeouts of the instance.

These functions affect the perform of the operations required in Tendermint.

## KP 4.2: Output log

A decision log: 
- at what height the value that it was decided on
- and the round it was decided on (the round is required to mitigate byzantine threats for instance nodes lying on rounds).

Benchmark the max of this log (height and rounds)
->

<!--
- Functions the validators including the producer and validators signing and verifying signatures in a block and operations.

The validators can vote (accept the operations or not by voting Nil), these vote is like a endorsement of the block. -->

<!--
- How many signatures (validators votes accept) for a block to be accepted?

**Block pool**: the block with enough signatures from validators will be commited to a block pool.

- How big is this block pool? what is it limit?
- How the block priority to call and commit to Deku chain?
- How long a block can stay in this block pool? if after a time (when) it will be deleted? (will it be deleted)? is it as mempool in Tezos?->


<!--
## KP 5.3: Compute the hash of a block

- Hashing block: A block of transactions that the producer will commit to Deku, hash the final state from this block.

- How many Gb can be hash per second (state hashing, how big the state can be hash per second?)-->

---

# Gossip network: KPI benchmarks
It is a separate KPIs.

----
<!--
Note discuss with GA:

- How many Gb can be hash per second (state hashing, how bigger the state can be hash per second?) (the state hash of the final block -> we need to hash it -> then we can use it to the Tezos chain? )

- Internal test without network involve (not deploy the chain): write many tests and make transfers to see how many we can archive. 2 types of transfers: 

    - How many transfer per second can we do? just processing transactions (without sm)

    - Transaction with SM (A calls C)? how many transfer we can do (no network), without deploy the chain (internal tests)

- Minimal Block Time: if remove all the timeout the time between block is how long
  - This will depend on block gas limits and block size. So we need to know those first before we can understand minimal block times.  
- Depoly the chain:

    - TPS (network)

- Deku run on RAM:
    - Memory consumption without networks
    - To determine memory consumption over deku, we will need to determine how much memory is required for transactions and smart contract calls. The smart contract calls will need to be tested at varying sizes to see the largest and smallest that things can get. -->
