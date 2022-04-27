
# KPI: Key Performance Indicators for benchmarking Deku, more specifically the Tendermint consensus

Tendermint achieves consensus through 3 step process (Proposal, Prevote, Precommit) and works in rounds.

**Definition about validator**: Among the validators (a validator is a Deku node), there is only one producer (validator that produce block), the rest is validators who take the proposes block from block producer and validate the block (when the validators accepted the block propose, they will sign and send it back to the block producer, those signatures can be seen as endorsement in Tezos). The block producer is then responsible commit a valid block in Deku. 

Number of validators: Tendermint requires at least 4 validators (one producer and 3 validators) to run properly. Currently, it max is 15 to 21 validators to run effectively, if there are more validators the algorithm will not run well. 

**Definition about operations in Deku**: there are 3 operations: deposits, transfers and withdrawal.

When the smart contract is deposited to Deku, it stores in a vault (its size limit is the limit of Bigmap of Michelson smart contract). Then Deku will forges this operation to Deku's ticket (so that Deku can do operations on it and it is conserded as internal operations). These operations are not observed on Tezos' chain (only on Deku's chain).

**Definition about internal operations inside Deku**: those are ticket operations: transfer ticket, etc. 

There is a gossip network to communicate between nodes. This is not discuss in this document.

# KPI 1: Identify hardware configurations on cloud

Deku run on RAM and reply on hardware to run and store states.

Run validators on different hardware configurations.

The senario can be:

- 4 validators (base case):
    - hardware configurations: 
        - minimal hardware configuration: find out what is the minimal hardware configuration that require for validators to run on cloud. 
        - Then change to run validators with different hardware and then compare the results.
    - operations: benchmarking to see how many operations (deposits, transfers, withdrawals) it can validate, at what minimal hardware configuration that it can handle for different number of operations. For instance, transfer 10, 1000 , etc. keep on increasing to see requirements.

- Increase the number of validators (growing case): like the base case, but increase the number of validators (for instance: 5, etc. up to 15, 21 because the consensus currently in Deku will not be effected after that).


# KPI 2: Process, operations and internal operations on Deku's nodes (validators)

## KPI 2.1: Operations: deposits, transfers, withdrawal

- Deku's node stores a list of operations:
    - how many operations it is considered as full?
    - When the block is baked?
  
## KPI 2.2: Snapshot
Remark: is it a good place here? 

    - when we take snapshot? 
    - A snapshot has how many blocks? 
    - how to transfer snapshot to a database? 
    - what is that database to store snapshot? 
    - how fast/slow to call the snapshot? (do we need it?)

## KPI 2.3: Process of start, stop, validate a Deku's node
    - how long does it take to start and stop (at network layers) a validator (as running)
    - how long for a validator to:
        - validate operations (deposits, transfers, withdrawals)?
        - if I increase the number of validators how fast or slow is that? 

## KPI 2.4: internal operations inside the consensus

### KPI 2.4.1: The hardware requirement for running those internal operations

Same as KP 1 for running on cloud but for internal operations.

### KPI 2.4.2: Benchmark to see the number of those internal operations

Same as KP 2 but for internal operations.

---

# KP 3: Data structure requirements

`src/node/tendermint_data.ml`

https://github.com/marigold-dev/deku/pull/433 (at section of Data structure requirements)

To work, Tendermint requires specific data structures:
- an `input_log`: that stores the incoming messages a consensus instance receives.
- an `output_log`: that stores the decision made by a consensus instance.


## KP 3.1: Input log
- How many incoming message in input log? `msg_log` holds the consensus message broadcasted to the network. 
- `timeouts` function holds timeouts of the instance.

These functions affect the perform of the operations required in Tendermint.

## KP 3.2: Output log

A decision log: 
- at what height the value that it was decided on
- and the round it was deciced on (the round is required to mitigate byzantine threats for instance nodes lying on rounds).

Benchmark the max of this log (height and rounds)

---

# KP 4: Benchmark hashing functions

## KP 4.1: State hash

TODO: ask @d4hines for clarify this point

- State hashing run without parallel
- What is the gain of having the state hashing run in parallel

## KP 4.2: Signing and verifying signatures

- Functions the validators including the producer and validators signing and verifying signatures in a block and operations.

The validators can vote (accept the operations or not by voting Nil), these vote is like a endorsement of the block. 

- How many signatures (validators votes accept) for a block to be accepted?

Then the block with enough signatures from validators will be commited to a block pool.

- How big is this block pool? what is it limit?
- How the block priority to call and commit to Deku chain?
- How long a block can stay in this block pool? if after a time (when) it will be deleted? (will it be deleted)? is it as mempool in Tezos?

## KP 4.3: Compute the hash of a block

- Hashing block: A block of transactions that the producer will commit to Deku, hash the final state from this block.


---

# Gossip network: KPI benchmarks
It is a separate KPIs.