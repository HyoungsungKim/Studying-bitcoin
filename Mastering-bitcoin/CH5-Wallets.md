# CH 5 Wallets

The word "wallets" is used to describe a few difference things in bitcoin.

At a high level, a wallet is an application that serves as the primary user interface. The wallets controls access to a user's money, managing keys and addresses, tracking the balance, and creating and signing transactions.

More narrowly, *from a programmer’s perspective,* the word ***“wallet” refers to the data structure used to store and manage a user’s keys.***

## Wallet Technology Overview

***A common misconception about bitcoin is that bitcoin wallets contain bitcoin.*** In fact, ***the wallet contains only keys.*** The “coins” are recorded in the blockchain on the bitcoin network.

Users control the coins on the network by signing transactions with the keys in their wallets. ***In a sense, a bitcoin wallet is a keychain.***

The coins are stored on the blockchain in the form of transaction outputs (often noted as vout or txout).

There are two primary types of wallets, distinguished by whether the keys they contain are related to each other or not.

- ***The first type is a nondeterministic wallet***, where each key is independently generated from a random number. The keys are not related to each other. ***This type of wallet is also known as a JBOK wallet from the phrase “Just a Bunch Of Keys.”***
- ***The second type of wallet is a deterministic wallet, where all the keys are derived from a single master key, known as the seed.*** All the keys in this type of wallet are related to each other and can be generated again if one has the original seed. There are a number of different key derivation methods used in deterministic wallets. ***The most commonly used derivation method uses a tree-like structure and is known as a hierarchical deterministic or HD wallet.***

### Nondeterministic (Random) Wallets

***In the first bitcoin wallet (now called Bitcoin Core), wallets were collections of randomly generated private keys.*** For example, the original Bitcoin Core client pregenerates 100 random private keys when first started and generates more keys as needed, using each key only once. ***such wallets are being replaced with deterministic wallets because they are cumbersome to manage, back up, and import.*** The disadvantage of random keys is that if you generate many of them you must keep copies of all of them, meaning that the wallet must be backed up frequently. Each key must be backed up, or the funds it controls are irrevocably lost if the wallet becomes inaccessible.

### Deterministic (Seeded) Wallets

***Deterministic, or “seeded,” wallets are wallets that contain private keys that are all derived from a common seed, through the use of a one-way hash function.*** The seed is a randomly generated number that is combined with other data, such as an index number or “chain code” to derive the private keys.

***In a deterministic wallet, the seed is sufficient to recover all the derived keys, and therefore a single backup at creation time is sufficient.*** The seed is also sufficient for a wallet export or import, allowing for easy migration of all the user’s keys between different wallet implementations.

### HD Wallets(BIP-32/BIP-44)

***Deterministic wallets*** were developed to make it easy to ***derive many keys from a single “seed.”***  ***The most advanced form of deterministic wallets is the HD wallet defined by the BIP-32 standard.*** HD wallets contain ***keys derived in a tree structure, such that a parent key can derive a sequence of children keys, each of which can derive a sequence of grandchildren keys, and so on, to an infinite depth.***

HD wallets offer two major advantages over random (nondeterministic) keys.

- ***First, the tree structure can be used to express additional organizational meaning, such as when a specific branch of subkeys is used to receive incoming payments and a different branch is used to receive change from outgoing payments.*** Branches of keys can also be used in corporate settings, allocating different branches to departments, subsidiaries, specific functions, or accounting categories.
- ***The second advantage of HD wallets is that users can create a sequence of public keys without having access to the corresponding private keys***. This allows HD wallets to be used on an insecure server or in a receive-only capacity, issuing a different public key for each transaction. ***The public keys do not need to be preloaded or derived in advance, yet the server doesn't have the private keys that can spend the funds.***

### Seeds and Mnemonic Codes (BIP-39)

HD wallets are a very powerful mechanism for managing many keys and addresses.

Example)

```tex
0C1E24E5917779D297E14D45F14E1A1A
army van defense carry jealous true
garbage claim echo media make crunch
```

### Wallet Best Practice

As bitcoin wallet technology has matured, certain common industry standards have emerged that make bitcoin wallets broadly interoperable, easy to use, secure, and flexible.

> interoperable : 상호 정보 교환이 가능한
>
> mnemonic : (기억을 돕는) 연상기호

- Mnemonic code words, based on BIP-39
- HD wallets, based on BIP-32
- Multipurpose HD wallet structure, based on BIP-43
- Multicurrency and multiaccount wallets, based on BIP-44



## Wallet Technology Details

### Mnemonic Code Words(BIP-39)

Mnemonic code words are word sequences that represent (encode) a random number used as a seed to derive a deterministic wallet. The sequence of words is sufficient to re-create the seed and from there re-create the wallet and all the derived keys.

A wallet application that implements deterministic wallets with mnemonic words will show the user a sequence of 12 to 24 words when first creating a wallet. That sequence of words is the wallet backup and can be used to recover and re-create all the keys in the same or any compatible wallet application. 

>Mnemonic words are often confused with “brainwallets.” They are not the same. The primary difference is that a brainwallet consists of words chosen by the user, whereas mnemonic words are created randomly by the wallet and presented to the user.

