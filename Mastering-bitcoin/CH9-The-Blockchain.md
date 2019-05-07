# CH9 The Blockchain

## Introduction

The blockchain data structure is an ordered, back-linked list of blocks of transactions.

***The Bitcoin Core client stores the blockchain metadata using Google’s LevelDB database.*** 

> metadata : 어떤 목적을 가지고 만들어진 데이터

Each block within the blockchain is identified by a hash, generated using the SHA256 cryptographic hash algorithm on the header of the block. ***Each block also references a previous block, known as the parent block, through the “previous block hash” field in the block header.***

- Genesis block : The first block of blockchain

## Structure of a Block

A block is a container data structure that aggregates transactions for inclusion in the public ledger, the blockchain.

> aggregate : 집합체

- Block Size : 4 bytes
- Block Header : 80 bytes
- average transaction : 250 bytes -average block contains more than 500 transactions
- Transaction Counter : 1-9 bytes

## Block Header

The block header consists of three sets of block metadata.

- *There is a reference to a previous blockhash*, with connects this block to the previous block
- Set of metadata, namely the *difficulty*, *timestamp*, and *nonce*
- merkle tree root

> reference of a previous hash, difficulty, timestamp, nonce, merkle tree

## Block Identifiers:Block Header Hash and Block Height

The primary identifier of a block is its cryptographic hash, a digital fingerprint, made by hashing the block header twice through the SHA256 algorithm.

***Note that the block hash is not actually included inside the block’s data structure, neither when the block is transmitted on the network, nor when it is stored on a node’s persistence storage as part of the blockchain.***

***Instead, the block’s hash is computed by each node as the block is received from the network.*** The block hash might be stored in a separate database table as part of the block’s metadata, to facilitate indexing and faster retrieval of blocks from disk.

***A second way to identify a block is by its position in the blockchain, called the block height.***

> ***Current hash is not saved but calculated by nodes***
>
> storing current hash is wasting(?)

A block can thus be identified two ways: 

- by referencing the block hash
- by referencing the block height

Unlike the block hash, ***the block height is not a unique identifier.*** Although a single block will always have a specific and invariant block height, the reverse is not true.

the block height does not always identify a single block. Two or more blocks might have the same block height, competing for the same position in the blockchain.

## Linking Blocks in the Blockchain

Bitcoin full nodes maintain a local copy of the blockchain, starting at the genesis block.

As a node receives incoming blocks from the network, it will validate these blocks and then link them to the existing blockchain. To establish a link, a node will examine the incoming block header and look for the “previous block hash.”

## Merkle Trees

Each block in the bitcoin blockchain contains a summary of all the transactions in the block using a merkle tree.

***A merkle tree, also known as a binary hash tree, is a data structure used for efficiently summarizing and verifying the integrity of large sets of data.***

Merkle trees are used in bitcoin to summarize all the transactions in a block, producing an overall digital fingerprint of the entire set of transactions, providing a very efficient process to verify whether a transaction is included in a block.

***A merkle tree is constructed by recursively hashing pairs of nodes until there is only one hash, called the root, or merkle root.*** 

***The merkle tree is constructed bottom-up.*** The transactions are not stored in the merkle tree; rather, their data is hashed and the resulting hash is stored in each leaf node. Consecutive pairs of leaf nodes are then summarized in a parent node,

```
HA = SHA256(SHA256(Transaction A))
HAB = SHA256(SHA256(HA + HB))
.
.
.
It becomes 32bytes

The process continues until there is only one node at the top, the node known as the
merkle root.
```

Because the merkle tree is a binary tree, it needs an even number of leaf nodes. ***If there is an odd number of transactions to summarize, the last transaction hash will be duplicated to create an even number of leaf nodes, also known as a balanced tree.***

To prove that a specific transaction is included in a block, a node only needs to produce log2(N) 32-byte hashes, constituting an authentication path or merkle path connecting the specific transaction to the root of the tree.

***With merkle trees, a node can download just the block headers (80 bytes per block) and still be able to identify a transaction’s inclusion in a block by retrieving a small merkle path from a full node***

> ***Block header is including merkle root***

## Merkle Trees and Simplified Payment Verification(SPV)

Merkle trees are used extensively by SPV nodes. SPV nodes don’t have all transactions and do not download full blocks, just block headers. In order to verify that a transaction is included in a block, without having to download all the transactions in the block, they use an authentication path, or merkle path.

> Full node give merkle path to SPV

## Bitcoin's Test Blockchain

There are other bitcoin blockchains that are used for testing purposes: at this time testnet, segnet, and regtest. Let’s look at each in turn.

### Testnet-Bitcoin's Testing Playground

Testnet is the name of the test blockchain, network, and currency that is used for testing purposes. The testnet is a fully featured live P2P network, with wallets, test bitcoins (testnet coins), mining, and all the other features of mainnet.

## Segnet - The Segregated Witness Testnet

In the future it is likely we will see other testnet blockchains that are specifically designed to test a single feature or major architectural change, like segnet.

## Regtest-The Local Blockchain

Regtest, which stands for “Regression Testing,” is a Bitcoin Core feature that allows you to create a local blockchain for testing purposes.

The regtest blockchains are intended to be run as closed systems for local testing.