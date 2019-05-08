# CH10 Mining and Consensus

## Introduction

Although mining is incentivized by this reward, the primary purpose of mining is not the reward or the generation of new coins.

Mining is the mechanism that underpins the decentralized clearinghouse, by which transactions are validated and cleared. Miners validate new transactions and record them on the global ledger.

Miners receive two types of rewards in return for the security provided by mining: new coins created with each new block, and transaction fees

# Decentralized Consensus

consensus is an emergent artifact of the asynchronous interaction of thousands of independent nodes, all following simple rules.

Bitcoin’s decentralized consensus emerges from the interplay of four processes that occur independently on nodes across the network:

- Independent verification of each transaction, by every full node, based on a comprehensive
  list of criteria
- Independent aggregation of those transactions into new blocks by mining nodes, coupled with demonstrated computation through a Proof-of-Work algorithm
- Independent verification of the new blocks by every node and assembly into a chain
- Independent selection, by every node, of the chain with the most cumulative computation demonstrated through Proof-of-Work

## Independent Verification of Transaction

***A resulting transaction is then sent to the neighboring nodes in the bitcoin network so that it can be propagated across the entire bitcoin network.***

However, before forwarding transactions to its neighbors, ***every bitcoin node that receives a transaction will first verify the transaction. This ensures that only valid transactions are propagated across the network, while invalid transactions are discarded at the first node that encounters them.***

By independently verifying each transaction as it is received and before propagating it, every node builds a pool of valid (but unconfirmed) transactions known as the transaction pool, memory pool, or mempool.

## Aggregating Transactions into Blocks

After validating transactions, a bitcoin node will add them to the memory pool, or transaction pool, where transactions await until they can be included (mined) into a block.

> Candidate block : a block which is not valid

### The Coinbase Transaction

The first transaction in any block is a special transaction, called a coinbase transaction.

> Coinbase Transaction : generation reward + transactions fee

***Unlike regular transactions, the coinbase transaction does not consume (spend) UTXO as inputs.*** Instead, it has only one input, called the coinbase, which creates bitcoin from nothing.

### Coinbase Reward and Fees

```
Total Fees = Sum(Inputs) - Sum(Outputs)
```

If mining node writes the coinbase transaction 100 or 1000 bitcoin? The answer is that an incorrect reward would result in the block being deemed invalid by everyone else,

### Structure of the Coinbase transaction

The coinbase transaction has a special format. Instead of a transaction input specifying a previous UTXO to spend, it has a “coinbase” input.

> The structure of a coinbase is different with the structure of a normal transaction
>
> - Unlocking script is not needed

### Coinbase Data

Coinbase transactions do not have an unlocking script (aka, scriptSig) field. Instead, this field is replaced by coinbase data, which must be between 2 and 100 bytes.

## Constructing the Block Header

To construct the block header, the mining node needs to fill in six fields.

- Version
- Previous Block hash
- Merkle Root
- Timestamp
- Target
- Nonce - Nonce is initialized to zero

## Mining the Block

In the simplest terms, mining is the process of hashing the block header repeatedly, changing one parameter, until the resulting hash matches a specific target. The hash function’s result cannot be determined in advance, nor can a pattern be created that will produce a specific hash value. ***This feature of hash functions means that the only way to produce a hash result matching a specific target is to try again and again, randomly modifying the input until the desired hash result appears by chance.***

### Proof-of-Work Algorithm

The Proof-of-Work must produce a hash that is less than the target. A higher target means it is less difficult to find a hash that is below the target. A lower target means it is more difficult to find a hash below the target. The target and difficulty are inversely related.

### Target Representation

we saw that the block contains the target, in a notation called “target bits” or just “bits,” which in block 277,316 has the value of 0x1903a30c. ***This notation expresses the Proof-of-Work target as a coefficient/exponent format, with the first two hexadecimal digits for the exponent and the next six hex digits as the coefficient.***

> Bits value : 0x1903a30c
>
> First two hexa : 19 -> exponent
>
> Next six hexa : 03a30c -> coefficient
>
> ```
> target = coefficient * 2^(8 * (exponent – 3))
> ```

### Retargeting to Adjust Difficulty

This leads to the obvious questions: Why is the difficulty adjustable, who adjusts it, and how?

To keep the block generation time at 10 minutes, the difficulty of mining must be adjusted to account for these changes

```
New Target = Old Target * (Actual Time of Last 2016 Blocks / 20160 minutes)
```

***To avoid extreme volatility in the difficulty, the retargeting adjustment must be less than a factor of four (4) per cycle.*** If the required target adjustment is greater than a factor of four, it will be adjusted by a factor of 4 and not more. Any further adjustment will be accomplished in the next retargeting period because the imbalance will persist through the next 2,016 blocks. Therefore, large discrepancies between hashing power and difficulty might take several 2,016 block cycles to balance out.

> discrepancies  : 불일치

## Validating a New Block

As the newly solved block moves across the network, each node performs a series of tests to validate it before propagating it to its peers. This ensures that only valid blocks are propagated on the network.

The independent validation of each new block by every node on the network ensures that the miners

cannot cheat.

## Assembling and Selecting Chains of Blocks

Once a node has validated a new block, it will then attempt to assemble a chain by connecting the block to the existing blockchain.

The main chain will also have branches with blocks that are “siblings”  to the blocks on the main chain. These blocks are valid but not part of the main chain. They are kept for future reference.

If a valid block is received and no parent is found in the existing chains, that block is considered an “orphan.” Orphan blocks are saved in the orphan block pool where they will stay until their parent is received. Once the parent is received and linked into the existing chains, the orphan can be pulled out of the orphan pool and linked to the parent, making it part of a chain. Orphan blocks usually occur when two blocks that were mined within a short time of each other are received in reverse order.

## Blockchain Forks

***Because the blockchain is a decentralized data structure, different copies of it are not always consistent.***Blocks might arrive at different nodes at different times, causing the nodes to have different perspectives of the blockchain.

To resolve this, each node always selects and attempts to extend the chain of blocks that represents the most Proof-of-Work, ***also known as the longest chain or greatest cumulative work chain.***

## Mining and the Hashing Race

### The Extra Nonce Solution

The nonce values were exhausted in less than a second. 

1. ***The solution was to use the coinbase transaction as a source of extra nonce values.***

Because the coinbase script can store ***between 2 and 100 bytes of data, miners started using that space as extra nonce space***, allowing them to explore a much larger range of block header values to find valid blocks. ***The coinbase transaction is included in the merkle tree, which means that any change in the coinbase script causes the merkle root to change.***

Eight bytes of extra nonce, plus the 4 bytes of “standard” nonce allow miners to explore a total 296 (8 followed by 28 zeros) possibilities per second without having to modify the timestamp.

> nonce : 4 bytes (32 bits)
>
> extra nonce : 8 bytes (64 bits)

2. modify the timestamp

### Mining Pools





## Consensus Attacks

***It is important to note that consensus attacks can only affect future consensus, or at best, the most recent past (tens of blocks).*** Bitcoin’s ledger becomes more and more immutable as time passes. While in theory, a fork can be achieved at any depth, in practice, the computing power needed to force a very deep fork is immense, making old blocks practically immutable. Consensus attacks also do not affect the security of the private keys and signing algorithm (ECDSA). ***A consensus attack cannot steal bitcoin, spend bitcoin without signatures, redirect bitcoin, or otherwise change past transactions or ownership records.*** Consensus attacks can only affect the most recent blocks and cause denial-of-service disruptions on the creation of future blocks.

> immense : 어마어마한

- Double spending attack
- Deny service to specific bitcoin participants(Blacklist)

## Changing the Consensus Rules