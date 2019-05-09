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

Miners now collaborate to form mining pools, pooling their hashing power and sharing the reward among thousands of participants.

***How does a mining pool measure the individual contributions, so as to fairly distribute the rewards, without the possibility of cheating?*** The answer is to use bitcoin’s Proof-of-Work algorithm to measure each pool miner’s contribution, but set at a lower difficulty so that even the smallest pool miners win a share frequently enough to make it worthwhile to contribute to the pool.

Let’s return to the analogy of a dice game. If the dice players are throwing dice with a goal of throwing less than four (the overall network difficulty), ***a pool would set an easier target, counting how many times the pool players managed to throw less than eight. When pool players throw less than eight (the pool share target), they earn shares, but they don’t win the game because they don’t achieve the game target (less than four).*** The pool players will achieve the easier pool target much more often, earning them shares very regularly, even when they don’t achieve the harder target of winning the game. Every now and then, one of the pool players will throw a combined dice throw of less than four and the pool wins. Then, the earnings can be distributed to the pool players based on the shares they earned. Even though the target of eight-or-less wasn't winning, it was a fair way to measure dice throws for the players, and it occasionally produces a less-than-four throw.

#### Managed pools

Most mining pools are “managed,” meaning that there is a company or individual running a pool server. The owner of the pool server is called the *pool operator*, and he charges pool miners a percentage fee of the earnings.

The pool server is also connected to one or more full bitcoin nodes and has direct access to a full copy of the blockchain database. ***This allows the pool server to validate blocks and transactions on behalf of the pool miners, relieving them of the burden of running a full node.***

#### Peer-to-peer mining pool(P2Pool)

Managed pools create the possibility of cheating by the pool operator, who might direct the pool effort to double-spend transactions or invalidate blocks.

Furthermore, centralized pool servers represent a single-point- of-failure. If the pool server is down or is slowed by a denial-of-service attack, the pool miners cannot mine.In 2011, to resolve these issues of centralization, ***a new pool mining method was proposed and implemented: P2Pool, a peer-to-peer mining pool without a central operator.***

P2Pool works by decentralizing the functions of the pool server, implementing a parallel blockchain-like system called a *share chain*.

A share chain is a blockchain running at a lower difficulty than the bitcoin blockchain. ***The share chain allows pool miners to collaborate in a decentralized pool by mining shares on the share chain at a rate of one share block every 30 seconds.***

On P2Pool, individual pool miners construct their own candidate blocks, aggregating transactions much like solo miners, but then mine collaboratively on the share chain. P2Pool is a hybrid approach that has the advantage of much more granular payouts than solo mining, but without giving too much control to a pool operator like managed pools.

## Consensus Attacks

***It is important to note that consensus attacks can only affect future consensus, or at best, the most recent past (tens of blocks).*** Bitcoin’s ledger becomes more and more immutable as time passes. While in theory, a fork can be achieved at any depth, in practice, the computing power needed to force a very deep fork is immense, making old blocks practically immutable. Consensus attacks also do not affect the security of the private keys and signing algorithm (ECDSA). ***A consensus attack cannot steal bitcoin, spend bitcoin without signatures, redirect bitcoin, or otherwise change past transactions or ownership records.*** Consensus attacks can only affect the most recent blocks and cause denial-of-service disruptions on the creation of future blocks.

> immense : 어마어마한

- Double spending attack
- Deny service to specific bitcoin participants(Blacklist)

## Changing the Consensus Rules

The rules of consensus determine the validity of transactions and blocks.

### Hard Forks

There is another scenario in which the network may diverge into following two chains: a change in the consensus rules. This type of fork is called a hard fork, because after the fork the network does not reconverge onto a single chain. Instead, the two chains evolve independently.

### Hard Forks:Software, Network, Mining and Chain

Conceptually, we can think of a hard fork as developing in four stages: a software fork, a network fork, a mining fork, and a chain fork.

The process begins when an alternative implementation of the client, with modified consensus rules, is created by developers.

### Contentious hard Forks

The risk of splitting the entire system into two competing systems is seen by many as an unacceptable risk. As a result, many developers are reluctant to use the hard fork mechanism to implement upgrades to the consensus rules, unless there is near-unanimous support from the entire network.

Any hard fork proposals that do not have near-unanimous support are considered too “contentious” to attempt without risking a partition of the system.

### Soft Forks

Not all consensus rule changes cause a hard fork. Only consensus changes that are forward-incompatible cause a fork. If the change is implemented in such a way that an unmodified client still sees the transaction or block as valid under the previous rules, the change can happen without a fork.

***In practice, a soft fork is not a fork at all. A soft fork is a forward-compatible change to the consensus rules that allows unupgraded clients to continue to operate in consensus with the new rules.***

#### Soft forks redefining NOP opcodes

For example, BIP-65 (CHECKLOCKTIMEVERIFY) reinterpreted the NOP2 opcode. Clients implementing BIP-65 interpret NOP2 as OP_CHECKLOCKTIMEVERIFY and impose an absolute locktime consensus rule on UTXO that contain this opcode in their locking scripts. This change is a soft fork because a transaction that is valid under BIP-65 is also valid on any client that is not implementing (ignorant of) BIP-65. To the old clients, the script contains an NOP code, which is ignored.

#### Other ways to soft fork upgrade

***Segwit is an architectural change to the structure of a transaction, which moves the unlocking script (witness) from inside the transaction to an external data structure (segregating it).*** 

> Unlocking script is moved to external data structure
>
> So put more transactions in block 
>
> It is segwit

Segwit was initially envisioned as a hard fork upgrade, as it modified a fundamental structure (transaction)

***The mechanism used for this is a modification of the locking script of UTXO created under segwit rules, such that unmodified clients see the locking script as redeemable with any unlocking script whatsoever***. As a result, segwit can be introduced without requiring every node to upgrade or split from the chain: a soft fork.

