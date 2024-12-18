# Exploration of secure cryptographic interfaces

This working document contains notes about technical insights on the secure cryptographic interface (SCI). For context, see [Wallet Secure Cryptographic Commons](../README.md).

## Vidua Inside SCI

This is a high-level design for the SCI that a wallet instance uses to apply Vidua’s local WSCA agent. This local WSCA agent communicates to a remote HSM-backed WSCA service using an end-to-end encrypted protocol. Multiple authentication mechanisms are available to the wallet provider, including one based on [SCAL3](https://github.com/cleverbase/scal3). The local WSCA agent is available in native Android and iOS code and deployed in [Vidua Wallet](https://vidua.nl/en/user/).

### Value objects

The user receives a **voucher** from the wallet provider. The voucher is the capability to activate the WSCA configuration as an identification means of either an unverified user or a verified user, with a provider-specified authentication mechanism.

The user sets a **person identification number** (PIN) during activation, for subsequent user authentication.

The user provides a **WSCA instruction** which is a sequence of applicatives from a predefined instruction set. The reason for sequencing is that several wallet actions require multiple cryptographic operations, such as a combination of proving possession and decrypting attribute values.

The WSCA returns a **WSCA result** which is either an error message or a sequence of outputs resulting from successful execution of all WSCA instructions. As a side effect, the remote WSCA service updates its persisted state.

### The WSCA-Empower function

The user requests empowerment, which combines registration in the WSCA and activation of the wallet unit as an identification means. Optionally, the process includes the request to execute initial WSCA instructions, such as key generation.

```
Inputs:
- application, a reference to a WSCA configuration
- voucher, an opaque byte string enabling empowerment
- pin, an UTF-8 string representing the user PIN
- instruction, a WSCA instruction

Outputs:
- result, a WSCA result

def WSCA-Empower(application, voucher, pin, instruction)
```

### The WSCA-Confirm function

The user requests confirmation, which combines authentication and authorisation to execute WSCA instructions.

```
Inputs:
- application, a reference to a WSCA configuration
- pin, an UTF-8 string representing the user PIN
- instruction, a WSCA instruction

Outputs:
- result, a WSCA result

def WSCA-Confirm(application, pin, instruction)
```

### Instruction set

Instructions refer to managed keys using labels, encoded as UTF-8 strings. Support for Hierarchical Deterministic Keys is planned depending on the outcome of [Topic B](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/discussions/354). This change would affect the document authentication and trust evidence instructions.

#### Managed keys

```
ListLabels → labels
DestroyKey label → success
```

#### Symmetric data encryption keys

```
StoreData label → fresh-key
ReadData label → key
````

#### Document authentication keys

```
GenerateKeyPair label type → pk
````

where `type = ECDSA-P256 | EC-SDSA-P256 | ECDH-P256`.

##### Non-repudiable proof of possession

```
SignMessage label message → digital-signature-value
````

##### Plausibly deniable proof of possession

```
CreateSharedSecret label pk' → Z_AB
```

#### Trust evidence issuance

```
AttestKeyPair label nonce → attestation
```

## Related resources

- [Hierarchical Deterministic Keys](https://datatracker.ietf.org/doc/html/draft-dijkhuis-cfrg-hdkeys-01), draft-dijkhuis-cfrg-hdkeys-01
- [SecureArea](https://github.com/openwallet-foundation-labs/identity-credential/blob/b42a11648f3d3ddd1fb77286e9b39f35992d04cf/identity/src/commonMain/kotlin/com/android/identity/securearea/SecureArea.kt), Identity Credential 202408.1
- [SecureArea](https://github.com/eu-digital-identity-wallet/eudi-lib-ios-iso18013-data-model/blob/ef353c447c7716c1fe5f5905ff98e089f5a29d43/Sources/MdocDataModel18013/SecureArea/SecureArea.swift), eudi-lib-ios-iso18013-data-model v0.4.1
