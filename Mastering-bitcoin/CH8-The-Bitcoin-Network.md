#  CH8 The Bitcoin Network

## Peer-to-peer Network Architecture

Bitcoin is structured as a peer-to-peer network architecture on top of the internet. There is no server, no
centralized service, and no hierarchy within the network. ***Nodes in a P2P network both provide and consume services at the same time with reciprocity acting as the incentive for participation.***

Beyond bitcoin, the largest and most successful application of P2P technologies is file sharing, with Napster as the pioneer and BitTorrent as the most recent evolution of the architecture.

The term “bitcoin network” refers to the collection of nodes running the bitcoin P2P protocol. In addition to the bitcoin P2P protocol, there are other protocols such as Stratum that are used for mining and lightweight or mobile wallets. These additional protocols are provided by gateway routing servers that access the bitcoin network using the bitcoin P2P protocol and then extend that network to nodes running other protocols.

## Node Types and Roles

***Although nodes in the bitcoin P2P network are equal, they may take on different roles depending on the functionality they are supporting.*** A bitcoin node is a collection of functions: routing, the blockchain database, mining, and wallet services.

***All nodes validate and propagate transactions and blocks, and discover and maintain connections to peers.***

- Some nodes, called full nodes, also maintain a complete and up-to-date copy of the blockchain. ***Full nodes can autonomously and authoritatively verify any transaction without external reference***
- Some nodes maintain only a subset of the blockchain and verify transactions using a method called simplified payment verification, or SPV. These nodes are known as SPV nodes or lightweight nodes.
- Mining nodes compete to create new blocks by running specialized hardware to solve the Proof-of-Work algorithm.
- lightweight nodes participating in pool mining and depending on a pool server to maintain a full node
- User wallets might be part of a full node, as is usually the case with desktop bitcoin clients. those running on resource constrained devices such as smartphones, are SPV nodes.

## The Extended Bitcoin Network

Various large companies interface with the bitcoin network by running full-node clients based on the Bitcoin Core client, with full copies of the blockchain and a network node, but without mining or wallet functions.

## Bitcoin Relay Networks

While the bitcoin P2P network serves the general needs of a broad variety of node types, ***it exhibits too high network latency for the specialized needs of bitcoin mining nodes.***

participating in this competition, bitcoin miners must minimize the time between the propagation of
a winning block and the beginning of the next round of competition. ***In mining, network latency is directly related to profit margins.***

***A Bitcoin Relay Network is a network that attempts to minimize the latency in the transmission of blocks between miners.***

change of relay service

AWS -> UDP-based relay network -> Falcon

***Another relay network (still in the proposal phase) is Falcon***. University. Falcon uses ***“cut-through-routing”*** instead of “store-and-forward” to reduce latency by propagating parts of blocks as they are received rather than waiting until a complete block is received.

## Network Discover

When a new node boots up, it must discover other bitcoin nodes on the network in order to participate. ***To start this process, a new node must discover at least one existing node on the network and connect to it.*** ***The geographic location of other nodes is irrelevant;*** the bitcoin network topology is not geographically defined. Therefore, any existing bitcoin nodes ***can be selected at random.***

To connect to a known peer, nodes establish a ***TCP connection, usually to port 8333***(the port generally known as the one used by bitcoin), or an alternative port if one is provided.

How does a new node find peers? ***The first method is to query DNS using a number of “DNS seeds,” which are DNS servers that provide a list of IP addresses of bitcoin nodes.*** Some of those DNS seeds provide a static list of IP addresses of stable bitcoin listening nodes. Some of the DNS seeds are custom implementations of BIND (Berkeley Internet Name Daemon) that ***return a random subset from a list of bitcoin node addresses collected by a crawler or a long-running bitcoin node.***

Alternatively, a bootstrapping node that knows nothing of the network must be given the IP address of at least one bitcoin node, after which it can establish connections through further introductions. 

Only one connection is needed to bootstrap, because ***the first node can offer introductions to its peer nodes and those peers can offer further introductions.***

After bootstrapping, a node will remember its most recent successful peer connections, so that ***if it is rebooted it can quickly reestablish connections with its former peer network.***

If there is no traffic on a connection, nodes will periodically send a message to maintain the connection. If a node has not communicated on a connection for more than 90 minutes, it is assumed to be disconnected and a new peer will be sought. Thus, the network dynamically adjusts to transient nodes and network problems, and can organically grow and shrink as needed without any central control.

## Full Nodes

Full nodes are nodes that maintain a full blockchain with all transactions. More accurately, they probably should be called “full blockchain nodes.”

## Exchanging "Inventory"

The first thing a full node will do once it connects to peers is try to construct a complete blockchain.

1. The process of syncing the blockchain starts with the *version* message.

2. A node will see the *version* messages from its peers, know how many blocks they each have, and be able to compare to how many blocks it has in its own blockchain.

3. Peered nodes will exchange a *getblocks* message that contains the hash (fingerprint) of the
   top block on their local blockchain

4. The peer that has the longer blockchain has more blocks than the other node and can identify which blocks the other node needs in order to “catch up.” It will identify the first 500 blocks to share and transmit their hashes using an *inv* (inventory) message.
5. The node missing these blocks will then retrieve them, by issuing a series of *getdata* messages requesting the full block data and identifying the requested blocks using the hashes from the *inv* message.

## Simplified Payment Verification(SPV) Nodes

Not all nodes have the ability to store the full blockchain. 

A ***simplified payment verification (SPV)*** method is used to allow them to ***operate without storing the full blockchain.*** These types of clients are called SPV clients or lightweight clients.

***SPV nodes download only the block headers and do not download the transactions included in each block.***  SPV nodes cannot construct a full picture of all the UTXOs that are available for spending because they do not know about all the transactions on the network. ***SPV nodes verify transactions using a slightly different methodology that relies on peers to provide partial views of relevant parts of the blockchain on demand.***

Full node is like a tourist in a strange city, equipped with a detailed map of every street and every address. By comparison, an SPV node is like a tourist in a strange city asking random strangers for turn-by-turn directions while knowing only one main avenue. ***Although both tourists can verify the existence of a street by visiting it, the tourist without a map doesn’t know what lies down any of the side streets and doesn't know what other streets exist.*** The mapless tourist’s best chance is to ask enough people and hope some of them are not trying to mug him.

- SPV verifies transactions by reference to their depth in the blockchain instead of their height.
- Full blockchain node will construct a fully verified chain of thousands of blocks and transactions reaching down the blockchain (back in time) all the way to the genesis block

An SPV node will verify the chain of all blocks (but not all transactions) and link that chain to the transaction of interest.

***An SPV node cannot validate whether the UTXO is unspent. Instead, the SPV node will establish a link between the transaction and the block that contains it, using a merkle path***

> SPV is linked by merkle path

An SPV node cannot be persuaded that a transaction exists in a block when the transaction does not in fact exist. ***The SPV node establishes the existence of a transaction in a block by requesting a merkle path proof and by validating the Proof-of-Work in the chain of blocks.*** However, a transaction’s existence can be “hidden” from an SPV node. ***An SPV node can definitely prove that a transaction exists but cannot verify
that a transaction, such as a double-spend of the same UTXO, doesn't exist because it doesn't have a record of all transactions.*** 

> SPV can prove exist but cannot verify double-spending attack

***This vulnerability can be used in a denial-of-service attack or for a double-spending attack against SPV nodes.*** To defend against this, an SPV node needs to connect randomly to several nodes, to increase the probability that it is in contact with at least one honest node. This need to randomly connect means that SPV nodes also are vulnerable to network partitioning attacks or Sybil attacks, where they are connected to fake nodes or fake networks and do not have access to honest nodes or the real bitcoin network.

> If SPV is not connected with honest nodes, it is very vulnerable to attack.
>
> SPVs are good but full nodes are best

the SPV node’s requests for specific data can inadvertently reveal the addresses in their wallet.

> Inadvertently : 무심코, 우연히

***Because SPV nodes need to retrieve specific transactions in order to selectively verify them, they also create a privacy risk.*** Unlike full blockchain nodes, which collect all transactions within each block, ***the SPV node’s requests for specific data can inadvertently reveal the addresses in their wallet.***

Shortly after the introduction of SPV/lightweight nodes, bitcoin developers added a feature called bloom filters to address the privacy risks of SPV nodes.

Bloom filters allow SPV nodes to receive a subset of the transactions without revealing precisely which addresses they are interested in, through a filtering mechanism that uses probabilities rather than fixed patterns.

## Bloom Filters

A bloom filter is a probabilistic search filter, a way to describe a desired pattern without specifying it exactly.

### How Bloom Filters Work

Bloom filters are implemented as a variable-size array of N binary digits (a bit field) and a variable number of M hash functions.

By choosing different length (N) bloom filters and a different number (M) of hash functions, the bloom filter can be tuned, varying the level of accuracy and therefore privacy.

## How SPV Nodes Use Bloom Filters

Bloom filters are used to filter the transactions (and blocks containing them) that an SPV node receives from its peers, selecting only transactions of interest to the SPV node without revealing which addresses or keys it is interested in.

An SPV node will initialize a bloom filter as “empty”; in that state the bloom filter will not match any patterns. ***The SPV node will then make a list of all the addresses, keys, and hashes that it is interested in.*** It will do this by extracting the public key hash and script hash and transaction IDs from any UTXO controlled by its wallet. ***The SPV node then adds each of these to the bloom filter, so that the bloom filter will “match” if these patterns are present in a transaction, without revealing the patterns themselves.***

After a filter is established, the peer will then test each transaction’s outputs against the bloom filter. Only transactions that match the filter are sent to the node.

> compare results of hash 
>
> 1. Make a list which SPV is interested in
> 2. use it to make a bloom filter
> 3. only valid transaction can pass bloom filter

***As the full node sends transactions to the SPV node, the SPV node discards any false positives and uses the correctly matched transactions to update its UTXO set and wallet balance.*** As it updates its own view of the UTXO set, it also modifies the bloom filter to match any future transactions referencing the UTXO it just found. The full node then uses the new bloom filter to match new transactions and the whole process repeats.

## SPV Nodes and Privacy

Nodes that implement SPV have weaker privacy than a full node. ***A full node receives all transactions and therefore reveals no information about whether it is using some address in its wallet.***

An SPV node receives a filtered list of transactions related to the addresses that are in its wallet. As a result, it reduces the privacy of the owner.

## Encrypted and Authenticated Connections

Most new users of bitcoin assume that the network communications of a bitcoin node are encrypted. In fact, the original implementation of bitcoin communicates entirely in the clear. While this is not a major privacy concern for full nodes, it is a big problem for SPV nodes.

### Tor Transport

Bitcoin Core offers several configuration options that allow you to run a bitcoin node with its traffic transported over the Tor network.

### Peer-to-Peer Authentication and Encryption



## Transaction Pools

Almost every node on the bitcoin network maintains a temporary list of unconfirmed transactions called the memory pool, mempool, or transaction pool.

As transactions are received and verified, they are added to the transaction pool and relayed to the neighboring nodes to propagate on the network.

Some node implementations also maintain a separate pool of orphaned transactions. If a transaction’s inputs refer to a transaction that is not yet known, such as a missing parent, the orphan transaction will be stored temporarily in the orphan pool until the parent transaction arrives.

> orphaned block: valid but included in non-the longest chain(it is not connected with other blocks)
>
> -> parent block is unknown
>
> stale block : valid but not part of the best blockchain
>
> -> parent block is known
>
> ***[ Bitcoin Core v0.10, there are no such orphan blocks anymore, due to a significant change in the download mechanism](https://bitcoin.stackexchange.com/questions/5859/what-are-orphaned-and-stale-blocks)***