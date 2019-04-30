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

