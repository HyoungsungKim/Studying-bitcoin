# CH12 Blockchain Application

## Application from Building Blocks

- Proof-of-Existence(Digital notary)
- Kickstarter(lighthouse)
- Payment Channels

## Counterparty

Counterparty is a protocol layer built on top of bitcoin. The Counterparty protocol, similar to colored coins, offers the ability to create and trade virtual assets and tokens.

In addition, Counterparty offers a decentralized exchange for assets. Counterparty is also implementing smart contracts, based on the Ethereum Virtual Machine (EVM).

Counterparty can be used as a platform for other applications and services, in turn.

## Payment Channels and State Channels

Payment channels are a trustless mechanism for exchanging bitcoin transactions between two parties, outside of the bitcoin blockchain.

***These transactions, which would be valid if settled on the bitcoin blockchain, are held off-chain instead, acting as promissory notes for eventual batch settlement.***

> promissory : 약속의

***Actually, the term channel is a metaphor.*** We use the term channel to represent the relationship and shared state between two parties, outside of the blockchain.

a payment channel is just a series of transactions. If properly sequenced and connected, they create redeemable obligations that you can trust even though you don’t trust the other side of the channel.

***Payment channels are part of the broader concept of a state channel, which represents an off-chain alteration of state, secured by eventual settlement in a blockchain.***

### State Channels-Basic Concept and Terminology

***A state channel is established between two parties, through a transaction that locks a shared state on the blockchain.*** This is called the funding transaction or anchor transaction. This single transaction must be transmitted to the network and mined to establish the channel.

***The two parties then exchange signed transactions, called commitment transactions, that alter the initial state.*** These transactions are valid transactions in that they could be submitted for settlement by either party, but instead are held off-chain by each party pending the channel closure.

***State updates can be created as fast as each party can create, sign, and transmit a transaction to the other party. In practice this means that thousands of transactions per second can be exchanged.***

Finally, the channel can be closed either cooperatively, by submitting a final settlement transaction to the blockchain, or unilaterally, by either party submitting the last commitment transaction to the blockchain.

In the entire lifetime of the channel, only two transactions need to be submitted for mining on the blockchain:

- the funding
- settlement transactions

***In between these two states, the two parties can exchange any number of commitment transactions that are never seen by anyone else, nor submitted to the blockchain.***

## Routed Payment Channels(Lightning Network)

The Lightning Network is a proposed routed network of bidirectional payment channels connected end-to-end. ***A network like this can allow any participant to route a payment from channel to channel without trusting any of the intermediaries.***

For now, these implementations can only be run on testnet because they use segwit, which is not activated on the main bitcoin blockchain (mainnet).

### Lightning Network Transport and Routing

All communications between LN nodes are encrypted point-to-point. In addition, nodes have a long-term public key that they use as an identifier and to authenticate each other.

Whenever a node wishes to send a payment to another node, it must first construct a path through the network by connecting payment channels with sufficient capacity. Nodes advertise routing information, including what channels they have open, how much capacity each channel has, and what fees they charge to route payments.

At this point, you might be wondering how it is possible that the nodes do not know the length of the path and their position in that path. After all, they receive a message and forward it to the next hop. ***Doesn't it get shorter, allowing them to deduce the path size and their position? To prevent this, the path is always fixed at 20 hops and padded with random data.*** Each node sees the next hop and a fixed-length encrypted message to forward. ***Only the final recipient sees that there is no next hop. To everyone else it seems as if there are always 20 more hops to go.***