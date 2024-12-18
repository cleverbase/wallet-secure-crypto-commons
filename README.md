# Wallet Secure Cryptographic Commons

> [!NOTE]
> This is not a supported Cleverbase product.

## Background

The [EU Digital Identity Regulation](https://data.europa.eu/eli/reg/2024/1183/oj) requires the availability of secure digital identity wallets. The [Commission Implementing Regulation (EU) 2024/2979](https://data.europa.eu/eli/reg_impl/2024/2979/oj) on integrity and core functionalities requires the use of one or more combinations of:

- a *wallet secure cryptographic device* (WSCD): a tamper-resistant device providing a secure execution environment for critical operations and protection of critical assets;
- a *wallet secure cryptographic application* (WSCA): an application of the cryptographic and non-cryptographic functions provided by the WSCD, managing critical assets on behalf of the user.

The [Architecture and Reference Framework version 1.4](https://eu-digital-identity-wallet.github.io/eudi-doc-architecture-and-reference-framework/1.4.0/arf/) specifies the need for a *secure cryptographic interface* (SCI) to the WSCA. In [Topic P](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/issues/346) the European Commission considers harmonising this interface.

Critical WSCA functions to expose at the SCI include:

- user management and authentication;
- key management and usage.

For requirements to the user management functions and authentication mechanism we refer to the [Commission Implementing Regulation (EU) 2015/1502](https://data.europa.eu/eli/reg_impl/2015/1502/oj) and to the risk register in Annex I of the [Commission Implementing Regulation (EU) 2024/2981](https://data.europa.eu/eli/reg_impl/2024/2981/oj). User management includes activating the wallet unit for e-identification of a verified user, for example by setting a PIN.

For key management and usage, the WSCA should provide an instruction set to wallet instances consuming the SCI.

## Exploration

This repository aims to collect community discussions and technical reports on WSCA and SCI design based on WSCD knowledge. The purpose is to increase the likelihood that the EU Digital Identity ecosystem achieves high security levels at scale, by:

- informing and validating WSCA product design;
- stimulating SCI standardisation, enabling an open internal market;
- sharing security knowlegde, informing decision makers.

The work has started within the community developing [Hierarchical Deterministic Keys for the European Digital Identity Wallet](https://github.com/sander/hierarchical-deterministic-keys), but may be relevant to different communities as well.

## Table of contents

- [Exploration of secure cryptographic interfaces](sci/README.md)
- [Exploration of wallet secure cryptographic applications](wsca/README.md)
- [Exploration of wallet secure cryptographic devices](wscd/README.md)

## Contributing

To enable reuse, new contributions to the technical reports not intended for standardisation must be provided under either [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) or [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/). For contributions to standardisation, contact [Sander](mailto:sander.dijkhuis@cleverbase.com) to set up a contributor agreement.
