
# KPI: Key Performance Indicators for benchmarking Deku, more specifically the Tendermint consensus

**Notes**: These KPIs are **not** ordered by priority

Tendermint achieves consensus with a 3 step process (Proposal, Prevote, Precommit) and works in rounds.

**Definition of a validator**: Among the validators (a validator is a Deku node), there is only one producer (a validator that produces a block), the rest are validators who take the proposed block from the block producer and validate it. when the validators accepted the proposed block they will sign and send it back to the block producer, those signatures are the same as an endorsement would be in Tezo. The block producer then commits the valid block in Deku. 

Number of validators: Tendermint requires at least 4 validators (one producer and 3 validators) to run properly. Currently, it can support at most 15 to 21 validators. 

**Definition of Deku operations**: there are 3 operations: deposits, transfers and withdrawal.

When the smart contract is deposited to Deku, it stores it in a vault (its size limit is the limit of Bigmap of Michelson smart contract). Deku will then forge this operation to Deku's ticket so it will be considered an internal operation, and deku can perform operations on it. These operations are not observed on Tezos' chain, only on Deku's chain.

**Definition of Deku internal operations**: those are ticket operations, transfer ticket, etc. 

There is a gossip network to communicate between nodes. This is not discussed yet in this document.

There are two environments we need to benchmark:
- Deploy and interact with the Deku chain
- Locally without interacting with the Deku chain

For transactions, there are two kinds of transactions we are taking into account:
- Normal transaction (A sends 10 xtz to B)
- Smart contract call (A call a smart contract C)

Deku run on RAM and reply on hardware to run and store states.
    
---

# KPI 1: Locally

A local environment existing on a single machine. We are using this benchmark:

- Normal transaction: how many transactions we can do? how many operations it is considered as full?
- Smart contract call: how many transactions we can do? How large of a contract can we call? etc.
- Memory consumption: what is the minimal memory that we are using to do the normal transfer? smart contract call? etc. (Deku run on RAM)
- Minimal block time: what is the minimal block time?
- How many Gb can we hash per second (it is a state hashing, how big the state can be?) (It is the state hash of the final block on Deku -> we need to hash it -> then we can use it to the Tezos chain?).
- Since there are probably two main variables that will determine block time, gas limits, and block size: network bandwidth, and validator speed. We should max out these two variables separately in benchmarking to see what their effect on capacities will be. 
---

# KPI 2: Deploy with the network

The same items as the local on KPI 1 but with the interactions done on a Deku's chain.

Additionally, identify hardware configurations on cloud. Run validators on different hardware configurations.

The senario can be:

- 4 validators (base case):
    - hardware configurations: 
        - minimal hardware configuration: find out the minimal hardware configuration that are required for validators to run on the cloud. 
        - Then change to run validators with different hardware and then compare the results.
    - operations: benchmarking to see how many operations (deposits, transfers, withdrawals) it can validate, at what minimal hardware configuration that it can handle for different number of operations. For instance, transfer 10, 1000 , etc. keep on increasing to see requirements.

- Increase the number of validators (growing case): like the base case, but increase the number of validators (for instance: 5, etc. up to 15, 21 because the consensus currently in Deku will not be effected after that).

---

# KPI 3: Profiling the process of start, stop, validate a Deku's node
- how long does it take to start and stop (at network layers) a validator (as running)
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

---

## KP 5.1: State hash

TODO: ask @d4hines for clarify this point

- State hashing runs without parallel
- What is the gain of having state hashing run in parallel
- @Swerve's guess
  - parallel hashing means that if you have 1k items to hash, instead of hashing them all serially, you can divide up the work and have 64 (or whatever) threads hashing them all at the same time
  - you can employ this tech to speed up the creation of merkle roots as well 


# KP 5: Block pool, signing and verifying signatures

<!--
- Functions the validators including the producer and validators signing and verifying signatures in a block and operations.

The validators can vote (accept the operations or not by voting Nil), these vote is like a endorsement of the block. -->

- How many signatures (validators votes accept) for a block to be accepted?

**Block pool**: the block with enough signatures from validators will be commited to a block pool.

- How big is this block pool? what is it limit?
- How the block priority to call and commit to Deku chain?
- How long a block can stay in this block pool? if after a time (when) it will be deleted? (will it be deleted)? is it as mempool in Tezos?

<!--
## KP 5.3: Compute the hash of a block

- Hashing block: A block of transactions that the producer will commit to Deku, hash the final state from this block.

- How many Gb can be hash per second (state hashing, how big the state can be hash per second?)-->

---

# Gossip network: KPI benchmarks
It is a separate KPIs.

----

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
    - To determine memory consumption over deku, we will need to determine how much memory is required for transactions and smart contract calls. The smart contract calls will need to be tested at varying sizes to see the largest and smallest that things can get. 
