# Exploration of secure cryptographic interfaces

This working document contains notes about technical insights on the secure cryptographic interface (SCI). For context, see [Wallet Secure Cryptographic Commons](../README.md).

## Example interface

By comparing multiple SCI specifiations, we may be able to come up with a harmonised one.

### Vidua Inside SCI

Vidua provides a Remote WSCD service for digital identity wallets. Below is a high-level overview the SCI that a wallet instance uses to apply the local WSCA agent of this service. This local WSCA agent communicates to a remote WSCA service using an end-to-end encrypted protocol. The remote WSCA service is backed by a stateless HSM application. The wallet provider can select from multiple authentication mechanisms, including one based on [SCAL3](https://github.com/cleverbase/scal3). The local WSCA agent is available in native Android and iOS code and deployed for example in [Vidua Wallet](https://vidua.nl/en/user/).

#### Value objects

The wallet provider provides a **voucher** to a user. The voucher is the capability to activate the WSCA configuration as an identification means of either an unverified user or a verified user, with a provider-specified authentication mechanism.

During activation, the user sets a **person identification number** (PIN) for subsequent user authentication and authorisation.

Each authentication and authorisation requires a **challenge**. This is a capability that may be used only once and may pose additional constraints, such as a time limit.

The user provides a **WSCA instruction** which is a sequence of applicatives from a predefined instruction set. The reason for sequencing is that several wallet actions require multiple cryptographic operations, such as a combination of proving possession and decrypting attribute values.

The WSCA returns a **WSCA result** which is either an error message or a sequence of outputs resulting from successful execution of all WSCA instructions. As a side effect, the remote WSCA service updates its persisted state.

#### Application interface

##### Empowerment

The user requests empowerment, which combines registration in the WSCA and activation of the wallet unit as an identification means. The process includes the request to execute zero or more initial WSCA instructions, such as key generation.

```
Inputs:
- application, a representation of a WSCA configuration
- voucher, an opaque byte string enabling empowerment
- pin, an UTF-8 string representing the user PIN
- instruction, a WSCA instruction

Outputs:
- result, a WSCA result

def empower(application, voucher, pin, instruction)
```

##### Confirmation

The user requests confirmation, which combines authentication and authorisation to execute WSCA instructions.

```
Inputs:
- application, a representation of a WSCA configuration
- challenge, an opaque byte string enabling confirmation
- pin, an UTF-8 string representing the user PIN
- instruction, a WSCA instruction

Outputs:
- result, a WSCA result

def confirm(application, challenge, pin, instruction)
```

#### Instruction set

The instruction set consists of applicatives, specified with user-provided inputs and expected WSCA outputs.

Not all instructions are supported yet. Support for Hierarchical Deterministic Keys (HDKeys) is planned depending on the outcome of [Topic B](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/discussions/354). Support for Proof of Assocation (PoA) may be added depending on the outcomes of [Topic C](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/issues/333) and [Topic K](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/issues/341).

##### Key management

Instructions refer to managed keys using labels, encoded as UTF-8 strings specified by the wallet instance.

```
Outputs:
- labels, a list of labels of user-managed keys

instruction ListLabels()
```

##### Symmetric data encryption keys

Key values are transmitted over the end-to-end encrypted channel to the HSM application.

```
Inputs:
- label, a label that is not yet assigned

Outputs:
- key, a fresh data encryption key

instruction StoreData(label)
```

```
Inputs:
- label, a label of a data encryption key

Outputs:
- key, the assigned data encryption key

instruction ReadData(label)
```

```
Inputs:
- label, a label of a data encryption key

instruction DeleteData(label)
```

##### Document authentication keys

```
Inputs:
- label, a label that is not yet assigned
- type, either ECDSA-P256 or EC-SDSA-P256 or ECDH-P256

Outputs:
- hdk, an root HDKey representation

instruction GenerateKeyPair(label, type)
```

```
Inputs:
- label, a label assigned to a document authentication key

instruction DestroyPrivateKey(label)
```

###### Non-repudiable proof of possession

```
Inputs:
- label, a label assigned to an ECDSA-P256 or EC-SDSA-P256 key
- hdk, an HDKey representation
- message, an opaque byte array

Outputs:
- signature, a digital signature value for proving possession

instruction SignMessage(label, hdk, message)
````

###### Plausibly deniable proof of possession

```
Inputs:
- label, a label assigned to an ECDH-P256 key
- hdk, an HDKey representation
- pk', the reader's public key

Outputs:
- Z_AB, the shared secret value FE2OS(x_S) with S = [sk]pk'

instruction CreateSharedSecret(label, hdk, pk')
```

##### Trust evidence issuance

```
Inputs:
- label, a label assigned to a document authentication key
- hdk, an HDKey representation
- nonce, an opaque byte string to be included in the attestation

Outputs:
- attestation, a byte string attesting WSCA binding of the key to the user

instruction AttestKeyPair(label, hdk, nonce)
```

## Related resources

- [Hierarchical Deterministic Keys](https://datatracker.ietf.org/doc/html/draft-dijkhuis-cfrg-hdkeys-01), draft-dijkhuis-cfrg-hdkeys-01
- [SecureArea](https://github.com/openwallet-foundation-labs/identity-credential/blob/b42a11648f3d3ddd1fb77286e9b39f35992d04cf/identity/src/commonMain/kotlin/com/android/identity/securearea/SecureArea.kt), Identity Credential 202408.1
- [SecureArea](https://github.com/eu-digital-identity-wallet/eudi-lib-ios-iso18013-data-model/blob/ef353c447c7716c1fe5f5905ff98e089f5a29d43/Sources/MdocDataModel18013/SecureArea/SecureArea.swift), eudi-lib-ios-iso18013-data-model v0.4.1
