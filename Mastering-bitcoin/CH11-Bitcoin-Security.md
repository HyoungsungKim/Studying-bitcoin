# CH11 Bitcoin Security

A bitcoin wallet, containing your keys, can be backed up like any file. It can be stored in multiple copies, even printed on paper for hard-copy backup.

## Security Principles

A bitcoin transaction authorizes only a specific value to a specific recipient and cannot be forged or modified. ***It does not reveal any private information, such as the identities of the parties, and cannot be used to authorize additional payments.*** Therefore, a bitcoin payment network does not need to be encrypted or protected from eavesdropping. In fact, you can broadcast bitcoin transactions over an open public channel, such as unsecured WiFi or Bluetooth, with no loss of security.

### Developing Bitcoin Systems Securely

The most important principle for bitcoin developers is decentralization. Most developers will be familiar with centralized security models and might be tempted to apply these models to their bitcoin applications, with disastrous results.

Another common mistake is to take transactions “off blockchain” in a misguided effort to reduce transaction fees or accelerate transaction processing.

### The Root of Trust

Traditional security architecture is based upon a concept called the *root of trust*, which is a trusted core used as the foundation for the security of the overall system or application.

Security architecture is developed around the root of trust as a series of concentric circles, like layers in an onion, extending trust outward from the center.

***Each layer builds upon the more-trusted inner layer using access controls, digital signatures, encryption, and other security primitives.*** As software systems become more complex, they are more likely to contain bugs, which make them vulnerable to security compromise. As a result, the more complex a software system becomes, the harder it is to secure. ***The root of trust concept ensures that most of the trust is placed within the least complex part of the system, and therefore least vulnerable, parts of the system, while more complex software is layered around it.***

***Bitcoin security architecture is different.*** In bitcoin, the consensus system creates a trusted public ledger that is completely decentralized. ***A correctly validated blockchain uses the genesis block as the root of trust, building a chain of trust up to the current block.*** Bitcoin systems can and should use the blockchain as their root of trust. ***If your application explicitly or implicitly vests trust in anything but the blockchain, that should be a source of concern because it introduces vulnerability.**

