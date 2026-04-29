# Credential Token Schema v1

This schema defines the **Credential Token** type, extending the UNITS Token Specification with W3C Verifiable Credentials compliance. It enables tokenization of identity verification results, KYC attestations, and other verifiable claims from third-party providers.

## Overview

Credential Tokens are specialized UNITS tokens that:

1. **Follow W3C VC Standards** - Full compliance with Verifiable Credentials Data Model 1.1/2.0
2. **Extend UNITS Tokens** - Inherit five-section structure (metadata, data, claims, identities, state)
3. **Non-Fungible by Design** - Each credential is unique and non-transferable (soulbound)
4. **Support Multiple Providers** - Flexible mapping for Signzy, Onfido, Jumio, etc.
5. **Preserve Raw Data** - Encrypted storage of complete provider responses
6. **Enable Privacy** - Selective disclosure of credential attributes

## Token Hierarchy

CredentialToken extends NonFungibleToken in the UNITS token hierarchy:

```
Token (base) - UNITS-TOKEN
    └── NonFungibleToken (UNITS-NFT)
            └── CredentialToken (UNITS-SBT)
                    ├── tokenStandard: ["UNITS-SBT", "W3C-VC-2.0"]
                    ├── balance: "1" (always)
                    ├── transferable: false (soulbound)
                    ├── divisible: false
                    └── burnable: false
```

**Note:** `tokenStandard` is an array. Credentials typically use:
- `UNITS-SBT` as primary (soulbound token)
- `W3C-VC-2.0` for credential data model compliance
- Optionally `ERC-6239` for Ethereum semantic soulbound interop

## Fungibility: Why Credentials are NFTs

Credentials are **always Non-Fungible Tokens (NFT)** because:

| Property | Value | Reason |
|----------|-------|--------|
| `fungibility` | `nonFungible` | Each credential is unique to its subject |
| `decimals` | `0` | Credentials are atomic (you have 1 or none) |
| `balance` | `"1"` | NFT characteristic - holder always has exactly 1 |
| `transferable` | `false` | Identity credentials are soulbound |
| `divisible` | `false` | Cannot split a credential |
| `burnable` | `false` | Holder cannot destroy, only issuer can revoke |

Unlike property NFTs (art, real estate), credentials are **soulbound** - they represent attestations about a specific person and cannot be transferred to another holder.

## W3C Verifiable Credentials Compliance

This schema implements:

| Standard | Version | Compliance |
|----------|---------|------------|
| [Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model/) | 1.1 | Full |
| [Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model-2.0/) | 2.0 | Full |
| [DID Core](https://www.w3.org/TR/did-core/) | 1.0 | Full |
| [Data Integrity](https://www.w3.org/TR/vc-data-integrity/) | 1.0 | Supported |

### Required W3C VC Properties

Every credential MUST include:

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://finternet-io.github.io/specs/schemas/credential/v1/context.jsonld"
  ],
  "type": ["VerifiableCredential", "IdentityCredential"],
  "issuer": "did:web:finternet.io",
  "issuanceDate": "2025-01-15T00:00:00Z",
  "credentialSubject": {
    "id": "did:web:user.example"
  }
}
```

### Optional W3C VC Properties

| Property | W3C VC 1.1 | W3C VC 2.0 | Purpose |
|----------|------------|------------|---------|
| `expirationDate` | Yes | Deprecated | When credential expires |
| `validFrom` | No | Yes | Start of validity period |
| `validUntil` | No | Yes | End of validity period |
| `credentialStatus` | Yes | Yes | Revocation check endpoint |
| `credentialSchema` | Yes | Yes | Schema for validation |
| `evidence` | Yes | Yes | Supporting evidence |
| `termsOfUse` | Yes | Yes | Usage restrictions |
| `refreshService` | Yes | Yes | Credential refresh |
| `proof` | Yes | Yes | Cryptographic signature |

## Finternet Extensions

Extensions are namespaced under `finternet:` to maintain W3C compatibility.

### Credential Types

| Type | Description |
|------|-------------|
| `identityVerification` | Full identity verification (document + biometric) |
| `documentVerification` | Document-only verification |
| `biometricVerification` | Biometric-only verification |
| `kycVerification` | Comprehensive KYC |
| `addressVerification` | Address proof verification |
| `ageVerification` | Age verification (privacy-preserving) |

### Verification Levels

Aligned with eIDAS assurance levels:

| Level | Description | Typical Checks |
|-------|-------------|----------------|
| `basic` | Self-attestation | Document upload only |
| `standard` | Document validation | OCR + format validation |
| `enhanced` | Biometric verification | Liveness + face match |
| `premium` | Government verification | Database checks + manual review |

### Provider Metadata

Track provider-specific information:

```json
{
  "provider": {
    "providerName": "Signzy",
    "providerId": "signzy-in-prod",
    "providerType": "kycProvider",
    "journeyId": "PJGR095SLR",
    "flowId": "eb589c5d-8f88-46ba-9299-fd7a47ed0078",
    "flowVersion": 1,
    "rawResponseRef": "urn:finternet:blob:signzy:PJGR095SLR",
    "rawResponseHash": "sha256:a1b2c3d4...",
    "encryptionKeyRef": "did:web:finternet.io#key-enc-001"
  }
}
```

### Raw Response Storage

The complete provider response is:
1. **Encrypted** with a key managed by the credential holder
2. **Stored** in secure blob storage (referenced by `rawResponseRef`)
3. **Hashed** for integrity verification (`rawResponseHash`)

This enables:
- Full audit trail
- Provider-specific data preservation
- Selective disclosure of attributes

## Document Types

| Type | Code | Description |
|------|------|-------------|
| `passport` | - | International passport |
| `nationalId` | - | National identity card |
| `driversLicense` | - | Driver's license |
| `pan` | PAN | India Permanent Account Number |
| `aadhaar` | - | India Aadhaar (UIDAI) |
| `voterId` | - | Voter identification card |

## Usage Examples

### KYC Credential from Signzy

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/credential/v1/context.jsonld",
  "@type": "CredentialToken",
  "id": "urn:uuid:cred-001",
  "metadata": {
    "tokenStandard": ["UNITS-SBT", "W3C-VC-2.0"],
    "name": "KYC Verification - PAN",
    "symbol": "KYC",
    "fungibility": "nonFungible",
    "decimals": 0,
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
    "provider": {
      "providerName": "Signzy",
      "providerId": "signzy-in-prod",
      "journeyId": "PJGR095SLR"
    }
  },
  "claims": [
    {
      "@context": ["https://www.w3.org/2018/credentials/v1"],
      "type": ["VerifiableCredential", "KYCCredential"],
      "issuer": "did:web:finternet.io",
      "issuanceDate": "2025-11-25T17:58:21Z",
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
  "identities": [
    { "id": "did:web:finternet.io", "type": "issuer" },
    { "id": "did:web:user.example", "type": "subject" }
  ],
  "state": {
    "status": "active",
    "balance": "1",
    "effectiveFrom": "2025-11-25T17:58:21Z",
    "effectiveUntil": "2026-11-25T17:58:21Z"
  }
}
```

## Provider Mappings

See the `tmp/` directory for provider-specific mapping documents:
- `signzy-vc-mapping.jsonc` - Signzy to W3C VC mapping

## Related Specifications

- [UNITS Token Specification](../token/v1/README.md)
- [W3C Verifiable Credentials Data Model 1.1](https://www.w3.org/TR/vc-data-model/)
- [W3C Verifiable Credentials Data Model 2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- [W3C DID Core](https://www.w3.org/TR/did-core/)
- [eIDAS Regulation](https://digital-strategy.ec.europa.eu/en/policies/eidas-regulation)

## Files

| File | Purpose |
|------|---------|
| `context.jsonld` | JSON-LD context for term mappings |
| `vocab.jsonld` | RDF vocabulary definitions |
| `attributes.yaml` | OpenAPI schema with validation |
| `README.md` | This documentation |
