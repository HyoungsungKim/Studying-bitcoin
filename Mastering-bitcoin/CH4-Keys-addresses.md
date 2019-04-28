# CH 4 Keys, Addresses

You may have heard that bitcoin is based on cryptography, which is a branch of mathematics used extensively in computer security. ***Ironically, encryption is not an important part of bitcoin, as its communications and transaction data are not encrypted and do not need to be encrypted to protect the funds.*** In this chapter we will introduce some of the cryptography used in bitcoin to control ownership of funds, in the form of keys, addresses, and wallets.



## Introduction

Ownership of bitcoin is established through digital keys, bitcoin addresses, and digital signatures. ***The digital keys are not actually stored in the network,*** but are instead created and stored by user in a file, or simple database, called a ***wallet***. The digital keys in a user's wallet are ***completely independent of the bitcoin protocol*** and ***can be generated and managed by the user's wallet software without reference to the blockchain or access to the Internet.***

***Most bitcoin transactions requires a valid digital signature to be included in the blockchain, which can only be generated with a secret key;*** therefore, anyone with a copy of that key has control of the bitcoin. The digital signature used to spend funds is also referred to as a *witness*, a term used in cryptography. *The witness data in a bitcoin transaction testifies to the true ownership of the funds being spent.* 



Public key

- Similar to a bank account
- public key is represented by its fingerprint called bitcoin address

However, ***not all bitcoin addresses represent public keys;*** they can also represent other beneficiaries such
as scripts, as we will see later in this chapter.

> Beneficiaries  : 수혜자

Private key

- similar to the PIN key or signature on a check, that provides control over the account

The public key can be calculated from the private key, so storing only the private key is also possible.



### Public Key Cryptography and Cryptocurrency

Bitcoin uses elliptic curve multiplication as the basis for its cryptography.

In bitcoin, we use public key cryptography to create a key pair that controls access to bitcoin. ***The key pair consists of a private key and—derived from it—a unique public key.*** The public key is used to receive funds, and the private key is used to sign transactions to spend the funds. 

When spending bitcoin, ***the current bitcoin owner presents her public key and a signature*** (different each time, but *created from the same private key*) in a transaction to spend those bitcoin.



### Private and Public Keys

From the private key, we use elliptic curve multiplication, a one-way cryptographic function, to generate a public key (K). From the public key (K), we use a one-way cryptographic hash function to generate a bitcoin address (A).

> Private key -> elliptic curve  -> generate public key -> hash function -> bitcoin address
>
> It is one way process so ***It is impossible to assume private key from bitcoin address***



### Why use Asymmetric Cryptography (public/private keys) ?

It’s not used to “encrypt” (make secret) the transactions. Rather, ***the useful property of asymmetric cryptography is the ability to generate digital signatures.*** This useful property of asymmetric cryptography makes it possible for anyone to ***verify every signature on every transaction, while ensuring that only the owners of private keys can produce valid signatures.***



### Private Keys

A private key is simply a number, picked at random. ***The private key is used to create signatures that are required to spend bitcoin by proving ownership of funds used in a transaction.*** 

### Generating a private key from a random number

Creating a bitcoin key is essentially the same as “Pick a number between 1 and $$2^{256}$$” The exact method you use to pick that number does not matter as long as it is not predictable or repeatable.

More precisely, the private key can be any number between 1 and n - 1, where n is a constant (n = 1.158 * $10^{77}$, slightly less than $2^{256}$) defined as the order of the elliptic curve used in bitcoin.  To create such a key, we randomly pick a 256-bit number and ***check that it is less than n - 1, we have a suitable private key. Otherwise, we simply try again with another random number.***

> Example of private key
>
> (HEX) $1E99423A4ED27608A15A2616A2B0E9E52CED330AC530EDCC32C8FFC6A526AEDD$ 
>
> Each number is 4 bits

In bitcoin core client, to generate private key, use *getnewaddress*. For secure reasons, it displays the public key only, not the private key.



### Public Keys

The public key is calculated from the private key using elliptic curve multiplication, which is irreversible: $K = k * G$

- k is the private key
- G is a constant point called the generator point
- K is the resulting public key

The reverse operation, known as ***“finding the discrete logarithm”***—calculating k if you know K—is as difficult as trying all possible values of k, i.e., ***a brute-force search***

- Elliptic curve multiplication is a type of function that cryptographers call a “trap door” function: ***It is easy to do in one direction (multiplication) and impossible to do in the reverse direction (division).***



### Elliptic Curve Cryptography Explained

Elliptic curve cryptography is a type of asymmetric or public key cryptography based on the discrete logarithm problem as expressed by addition and multiplication on the points of an elliptic curve. ***Bitcoin uses a specific elliptic curve and set of mathematical constants.***

$y^2 = (x^3 + 7) \, over\, \Bbb (F_p)$

or

$y^2\, mod\,  p = x^3 + 7\, mod\, p$

![Elliptic curve](.\img\Secp256k1.png)



The mod p (modulo prime number p) indicates that this curve is over a finite field of ***prime order p*** , also written as $\Bbb F_p$, where $p = 2^{256} – 2^{32} – 2^9 – 2^8 – 2^7 – 2^6 – 2^4 – 1$, a ***very large prime number*** .

In elliptic curve math, there is a point called the “point at infinity,” which roughly corresponds to the role of zero in addition. On computers, it’s sometimes represented by x = y = 0 (which doesn’t satisfy the elliptic curve equation, but it’s an easy separate case that can be checked).

Given ***two points $P_1$ and $P_2$ on the elliptic curve***, there is a ***third point $P_3 = P_1 + P_2$, also on the
elliptic curve.*** 

- $P_3$ and x-axis reflection of $P_3$ is output (i.e (x, y), (x, -y))

> $P_n = (x, y)$

Geometrically, this third point $P_3$ is calculated by drawing a line between $P_1$ and $P_2$. This line will intersect the elliptic curve in exactly one additional place.

If $P_1$ and $P_2$ are the same point, the line “between” $P_1$ and $P_2$ should extend to be the tangent on the curve at this point $P_1$. This tangent will intersect the curve in exactly one new point.

![https://steemit.com/ellipticcurve/@sso/calculate-bitcoin-publickey](.\img\same-point.png)

(Image from https://steemit.com/ellipticcurve/@sso/calculate-bitcoin-publickey)

In some cases (i.e., if $P_1$ and $P_2$ have the same x values but different y values), the tangent line will be exactly vertical, in which case $P_3$ = “point at infinity”

> tangent line : 접선
>
> $P_1$ 과 $P_2$가 같은 x, 다른 y일때 $P_3$은 무한대 위에 있다.(point at infinity)

If $P_1$ is the “point at infinity,” then $P_1$ + $P_2$ = $P_2$. Similarly, if $P_2$ is the point at infinity, then $P_1$ + $P_2$ = $P_1$. This shows how the point at infinity plays the role of zero.

### Generating a Public Key

Starting with a private key in the form of a randomly generated number k, we multiply it by a predetermined point on the curve called the generator point G to produce another point somewhere else on the curve, which is the corresponding public key K.

> How G is predetermined?

***The generator point is specified as part of the secp256k1 standard and is always the same for all keys in bitcoin***: $K = k * G$ 

- k is the private key

- G is the generator point

  ->$G = 02 79BE667E F9DCBBAC 55A06295 CE870B07 029BFCDB 2DCE28D9 59F2815B 16F81798$

  (compressed form)

- K is the resulting public key, a point on the curve.

- ***Result(public key) is (x, y) pair***

- final result is $04 + string(x) + string(y)$ -> 1 hexa is 4bit so total 520bit (8 + 256 + 256)

- It is too bit so compressed form is used -> y is ommited

> y is odd
>
> -> $02 + string(x)$
>
> y is even
>
> -> $03 + string(x)$ 

> In elliptic curve, * is one way operator is reverse(divide) is impossible
>
> -> That is why we can not guess private key by using public key

***Because the generator point is always the same for all bitcoin users, a private key k multiplied with G will always result in the same public key K.*** The relationship between k and K is fixed, but can only be calculated in one direction, from k to K. ***That’s why a bitcoin address (derived from K) can be shared with anyone and does not reveal the user’s private key (k).***



## Bitcoin Address

A bitcoin address is a string of digits and characters that can be shared with anyone who wants to send you money. Addresses produced from public keys consist of a string of numbers and letters, beginning with the digit “1.”

- Example : $1J7mdg5rbQyUHENYdx39WVWK7fsLpEoXZy$ 

If we compare a bitcoin transaction to a paper check, the bitcoin address is the beneficiary, which is what we write on the line after “Pay to the order of.”

> beneficiary : 수혜자, 수령인

On a paper check, that beneficiary can sometimes be the name of a bank account holder, but can also include corporations, institutions, or even cash. ***Because paper checks do not need to specify an account, but rather use an abstract name as the recipient of funds,*** they are very flexible payment instruments. ***Bitcoin transactions use a similar abstraction, the bitcoin address, to make them very flexible.***

A bitcoin address can represent the owner of a private/public key pair, or it can represent something
else, such as a payment script, as we will see in ***“Pay-to-Script-Hash (P2SH)”*** 

The bitcoin address is derived from the public key through the use of one-way cryptographic hashing.

Starting with the public key K, we compute the SHA256 hash and then compute the RIPEMD160 hash of the result, producing a 160-bit (20-byte) number:

- SHA : Secure Hash Algorithm
- RIPEMD : RACE Integrity Primitives Evaluation Message Digest

```
A = RIPEMD160(SHA256(K))
```

- ***A bitcoin address is not the same as a public key. Bitcoin addresses are derived from a public key using a one-way function.*** 

***Bitcoin addresses are almost always encoded as “Base58Check”***, which uses 58 characters (a Base58 number system) and a checksum to help human readability, avoid ambiguity, and protect against errors in address transcription and entry. ***Base58Check is also used in many other ways in bitcoin***, whenever there is a need for a user to read and correctly transcribe a number, such as a bitcoin address, a private key, an encrypted key, or a script hash.

![public key to bitcoin address](.\img\process-to-bitcoin-address.png)





### Base58 and Base58Check Encoding

In order to represent long numbers in a compact way, using fewer symbols, many computer systems use mixed-alphanumeric representations with a base (or radix) higher than 10.

Base64 is most commonly used to add binary attachments to email. ***Base58 is a text based binary-encoding format developed for use in bitcoin and used in many other cryptocurrencies.***(Base58 is a subset of Base64)

```
Example of bitcoin's Base58 address
123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz
```

To add extra security against typos or transcription errors, Base58Check is a Base58 encoding format, frequently used in bitcoin, which has a built-in error-checking code. ***The checksum is an additional four bytes added to the end of the data*** that is being encoded. ***The checksum is derived from the hash of the encoded data and can therefore be used to detect and prevent transcription and typing errors.***

 we compute the “double-SHA” checksum, meaning we apply the SHA256 hash algorithm twice on the previous result (prefix and data):

```
checksum = SHA256(SHA256(prefix+data))
```

From the resulting 32-byte hash (hash-of-a-hash), we take only the first four bytes. These four bytes serve as the error-checking code, or checksum. The checksum is concatenated (appended) to the end.

 The result is composed of three items: ***a prefix, the data, and a checksum***. This result is encoded using the Base58 alphabet

![Base58 check](.\img\Base58Check.bmp)

***This result is a address which can receive a bitcoin & It is one way function so cannot guess public key by using this.***

In bitcoin, most of the data presented to the user is Base58Check-encoded to make it compact, easy to read, and easy to detect errors.

|             Type             | Version Prefix(hex) | Base58 result prefix |
| :--------------------------: | :-----------------: | :------------------: |
|       Bitcoin Address        |        0x00         |          1           |
|  Pay-to-Script-Hash Address  |        0x05         |          3           |
|   Bitcoin Testnet Address    |        0x6F         |        m or n        |
|       Private Key WIF        |        0x80         |      5, K, or L      |
| BIP-38 Encrypted Private Key |       0x0142        |          6P          |
|  BIT-32 Extended public Key  |     0x0488B21E      |         xpub         |



### Key Formats

Both private and public keys can be represented in a number of different formats. These representations all encode the same number, even though they look different.

#### Private Key formats

The private key can be represented in a number of different formats, all of which correspond to the same 256-bit number.

The WIF is used for import/export of keys between wallets and often used in QR code (barcode) representations of private keys.

*Private key representations (encoding formats)*

| Type           | Prefix | Description                                                  |
| -------------- | ------ | ------------------------------------------------------------ |
| Raw            | None   | 32 bytes                                                     |
| Hex            | None   | 64 Hexadeciaml digits                                        |
| WIF            | 5      | Base58check encoding:<br />Base58 with version prefix of 128-and 32-bit checksum |
| WIF-compressed | K or L | As above, with added suffix 0x01 before encoding             |

***-> One private key can be encoded differently***

#### Public key formats

Public keys are also presented in different ways, usually as either [***compressed or uncompressed public keys.***](###generating-a-public-key)

It is usually presented with the prefix 04 followed by two 256-bit numbers: one for the x coordinate of the point, the other for the y coordinate. The prefix 04 is used to distinguish uncompressed public keys from compressed public keys that begin with a 02 or a 03.

#### Compressed Private Keys

Ironically, the term “compressed private key” is a misnomer, ***because when a private key is exported as WIF-compressed it is actually one byte longer than an “uncompressed” private key.***

> misnomer : 부적절한

That is because the ***private key has an added one-byte suffix, which signifies that the private key is from a newer wallet and should only be used to produce compressed public keys.***

- Private keys are not themselves compressed and cannot be compressed.
- The term *“compressed private key”* really means ***“private key from which only compressed public keys should be derived”***
- *“uncompressed private key”* really means ***“private key from which only uncompressed public keys should be derived."***
- ***You should only refer to the export format as “WIF-compressed” or “WIF”*** and not refer to the private key itself as “compressed” to avoid further confusion



> WIF : Well informed format
>
> Private key to WIF
>
> 1. private key 
>2. add a 0x80 byte in front of it, add a 0x01 byte at the end 3.
> 3. perform SHA-256 
> 4. perform SHA-256 again 
> 5. Take the first 4 bytes of the second SHA-256 hash, this is checksum 
> 6. Add 4 checksum bytes from point 5 at the end of the extended key from point 2
> 7. Convert result from a byte sting into a base58 string using Base58Check encoding. This is wallet import Format



| Format         | Private key                                                  |
| -------------- | ------------------------------------------------------------ |
| Hex            | $1E99423A4ED27608A15A2616A2B0E9E52CED330AC530EDCC32C8FFC6A526AEDD$ |
| WIF            | $5J3mBbAH58CpQ3Y5RNJpUKPE62SQ5tfcvU2JpbnkeyhfsYB1Jcn$        |
| Hex-compressed | $1E99423A4ED27608A15A2616A2B0E9E52CED330AC530EDCC32C8FFC6A526AEDD01$ |
| WIF-compressed | $KxFC1jmwwCoACiCAWZ3eXa96mBM6tb3TYzGmf6YwgdGWZgawvrtJ$       |

Remember, these formats are not used interchangeably. ***In a newer wallet that implements compressed public keys, the private keys will only ever be exported as WIF-compressed*** (with a K or L prefix).

## Advanced Keys and Addresses

we will look at advanced forms of keys and addresses, such as:

- Encrypted private keys
- script and multisignature addresses
- vanity addresses
- paper wallets.

### Encrypted Private Keys(BIP - 38)

Private keys must remain secret.  But what if the backup itself is stolen or lost? These conflicting
security goals led to the introduction of a portable and convenient standard for encrypting private keys in a way that can be understood by many different wallets and bitcoin clients, standardized by ***BIP-38***

> BIP(Bitcoin Improvement Proposals) : Bitcoin Improvement Proposals are design documents providing information to the bitcoin community, or for describing a new feature for bitcoin or its processes or
> environment.

BIP-38 proposes a common standard for encrypting private keys with a passphrase and encoding them with Base58Check so that they can be stored securely on backup media, transported securely between wallets, or kept in any other conditions where the key might be exposed.

> passphrase : 일반적으로 디지털 서명이나 암화화, 복호화에 사용되는 패스워드보다 긴 문자열로 된 비밀번호.

A BIP-38 encryption scheme takes as input a bitcoin private key, usually encoded in the WIF, as a Base58Check string with the prefix of “5.” Additionally, the BIP-38 encryption scheme takes a *passphrase—a long password—usually composed of several words or a complex string of alphanumeric characters.* 

> private key(prefix 5)  -> BIP-38 encryption -> Base58Check-encoded encrypted private key (prefix 6p)

The result of the BIP-38 encryption scheme is a Base58Check-encoded encrypted private key that begins with the prefix 6P. ***If you see a key that starts with 6P, it is encrypted and requires a passphrase in order to convert (decrypt) it back into a WIF-formatted private key (prefix 5) that can be used in any wallet.*** Many wallet applications now ***recognize BIP-38- encrypted private keys and will prompt the user for a passphrase to decrypt and import the key.*** Third-party applications, such as the incredibly useful browser-based Bit Address (Wallet Details tab), can be used to decrypt BIP-38 keys.

| Private key(WIF)      | 5J3mBbAH58CpQ3Y5RNJpUKPE62SQ5tfcvU2JpbnkeyhfsYB1Jcn        |
| --------------------- | ---------------------------------------------------------- |
| PassPharase           | MyTestPassphrase                                           |
| Encrypted Key(BIP-38) | 6PRTHL6mWa48xSopbU1cKrVjpKbBZxcLRRCdctLJ3z5yxE87MobKoXdTsJ |

### Pay-to-Script Hash(P2SH) and Multisig Addresses

As we know, traditional bitcoin addresses begin with the number “1” and are derived from the public key, which is derived from the private key. ***Although anyone can send bitcoin to a “1” address, that bitcoin can only be spent by presenting the corresponding private key signature and public key hash.***

***Bitcoin addresses that begin with the number “3” are pay-to-script hash (P2SH) addresses***, sometimes erroneously called multisignature or multisig addresses. They designate the beneficiary of a bitcoin transaction as the hash of a script, instead of the owner of a public key.

> designate : 임명하다
>
> beneficiary : 수혜자

Unlike transactions that “send” funds to traditional “1” bitcoin addresses, also known as a pay-to-public-key-hash (P2PKH), funds sent to “3” addresses require something more than the presentation of one public key hash and one private key signature as proof of ownership.

A P2SH address is created from a transaction script, which defines who can spend a transaction output.

Encoding a P2SH address involves using the same double-hash function as used during creation of a bitcoin address.(same process with (public key - bitcoin address))

```
script hash = RIPEMD160(SHA256(script))
```

The resulting “script hash” is encoded with Base58Check with a version prefix of 5, which results in an encoded address starting with a 3.

#### Multisignature addresses and P2SH

Currently, the most common implementation of the P2SH function is the multisignature address script.

As the name implies, the underlying script requires more than one signature to prove ownership and therefore spend funds. The bitcoin multi-signature feature is designed to require M signatures (also known as the “threshold”) from a total of N keys, known as an M-of-N multisig, where M is equal to or less than
N.

This would be similar to a “joint account” as implemented in traditional banking where either spouse can spend with a single signature.

> If there is 3 multi-signature, then at least need 2 or more signature to withdraw

### Vanity Address

> Vanity : 자만심, 허영심, 헛됨, 무의미

***Vanity addresses are valid bitcoin addresses that contain human-readable messages.***

For example, 1LoveBPzzD72PUXLzCkYAtGFYmK5vYNR33 is a valid address that contains the letters forming the word “Love” as the first four Base-58 letters.

Although there are some optimizations in the vanity generation algorithm, the process essentially involves picking a private key at random, deriving the public key, deriving the bitcoin address, and checking to see if it matches the desired vanity pattern, repeating billions of times until a match is found.

#### Generating vanity addresses

It’s important to realize that a bitcoin address is simply a number represented by symbols in the Base58 alphabet.

Generating a vanity address is a brute-force exercise: try a random key, check the resulting address to see if it matches the desired pattern, repeat until successful.

> [Vanity address](https://medium.com/coinbundle/vanity-addresses-857fa4fb44be)
>
> Private key를 이용하여 자신이 원하는 문자열로 시작하는 bitcoin address를 만듬
>
> EX) HS로 시작하는 bitcoin address
>
> 방법 1. 직접 HS가 나올때까지 bitcoin address를 생성(문자열이 길어질수록 오래걸림)
>
> 방법 2. mining pool에 의뢰(맡김) -> 빠르지만 위험함(private key를 빼돌릴 수도 있음)

#### Vanity address security

***Vanity addresses can be used to enhance and to defeat security measures; they are truly a double-edged sword.*** Used to improve security, a distinctive address makes it harder for adversaries to substitute their own address and fool your customers into paying them instead of you. ***Unfortunately, vanity addresses also make it possible for anyone to create an address that resembles any random address, or even another vanity address, thereby fooling your customers.***

> Attack can make a similar address

### Paper Wallets

Paper wallets are a very effective way to create backups or offline bitcoin storage, also known as “cold storage.”

> Cold wallet : wallet which is not connected with internet
>
> Hot waller : wallet which is connected with internet

