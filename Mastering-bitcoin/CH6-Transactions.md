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



#### Transaction serialization -inputs

