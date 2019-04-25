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

- K is the resulting public key, a point on the curve.

***Because the generator point is always the same for all bitcoin users, a private key k multiplied with G will always result in the same public key K.*** The relationship between k and K is fixed, but can only be calculated in one direction, from k to K. ***That’s why a bitcoin address (derived from K) can be shared with anyone and does not reveal the user’s private key (k).***



## Bitcoin Address

