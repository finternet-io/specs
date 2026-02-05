# CredentialTokenClass Schema v1

Token class for soulbound credential tokens (UNITS-SBT) with W3C Verifiable Credentials.

## Overview

CredentialTokenClass extends NonFungibleTokenClass for identity verification, KYC attestations, and other verifiable claims. These tokens are **soulbound** - they represent attestations about a specific person and cannot be transferred.

## Characteristics

| Property | Value | Description |
|----------|-------|-------------|
| `fungibility` | `nonFungible` | Always non-fungible |
| `decimals` | `0` | Credentials are atomic |
| `transferable` | **`false`** | Soulbound (cannot be transferred) |
| `divisible` | `false` | Cannot split a credential |
| `burnable` | **`false`** | Holder cannot destroy |
| `revocable` | **`true`** | Issuer can revoke |

## Token Standards

```json
["UNITS-SBT", "W3C-VC-2.0"]                // Standard credential
["UNITS-SBT", "W3C-VC-2.0", "ERC-6239"]    // With Ethereum interop
```

## W3C Verifiable Credentials

CredentialTokenClass is compliant with:

| Standard | Version | Description |
|----------|---------|-------------|
| W3C VC Data Model | 1.1 & 2.0 | Verifiable Credentials structure |
| W3C DID Core | 1.0 | Decentralized Identifiers |
| W3C Data Integrity | 1.0 | Cryptographic proofs |

## Credential Types

| Type | Description |
|------|-------------|
| `identityVerification` | Full identity check (document + biometric) |
| `documentVerification` | Document-only verification |
| `biometricVerification` | Liveness and face matching |
| `kycVerification` | Comprehensive KYC |
| `addressVerification` | Address proof verification |
| `ageVerification` | Age verification (privacy-preserving) |

## Verification Levels

Aligned with eIDAS assurance levels:

| Level | Description | Typical Checks |
|-------|-------------|----------------|
| `basic` | Self-attestation | Document upload only |
| `standard` | Document validation | OCR + format validation |
| `enhanced` | Biometric verification | Liveness + face match |
| `premium` | Government verification | Database checks + manual review |

## Provider Agnostic

This schema is **provider-agnostic**. It does not contain provider-specific fields like:
- Signzy journey IDs
- Onfido check IDs
- Jumio transaction references

Provider-specific mappings should be maintained in separate mapping documents (e.g., `tmp/signzy-vc-mapping.jsonc`).

## Example TokenClass

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/token-class/v1/credential/context.jsonld",
  "@type": "CredentialTokenClass",
  "tokenClassId": "urn:finternet:token-class:kyc-verification",
  "tokenStandard": ["UNITS-SBT", "W3C-VC-2.0"],
  "name": "KYC Verification",
  "symbol": "KYC",
  "decimals": 0,
  "fungibility": "nonFungible",
  "credentialType": "kycVerification",
  "verificationLevel": "enhanced",
  "flags": {
    "transactable": true,
    "transferable": false,
    "divisible": false,
    "locked": false,
    "revocable": true,
    "burnable": false
  },
  "identities": [
    { "id": "did:web:finternet.io", "type": "issuer" }
  ],
  "claims": [
    {
      "@context": ["https://www.w3.org/2018/credentials/v1"],
      "type": ["VerifiableCredential", "KYCCredential"],
      "issuer": "did:web:finternet.io",
      "issuanceDate": "2025-01-28T00:00:00Z",
      "credentialSubject": {
        "id": "did:web:user.example",
        "fullName": "John Doe"
      },
      "evidence": [
        {
          "type": ["IdentityEvidence"],
          "documentVerification": {
            "documentType": "pan",
            "documentCountry": "IN",
            "documentValid": true
          },
          "biometricVerification": {
            "livenessVerified": true,
            "livenessScore": 0.99
          }
        }
      ]
    }
  ],
  "programId": "reference-credential"
}
```

## Files

| File | Purpose |
|------|---------|
| `context.jsonld` | JSON-LD context (extends base + W3C VC) |
| `vocab.jsonld` | RDF vocabulary definitions |
| `attributes.yaml` | OpenAPI schema with validation |
| `README.md` | This documentation |

## Related

- [Base TokenClass](../base/README.md)
- [NonFungibleTokenClass](../non-fungible/README.md)
- [Token Instance](../../../token/v1/README.md)
- [W3C VC Data Model 2.0](https://www.w3.org/TR/vc-data-model-2.0/)
