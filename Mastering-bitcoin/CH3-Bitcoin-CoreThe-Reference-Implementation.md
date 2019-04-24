# CH.3 Bitcoin core: The Reference Implementation

Bitcoin is an open source project and the source code is available under an open(MIT) license, free to download and use for any purpose.

That first implementation, then simply known as “Bitcoin” or “Satoshi client,” has been heavily modified and improved. It has evolved into what is known as Bitcoin Core, to differentiate it from other compatible implementations. ***Bitcoin Core is the reference implementation of the bitcoin system, meaning that it is the authoritative reference on how each part of the technology should be implemented.***

- Bitcoin Core implements all aspects of bitcoin, including wallets, a transaction and block validation engine, and a full network node in the peer-to-peer bitcoin network.
- Even though Bitcoin Core includes a reference implementation of a wallet, this is not intended to be used as a production wallet for users or for applications.

### Running a Bitcoin Core Node

Bitcoin's peer-to-peer network is composed of network "nodes", run mostly by volunteers and some of the businesses that build bitcoin applications. Those running bitcoin nodes have a direct and authoritative view of the bitcoin blockchain, with a local copy of all the transactions, independently validated by their own system.

Why would you want to run a node?

- If you are developing bitcoin software and need to rely on a bitcoin node for programmable(API) access to the network and blockchain.
- If you are building applications that must validate transactions according to bitcoin's consensus rules. Typically, bitcoin software companies run several nodes.
- ***If you do not want to rely on any third party to process or validate your transactions***

-> If interested in developing bitcoin software, should be running your own node



## Getting Information on the Bitcoin Core Client Status

```
$ bitcoin-cli getinfo
```

This command display basic information about the status of the bitcoin network node, the wallet, and the blockchain database.

### Exploring and Decoding Transactions

```
Commands : getrawtransaction, decoderawtransaction
$ bitcoin-cli getrawtransaction 0627052b6f28912f2703066a912ea577f2ce4da4caa5a↵
5fbd8a57286c345c2f2
.
.
Rersult
.
.

$ bitcoin-cli decoderrawtransaction result
```

0627052b6f28912f2703066a912ea577f2ce4da4caa5a5fbd8a57286c345c2f2 is transaction ID ***A transaction ID is not authoritative until a transaction has been confirmed.***  Absence of a transaction hash in teh blockchain does not mean the transaction was not processed.

***->Transaction malleability***

Because transaction hashes can be modified prior to confirmation in a block.  ***After confirmation, the txid is immutable and authoritative.***

The input to this transaction was the output from a previously confirmed transaction