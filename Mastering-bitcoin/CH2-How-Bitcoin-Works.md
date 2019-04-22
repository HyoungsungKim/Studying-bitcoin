# CH2 How bitcoin works

## Transactions, Blocks, Mining, and the Blockchain

The bitcoin system, unlike traditional banking and payment systems, is based on decentralized trust. Instead of a central trusted authority, in bitcoin trust is achieved as an emergent property from the interactions of different participants in the bitcoin system.

***What will learn***

- Examine bitcoin from a high level by tracking a single transaction through the bitcoin system
- Watch as it becomes "trusted" and accepted by the bitcoin mechanism of distributed consensus 
- It finally recorded on the blockchain, the distributed consensus and is finally recorded on the blockchain, the distributed ledger of all transactions. 



## Bitcoin Overview

Bitcoin system consists of ***users with wallets containing keys, transactions that are propagated across the network,*** and ***miners who produce(through competitive computation) the consensus blockchain, which is the authoritative ledger of all transactions.*** 

```
bitcoin:1GdK9UzpHBzqzX2A9JFP3Di4weBwqgmoQA?
amount=0.015&
label=Bob%27s%20Cafe&
message=Purchase%20at%20Bob%27s%20Cafe
Components of the URL
A bitcoin address: "1GdK9UzpHBzqzX2A9JFP3Di4weBwqgmoQA"
The payment amount: "0.015"
A label for the recipient address: "Bob's Cafe"
A description for the payment: "Purchase at Bob's Cafe"
```



## Bitcoin Transactions

- ***Chain of ownership***

  A transaction tells the network that the owner of some bitcoin value has authorized the transfer of that value to another owner. The new owner can now spend the bitcoin by creating another transaction that authorizes transfer to another owner, and so on

### Transaction Inputs and Outputs

Transactions are like lines in a double-entry bookkeeping ledger. Each transaction contains one or more "inputs", which are like debits against a bitcoin account.

On the other side of the transaction, ***There are one or more "outputs", which are like credits added to a bitcoin account.***  The inputs and outputs(debits and credits) do not necessarily add up to the same amount.

- Output is slightly small because of *transaction fee* 

The transaction also contains proof of ownership for each amount of bitcoin whose value is being spent, in the form of a digital signature from the owner,which can be independently validated by anyone.

### Transaction Chains

The transactions form a chain, where the inputs from the latest transaction correspond to outputs form previous transactions.

Inputs from the latest transaction correspond to outputs from previous transactions. ***Alice’s key provides the signature that unlocks those previous transaction outputs,*** thereby proving to the bitcoin network that she owns the funds. She attaches the payment for coffee to Bob’s address, ***thereby “encumbering” that output with the requirement that Bob produces a signature in order to spend that amount.*** 

### Making Change

Many bitcoin transactions will include ***outputs that reference both an address of the new owner and an address of the current owner,*** called the change address. This is because ***transaction inputs, like currency notes cannot be divided.***

> ***Inputs cannot be divided***
>
> If price is 5 BTC and i have 20 BTC then pay 5 BTC and get (15 BTC  - transaction fee) as the change
>
> -> cannot be divided

***Importantly, the change address does not have to be the same address as that of the input and for privacy reasons is often a new address from the owner's wallet.*** Different wallets may use different strategies when aggregating inputs to make a payment requested by the user.

> Changes are returned to sender's new address

- Wallets might aggregate many small inputs, or use one that is equal to or larger than the desired payment.

## Construction a Transaction

Sender only need to specify receiver's address(destination) and an amount. And ***the rest happens in the wallet application without her seeing the details.*** Importantly, a ***wallet application can construct transactions even if it is completely off-line.***  Like a writing a check at home and later sending it to the bank in an envelope, ***the transaction does not need to be constructed and signed while connected to the bitcoin network***

> ***Transaction can be done in wallet even without Internet***

Sender's wallet application will first have to ***find inputs that can pay for the amount sender wants to send to receiver.***  Most wallets keep track of all the available outputs belonging to addresses in the wallet.  Therefore, ***Sender's wallet would contain a copy of the transaction output from receiver's transaction, which was cerated in exchange for cash.***  A bitcoin wallet application that *runs as a full-node client* actually contains a copy of every unspent output from every transaction in the blockchain. ***This allows a wallet to construct transaction inputs as well as quickly verify incoming transactions as having correct inputs.*** However, because a full-node client takes up a lot of disk space, ***most user wallets run "lightweight" clients that track only the user;s own unspent outputs.***

If the wallet application does not maintain a copy of unspent transaction outputs, *it can query the bitcoin network to retrieve this information using a variety of APIs available by different providers or by asking a full-node using an application programming interface(API) call.*

> 1 satoshi is 0.00000001 BTC (10^-8) maybe because float only can express 10^-8



## Creating the Outputs

A transaction ***output is created in the form of a script*** that creates an encumbrance on the value and can ***only redeemed by the introduction of a solution to the script.***

> Redeem : 보완하다
>
> Encumbrance : 부동산 상의 저당권과 같은 채무, 걸림돌
>
> Encumber : 토지를 저당잡히다, 지장을 주다

In simpler terms, Sender's transaction output will contain a script that says something like, ***"This outputs is payable to whoever can present a signature from the key corresponding to receiver's public address"***

Because only receiver has the wallet with the keys corresponding to that address.

-> Therefore sender will "encumber" the output value with a demand for a signature from receiver.

Sender's change payment is created by sender's wallet as an output in the very same transaction as the payment to receiver. ***Essentially, sender's wallet breaks her funds into two payments: one to receiver and one back to sender-self. Then sender can then use (spend) the change output in s subsequent transaction.***

>large amount -> break in sender's wallet -> send fragment(This fragment cannot be divide in input)
>
>->  receive -> return change to sender



## Adding the Transaction to the Ledger

The transaction created by sender's wallet application is ***258 bytes long and contains everything necessary to confirm ownership of the finds and assign new owners.***

### Transmitting the transaction

Because the transaction contains all the information necessary to process, ***it does not matter how or where it is transmitted to the bitcoin network.*** The purpose of the bitcoin network is to propagate transactions and block to all participants.

### How it propagates

***Any system, such as a server, desktop application, or wallet, that participates in the bitcoin network by "speaking" the bitcoin protocol is called a bitcoin node***. sender's wallet application can send the new transaction to any bitcoin node it is connected to over any type of conenction

> Difference miner and wallet?
>
> - The wallet software does not mine or add onto the blockchain as the mining software does.
>
> - Wallet clients download the entire blockchain, but do not mine for blocks as the mining software does
> - But these days, there are wallet which do mining too 

***Any bitcoin node that receives a valid transaction it has not seen before will immediately forward it to all other nodes to which it is connected, a propagation technique know as a flooding.***

### Receiver's view

If receiver's bitcoin wallet applicant is directly connected to sender's wallet application, receiver's wallet application might be the first node to receive the transaction. ***Sender's wallet will immediately identify sender's transaction as an incoming payment because it contains outputs redeemable by receiver's keys.***

> Redeemable : 교환 할 수 있는

Receiver's wallet applicant can also independently verify that the transaction is well formed, ***uses previously unspent inputs, and contains sufficient transaction fees to be included in the next block.***

> OmiseGo how it works...? like a kind of node?
>
> But node of what? bitcoin? etherium?