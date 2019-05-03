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

