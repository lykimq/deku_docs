# Deku consensus workflows


## Tendermint

Tendermint achieves consensus with 3 steps process (Proposal, Prevote, Precommit) and works in rounds.

### Validator

**Definition of a validator**: Among the validators (a validator is a Deku node), there is only one producer (a validator that produces a block), the rest are validators who take the proposed block from the block producer and validate it. When the validators accepted the proposed block they will sign and send it back to the block producer, those signatures are the same as an endorsement would be in Tezos. The block producer then commits the valid block in Deku. 

Number of validators: Tendermint requires at least 4 validators (one producer and 3 validators) to run properly. Currently, it can support at most 15 to 21 validators. 

---
### Operations

#### Deku operations

**Definition of Deku operations**: there are 3 operations: deposits, transfers and withdrawal.

When the smart contract is deposited to Deku, it stores it in a vault (its size limit is the limit of Bigmap of Michelson smart contract). Deku will then forge this operation to Deku's ticket so it will be considered an internal operation, and Deku can perform operations on it. These operations are not observed on Tezos' chain, only on Deku's chain.

#### Internal operations

**Definition of Deku internal operations**: those are ticket operations, transfer ticket, etc. 

----
### Storage

Deku's storage strategy is as follows:
- When the state root hash changes, serialize the entire state via `Marshel.to_bytes` and write it to a file `$data_folder/state.bin` where: `$data_folder` is, e.g, `./data/1` for node 1. What we can do from this is that we can read this file from another process and interspec the entire state, using Deku's libraries.

----
### Block

About block, we can take a look at the implementation in the `./src.node` library. More specifically, the files `./src/node/flows.ml` and `./src/node/building_blocks.ml`.

For block time, it is currently rate limited by the block producer, at the function `broadcast_block_and_signature` (`./src/node/building_blocks.ml`). The block producer waits 1 second before transmitting a block.

