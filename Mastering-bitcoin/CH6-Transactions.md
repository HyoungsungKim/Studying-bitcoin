# CH 6 Transactions

## Introduction

Transactions are the most important part of the bitcoin system.  ***Everything else in bitcoin is designed to ensure that transactions can be created, propagated on the network, validate, and finally added to the global ledger of transactions(the blockchain).***

## Transactions in Detail

In CH 2, We looked at the transaction from sender to receiver.

The block explorer application shows a transaction from sender's "address" to receiver's "address". In fact, as we will see in this chapter, ***much of the information shown is constructed by the block explorer and is not actually in the transactions***

### Transactions-Behind the Scenes

Actual transaction looks very different from a transaction provided by a typical block explorer. In fact, most of the high-level constructs we see in the various ***bitcoin application user interfaces do not actually exists in the bitcoin system.***

```json
{
    "version": 1,
    "locktime": 0,
    "vin": [
        {"txid": "7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18",
         "vout": 0,
         "scriptSig" : "3045022100884d142d86652a3f47ba4746ec719bbfbd040a570b1deccbb6498c75c4ae24cb02204b9f039ff08df09cbe9f6addac960298cad530a863ea8f53982c09db8f6e3813[ALL] 0484ecc0d46f1918b30928fa0e4ed99f16a0fb4fde0735e7ade8416ab9fe423cc5412336376789d172787ec3457eee41c04f4938de5cc17b4a10fa336a8d752adf",
         "sequence": 4294967295
        }
    ],
    "vout": [
        {
            "value": 0.01500000,
            "scriptPubKey": "OP_DUP OP_HASH160 ab68025513c3dbd2f7b92a94e0581f5d50f654e7 OP_EQUALVERIFY OP_CHECKSIG"    
        },
        {
            "value": 0.08450000,
            "scriptPubKey": "OP_DUP OP_HASH160 7f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a8 OP_EQUALVERIFY OP_CHECKSIG",
        }
    ]
}
```

You may notice a few things about this transaction, mostly the things that  are  missing!  Where  is  sender’s  address?  Where  is  receiver’s  address?  Where  is  the  0.1  input  “sent”by  sender?  ***In bitcoin, there are no coins,  no senders, no recipients, no balances, no accounts, and no addresses.*** All those things are constructed at a higher level for the benefit of the user, to make things easier to understand. You may also notice a lot of strange and  indecipherable  fields  and  hexadecimal strings.



## Transaction Output and Inputs

***The fundamental building block of a bitcoin transaction is a transaction output.*** Transaction outputs are indivisible chunks of bitcoin currency, recorded on the blockchain, and recognized as valid by the entire network.

Bitcoin full nodes track all available and spendable outputs, ***known as unspent transaction outputs, or UTXO.*** The collection of all UTXO is known as the UTXO set and currently numbers in the millions of UTXO.

***The UTXO set grows as new UTXO is created and shrinks when UTXO is consumed.*** Every transaction represents a change (state transition) in the UTXO set.

***When we say that a user’s wallet has “received” bitcoin, what we mean is that the wallet has detected a UTXO that can be spent with one of the keys controlled by that wallet.*** Thus, a user’s bitcoin “balance” is the sum of all UTXO that user’s wallet can spend and which may be scattered among hundreds of transactions and hundreds of blocks.

***The concept of a balance is created by the wallet application.*** The wallet calculates the user’s balance by scanning the blockchain and aggregating the value of any UTXO the wallet can spend with the keys it controls.

> aggregating  :총액, 합계
>
> denominated : ~로 표시된

A transaction output can have an arbitrary (integer) value denominated as a multiple of satoshis.

> 1 satoshi : 0.00000001 btc

Although an output can have any arbitrary value, once created it is indivisible. This is an important characteristic of outputs that needs to be emphasized:output are discrete and indivisible units of value, denominated in integer satoshis.

***If an UTXO is larger than the desired value of a transaction, it must still be consumed in its entirety and change must be generated in the transaction.*** In other words, if you have a UTXO worth 20 bitcoin and want to pay only 1 bitcoin, your transaction must consume the entire 20-bitcoin ***UTXO and produce two outputs.***

Similarly, a bitcoin transaction must be created from a user’s UTXO in whatever denominations that user has available. ***Users cannot cut an UTXO*** in half any more than they can cut a dollar bill in half and use it as currency. ***The user’s wallet application will typically select from the user’s available UTXO to compose an amount greater than or equal to the desired transaction amount.***

The bitcoin application can use several strategies to satisfy the purchase amount: ***combining several smaller units, finding exact change, or using a single unit larger than the transaction value and making change.*** All of this complex assembly of spendable UTXO is done by the user’s wallet automatically and is invisible to users.

***The exception to the output and input chain is a special type of transaction called the coinbase transaction, which is the first transaction in each block.*** This special coinbase transaction does not consume UTXO; instead, it has a special type of input called the “coinbase.” This is how bitcoin’s
money supply is created during the mining process.

### Transaction Outputs

***Every bitcoin transaction creates outputs, which are recorded on the bitcoin ledger.*** Almost all of these outputs, with one exception create spendable chunks of bitcoin called UTXO, which are then recognized by the whole network and available for the owner to spend in a future transaction.

***UTXO are tracked by every full-node bitcoin client in the UTXO set.*** New transactions consume (spend) one or more of these outputs from the UTXO set.

Transaction outputs consist of two parts:

- An amount of bitcoin, denominated in satoshis.
- A ***cryptographic puzzle*** that determines the conditions required to spend the output

> require : 필요로 하다

***The cryptographic puzzle is also known as a locking script, a witness script, or a scriptPubKey***

JSON of sender's transaction

```json
"vout": [
{
    "value": 0.01500000,
    "scriptPubKey": "OP_DUP OP_HASH160 ab68025513c3dbd2f7b92a94e0581f5d50f654e7    OP_EQUALVERIFY OP_CHECKSIG"
},
{
    "value": 0.08450000,
    "scriptPubKey": "OP_DUP OP_HASH160 7f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a8    OP_EQUALVERIFY OP_CHECKSIG",
	}
]
```

- It is containing 2 output
- Each output is defined by a value and a cryptographic puzzle

The value is shown in bitcoin, but in the transaction itself it is recorded as an integer denominated in satoshis. The second part of each output is the cryptographic puzzle that sets the conditions for spending. ***Bitcoin core shows this as scriptPubKey and shows us a human-readable representation of the script.***

#### Transaction serialization-outputs

***When transactions are transmitted over the network or exchanged between applications, they are serialized.*** Serialization is the process of converting the internal representation of a data structure into a format that can be transmitted one byte at a time, also known as a byte stream. Serialization is most commonly used for encoding data structures for transmission over a network or for storage in a file.

***Most bitcoin libraries and frameworks do not store transactions internally as byte-streams, as that would require complex parsing every time you needed to access a single field.*** For convenience and readability, bitcoin libraries store transactions internally in data structures

***The process of converting*** from the byte-stream representation of a transaction to a library’s internal representation data structure is ***called deserialization or transaction parsing.*** Most bitcoin libraries
have built-in functions for transaction serialization and deserialization.

> (serialized)byte-stream -> process converting -> deserialization or transaction parsing



#### Transaction Inputs

Transaction inputs identify (by reference) which UTXO will be consumed and provide ***proof of ownership through an unlocking script.***

```JSON
"vin": [
	{
        "txid": "7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18",
        "vout": 0,
        "scriptSig" :
        "3045022100884d142d86652a3f47ba4746ec719bbfbd040a570b1deccbb6498c75c4ae24cb02204    b9f039ff08df09cbe9f6addac960298cad530a863ea8f53982c09db8f6e3813[ALL] 0484ecc0d46f1918b30928fa0e4ed99f16a0fb4fde0735e7ade8416ab9fe423cc5412336376789d1    72787ec3457eee41c04f4938de5cc17b4a10fa336a8d752adf",
        "sequence": 4294967295
	}
]
```

- The first part of an input is a pointer to an UTXO by ***reference to the transaction hash and sequence number where the UTXO is recorded in the blockchain.***
- The second part is an unlocking script, which the wallet constructs in order to satisfy the spending conditions set in the UTXO.

> 1. Find UTXO
> 2. Check UTXO can satisfy transaction by using script

Most often, the unlocking script is a digital signature and public key proving ownership of the bitcoin. ***However, not all unlocking scripts contain signatures.*** The third part is a sequence number, which will be discussed later.

As you can see, there is only one input in the list ***(because one UTXO contained sufficient value to make this payment).*** The input contains four elements(txid, vout, scriptSig, sequence)

- A transaction ID, referencing the transaction that contains the UTXO being spent
- An output index(vout), identifying which UTXO from that transaction is referenced(first one is zero)

- A scriptSig, which satisfies the conditions placed on the UTXO, unlocking it for spending
- A sequence number

In sender's transaction, the input points to the transaction ID:

```
7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18
```

and output index 0 (i.e., the first UTXO created by that transaction). ***The unlocking script is constructed by sender’s wallet by first retrieving the referenced UTXO, examining its locking script, and then using it to build the necessary unlocking script to satisfy it.***

> retrieve : 되찾아오다, 검색하다
>
> - input : contain unlocking script - require the proof of fulliment
> - output : contain locking script - required to spend such a UTXO

It’s not just sender’s wallet that needs to retrieve UTXO referenced in the inputs. Once this transaction is broadcast to the network, every validating node will also need to retrieve the UTXO referenced in the transaction inputs in order to validate the transaction.

Transactions reference UTXO in their inputs but without retrieving that UTXO we cannot know the value of
the inputs or their locking conditions. When writing bitcoin software, anytime you decode a transaction with the intent of validating it or counting the fees or checking the unlocking script, ***your code will first have to retrieve the referenced UTXO from the blockchain in order to build the context implied but not present in the UTXO references of the inputs.***

> intent : 강한 관심을 보이는, 몰두하는

For example, to calculate the amount paid in fees, you must know the sum of the values of inputs and outputs. But without retrieving the UTXO referenced in the inputs, you do not know their value. ***So a seemingly simple operation like counting fees in a single transaction in fact involves multiple steps and data from multiple transactions.***

***To fully understand Sender’s transaction we had to retrieve the previous transaction(s) referenced as inputs.*** -> To retrieve UTXOs



#### Transaction serialization - inputs

When transactions are serialized for transmission on the network, their inputs are encoded into a byte stream.

### Transaction Fees

Most transactions include transaction fees, which compensate the bitcoin miners for securing the network. ***Fees also serve as a security mechanism themselves, by making it economically infeasible for attackers to flood the network with transactions.***

Transaction fees serve as an incentive to include (mine) a transaction into the next block and also as a disincentive against abuse of the system by imposing a small cost on every transaction.

***Transaction fees are calculated based on the size of the transaction in kilobytes, not the value of the transaction in bitcoin.*** Overall, transaction fees are set based on market forces within the bitcoin network. Miners prioritize transactions based on many different criteria, including fees, and might even process transactions for free under certain circumstances.

***Transaction fees affect the processing priority, meaning that a transaction with sufficient fees is likely to be included in the next block mined,*** whereas a transaction with insufficient or no fees might be delayed, processed on a best-effort basis after a few blocks, or not processed at all.

- Transaction fee is not mandatory
- Transactions without fees might be processed eventually.

In Bitcoin Core, fee relay policies are set by the *minrelaytxfee* option. The current default *minrelaytxfee* is 0.0000 bitcoin or a hundredth of a millibitcoin per kilobyte. ***Therefore, by default, transactions with a fee less than 0.0001 bitcoin are treated as free and are only relayed if there is space in the mempool;*** otherwise, they are dropped. Bitcoin nodes can override the default fee relay policy by adjusting the value of *minrelaytxfee.*

Any bitcoin service that creates transactions, including wallets, exchanges, retail applications, etc., must implement dynamic fees. Dynamic fees can be implemented through a third-party fee estimation service or with a built-in fee estimation algorithm. ***Fee estimation algorithms calculate the appropriate fee, based on capacity and the fees offered by “competing” transactions.***

### Adding Fees to Transactions

***The data structure of transactions does not have a field for fees. Instead, fees are implied as the difference between the sum of inputs and the sum of outputs.***

```
Fees = Sun(Inputs) - Sum(Outputs)
```

Let’s see how this works in practice, by looking at Alice’s coffee purchase again. Alice wants to spend 0.015 bitcoin to pay for coffee. To ensure this transaction is processed promptly, she will want to include a transaction fee, say 0.001. That will mean that the total cost of the transaction will be 0.016. Her wallet must therefore source a set of UTXO that adds up to 0.016 bitcoin or more and, if necessary, create change. Let’s say her wallet has a 0.2-bitcoin UTXO available. It will therefore need to consume this UTXO, create one output to Bob’s Cafe for 0.015, and a second output with 0.184 bitcoin in change back to her own wallet, ***leaving 0.001 bitcoin unallocated, as an implicit fee for the transaction.***

> If sender have only small UTXOs and when it is used as input, block size will be big so although sender want to send small amount of UTXOs, sender have to pay high fee

## Transaction Script and Script Language

The bitcoin transaction script language, called *Script*, is a Forth-like reverse-polish notation stack-based execution language. ***When a transaction is validated, the unlocking script in each input is executed alongside the corresponding locking script to see if it satisfies the spending condition.***

Today, most transactions processed through the bitcoin network have the form “Payment to receiver’s bitcoin address” and are based on a script called a *Pay-to-Public-Key- Hash script.* However, bitcoin transactions are not limited to the “Payment to receiver’s bitcoin address” script.

In fact, locking scripts can be written to express a vast variety of complex conditions. In order to understand these more complex scripts, we must first understand the basics of transaction scripts and script language.

### Turing Incompleteness

> deliberately : 고의로, 의도적으로

The bitcoin transaction script language contains many operators, but is deliberately limited in one important way—***there are no loops or complex flow control capabilities other than conditional flow control.*** This ensures that the language is not Turing Complete, meaning that scripts have limited complexity and predictable execution times.

 Script is not a general-purpose language. ***These limitations ensure that the language cannot be used to create an infinite loop or other form of “logic bomb” that could be embedded in a transaction in a way that causes a denial-of-service attack against the bitcoin network.***

> DDOS : Distributed denial-of-service attack

### Stateless Verification

The bitcoin transaction script language is stateless, in that there is no state prior to execution of the script, or state saved after execution of the script. ***Therefore, all the information needed to execute a script is contained within the script.***

***If your system verifies a script, you can be sure that every other system in the bitcoin network will also verify the script, meaning that a valid transaction is valid for everyone and everyone knows this.*** This predictability of outcomes is an essential benefit of the bitcoin system.

### Script Construction (Lock + Unlock)

Bitcoin’s transaction validation engine relies on two types of scripts to validate transactions: 

- A locking script 
- An unlocking script.

***A locking script is a spending condition placed on an output:*** it specifies the conditions that must be met to spend the output in the future.

Historically, the locking script was ***called a scriptPubKey, because it usually contained a public key or bitcoin address (public key hash).***

You will also see the locking script referred to as a ***witness script*** or more generally as a ***cryptographic puzzle***. These terms all mean the same thing, at different levels of abstraction.

***An unlocking script is a script that “solves,” or satisfies, the conditions placed on an output by a locking script and allows the output to be spent.*** Unlocking scripts are part of every transaction input. ***Most of the time they contain a digital signature produced by the user’s wallet from his or her private key.***

Historically, the unlocking script was ***called scriptSig, because it usually contained a digital signature.***  You will also see the unlocking script referred to as a ***witness***. In this book, we refer to it as an “unlocking script” to acknowledge the much broader range of locking script requirements, ***because not all unlocking scripts must contain signatures.***

Every bitcoin validating node will validate transactions by executing the locking and unlocking scripts together. ***Each input contains an unlocking script and refers to a previously existing UTXO. The validation software will copy the unlocking script, retrieve the UTXO referenced by the input, and copy the locking script from that UTXO.*** The unlocking and locking script are then executed in sequence. The input is valid if the unlocking script satisfies the locking script conditions

***Note that the UTXO is permanently recorded in the blockchain, and therefore is invariable and is unaffected by failed attempts to spend it by reference in a new transaction.*** Only a valid transaction that correctly satisfies the conditions of the output results in the output being considered as “spent” and removed from the set of unspent transaction outputs (UTXO set).

> Basically UTXO is recorded permanently and only valid transaction can remove UTXO

#### The script execution stack

Bitcoin’s scripting language is called a stack-based language because it uses a data structure called a *stack.*

> stack : Last in First out(LIFO)

The scripting language executes the script by processing each item from left to right. Numbers (data constants) are pushed onto the stack.

##### Separate execution of unlocking and locking scripts

***In the original bitcoin client, the unlocking and locking scripts were concatenated and executed in sequence.*** For security reasons, this was changed in 2010, because of a vulnerability that allowed a malformed unlocking script to push data onto the stack and corrupt the locking script. ***In the current implementation, the scripts are executed separately with the stack transferred between the two executions, as described next.***

 ***If the unlocking script is executed without errors (e.g., it has no “dangling” operators left over), the main stack (not the alternate stack) is copied and the locking script is executed.*** 

***If the result of executing the locking script with the stack data copied from the unlocking script is “TRUE,” the unlocking script has succeeded in resolving the conditions imposed by the locking script*** and, therefore, the input is a valid authorization to spend the UTXO. If any result other than “TRUE” remains after execution of the combined script, the input is invalid because it has failed to satisfy the spending conditions placed on the UTXO.

### Pay-to-Public-Key-hash(P2PKH)

The vast majority of transactions processed on the bitcoin network spend outputs locked with a Pay-to-Public-Key-Hash or “P2PKH” script. ***These outputs contain a locking script that locks the output to a public key hash, more commonly known as a bitcoin address.***

***An output locked by a P2PKH script can be unlocked (spent) by presenting a public key and a digital signature created by the corresponding private key***

For example, let’s look at Alice’s payment to Bob’s Cafe again. Alice made a payment of 0.015 bitcoin to the cafe’s bitcoin address. That transaction output would have a locking script of the form:

> Input contains unlocking script
>
> Output contains locking script

```
OP_DUP OP_HASH160 <Cafe Public Key Hash> OP_EQUALVERIFY OP_CHECKSIG
```

The *Cafe Public Key Hash* is equivalent to the bitcoin address of the cafe, without the Base58Check encoding.



The preceding locking script can be satisfied with an unlocking script of the form:

```
<Cafe Signature> <Cafe Public Key>
```

The two scripts together would form the following combined validation script:

```
<Cafe Signature> <Cafe Public Key> OP_DUP OP_HASH160
<Cafe Public Key Hash> OP_EQUALVERIFY OP_CHECKSIG
```

When executed, this combined script will evaluate to TRUE if, and only if, the unlocking script matches the conditions set by the locking script.

> compare signature and public key in unlocking script
>
> -> if it is true, execute locking script
>
> -> or not true, it it invalid transaction

In other words, ***the result will be TRUE if the unlocking script has a valid signature from the cafe’s private
key that corresponds to the public key hash set as an encumbrance.***

> Transaction data in the blockchain does not include any ID for a "sender" or "recipient".
>
> The script contains two components, a signature and a public key. 
>
> Outputs include receiver's public key and signature for comparing
>
> [More information](https://learnmeabitcoin.com/glossary/input)

### Digital Signature(ECDSA)

So far, we have not delved into any detail about “digital signatures.” In this section we look at how digital signatures work and how they can present proof of ownership of a private key without revealing that private key.

The digital signature algorithm used in bitcoin is the *Elliptic Curve Digital Signature Algorithm*, or *ECDSA*. ECDSA is the algorithm used for digital signatures based on elliptic curve private/public key pairs.

A digital signature serves three purposes in bitcoin.

- First, the signature proves that the owner of the private key, who is implication the owner of the funds, has authorized the spending of those funds.
- Secondly, the proof of authorization is undeniable (nonrepudiation).

> nonrepudiation : 부인방지

- Thirdly, the signature proves that the transaction (or specific parts of the transaction) have not and cannot be modified by anyone after it has been signed.

***Note that each transaction input is signed independently. This is critical, as neither the signatures nor the inputs have to belong to or be applied by the same “owners.”***

***Each transaction input and any signature it may contain is completely independent of any other input or signature.*** Multiple parties can collaborate to construct transactions and sign only one input each.

### How Digital Signature Work

A digital signature is a mathematical scheme that consists of two parts.

- The first part is an algorithm for ***creating a signature, using a private key (the signing key), from a message (the transaction).***
- The second part is an algorithm that ***allows anyone to verify the signature, given also the message and a public key.***

>Make signature : private key
>
>Verify signature : public key

#### Creating digital signature

The “message” being signed is the transaction. The signing key is the user’s private key. The result is the signature:

```
Sig = Fsig(Fhash(m),dA)
```

where:

- ***dA is the signing private key***
- m is the transaction (or parts of it)
- Fhash is the hashing function
- Fsig is the signing algorithm
- Sig is the resulting signature

The function Fsig produces a signature Sig that is composed of two values, commonly referred to as R and S: 

```
Sig = (R, S)
```

Now that the two values R and S have been calculated, they are serialized into a bytestream using an international standard encoding scheme called the ***Distinguished Encoding Rules, or DER.***

#### Serialization of signatures (DER)

sender's unlocking script:

```
3045022100884d142d86652a3f47ba4746ec719bbfbd040a570b1deccbb6498c75c4ae24cb02204b
9f039ff08df09cbe9f6addac960298cad530a863ea8f53982c09db8f6e381301
```

That signature is a serialized byte-stream of the R and S values produced by sender’s wallet to prove sender owns the private key authorized to spend that output.

### Verifying the Signature

To verify the signature, one must have the signature (R and S), the serialized transaction, and the public key

Essentially, verification of a signature means ***“Only the owner of the private key that generated this public key could have produced this signature on this transaction.”***

The signature verification algorithm takes the message, the signer’s public key and the signature (R and S values), and returns TRUE if the signature is valid for this message and public key

### Signature Hash Types(SIGHASH)

The signature implies a commitment by the signer to specific transaction data. 

***Bitcoin signatures have a way of indicating which part of a transaction’s data is included in the hash signed by the private key using a SIGHASH flag.*** The SIGHASH flag is a single byte that is appended to the signature. ***Every signature has a SIGHASH flag and the flag can be different from to input to input.*** 

A transaction with three signed inputs may have three signatures with different SIGHASH flags, each signature signing (committing) different parts of the transaction.

***Remember, each input may contain a signature in its unlocking script. As a result, a transaction that contains several inputs may have signatures with different SIGHASH flags that commit different parts of the transaction in each of the inputs.*** 

### ECDSA Math

The signature algorithm first generates an ephemeral (temporary) private public key pair. This temporary key pair is used in the calculation of the R and S values, after a transformation involving the signing private key and the transaction hash.

 ***The temporary key pair is based on a random number k, which is used as the temporary private key.***

### The Importance of Randomness in Signature

The signature generation algorithm uses a random key k, as the basis for an ephemeral private/public key pair. ***The value of k is not important, as long as it is random.*** If the same value k is used to produce two signatures on different messages (transactions), then the signing private key can be calculated by anyone. ***Reuse of the same value for k in a signature algorithm leads to exposure of the private key!***

***If you are implementing an algorithm to sign transactions in bitcoin, you must use RFC 6979 or a similarly deterministic-random algorithm to ensure you generate a different k for each transaction.***

## Bitcoin Addresses, Balances, and Other Abstractions

Many of the simplistic and familiar concepts from the earlier chapters, such as bitcoin addresses and balances, seem to be absent from the transaction structure. ***We saw that transactions don’t contain bitcoin addresses, perse, but instead operate through scripts that lock and unlock discrete values of bitcoin.***

Constructing the current balance (displayed as “Final Balance”) requires a bit more work. The blockchain explorer keeps a separate database of the outputs that are currently unspent, the UTXO set. ***To maintain this database, the blockchain explorer must monitor the bitcoin network, add newly created UTXO, and remove spent UTXO, in real time, as they appear in unconfirmed transactions.***

From the UTXO set, the blockchain explorer sums up the value of all unspent outputs referencing Bob’s public key hash and produces the “Final Balance” number shown to the user.

Every day, hundreds of transactions that do not contain P2PKH outputs are confirmed on the blockchain. The blockcahin explores often present these with red warning messages saying they cannot decode an address.

***->These are not strange transaction but these are containing more complex locking script than the common P2PKH***

