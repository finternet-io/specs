# CLAUDE.md - Credential Token Schema

This file provides guidance to Claude Code when working with the Credential Token schema.

## Overview

The Credential Token schema extends the UNITS Token specification to support W3C Verifiable Credentials. It enables tokenization of identity verification results from third-party KYC providers like Signzy, Onfido, Jumio, etc.

## Token Hierarchy

CredentialToken extends NonFungibleToken (soulbound):

```
Token (base) - UNITS-TOKEN
    └── NonFungibleToken (UNITS-NFT)
            └── CredentialToken (UNITS-SBT)
```

**Token Standards (Array):**
```json
"tokenStandard": ["UNITS-SBT", "W3C-VC-2.0"]
```
- `UNITS-SBT` = Primary (soulbound token behavior)
- `W3C-VC-2.0` = Credential data model compliance
- Optionally add `ERC-6239` for Ethereum interop

**Fixed Properties for Credentials:**
| Property | Value | Reason |
|----------|-------|--------|
| `balance` | `"1"` | NFT characteristic |
| `transferable` | `false` | Soulbound (cannot transfer) |
| `divisible` | `false` | NFT characteristic |
| `burnable` | `false` | Only issuer can revoke |
| `fungibility` | `nonFungible` | Each credential is unique |
| `decimals` | `0` | Atomic (no decimal places) |

## Key Concepts

### W3C Verifiable Credentials Compliance

The schema is fully compliant with:
- **W3C VC Data Model 1.1** - Core verifiable credentials specification
- **W3C VC Data Model 2.0** - Latest specification with validFrom/validUntil
- **W3C DID Core** - Decentralized identifiers for issuer/subject

### Extension Pattern

Finternet-specific extensions are namespaced under `finternet:` to maintain W3C compatibility:
- `finternet:credentialType` - Type of verification (identity, document, biometric, KYC)
- `finternet:verificationLevel` - Level of assurance (basic, standard, enhanced, premium)
- `finternet:VerificationProvider` - Third-party provider metadata

### Raw Response Storage

Complete provider responses are stored encrypted for:
- Audit trail and compliance
- Provider-specific data preservation
- Selective disclosure capabilities

## Directory Structure

```
schemas/credential/
├── v1/
│   ├── context.jsonld   # JSON-LD context (imports token context + W3C VC)
│   ├── vocab.jsonld     # RDF vocabulary definitions
│   ├── attributes.yaml  # OpenAPI schema with validation
│   └── README.md        # Detailed documentation
├── credential.jsonld    # Example credential token instance
├── credential.schema.json # JSON Schema validator
└── CLAUDE.md            # This file
```

## Common Tasks

### Adding a New Document Type

1. Update `vocab.jsonld` - Add to `DocumentType` enumeration:
   ```json
   { "@id": "finternet:newDocType" }
   ```

2. Update `attributes.yaml` - Add to `DocumentType` schema:
   ```yaml
   enum:
     - passport
     - newDocType  # Add here
   ```

3. Update `credential.schema.json` - Add to enum in `$defs/DocumentVerification`

### Adding a New Provider Mapping

1. Create mapping file in `tmp/`:
   ```
   tmp/{provider}-vc-mapping.jsonc
   ```

2. Document field mappings using the same format as `signzy-vc-mapping.jsonc`

3. Implement transformation logic in workflow service

### Creating a Credential Token

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/credential/v1/context.jsonld",
  "@type": "CredentialToken",
  "id": "urn:uuid:...",
  "metadata": {
    "tokenStandard": ["UNITS-SBT", "W3C-VC-2.0"],
    "name": "KYC Verification",
    "fungibility": "nonFungible",
    "decimals": 0,
    "credentialType": "kycVerification",
    "verificationLevel": "enhanced",
    "flags": {
      "transactable": true,
      "transferable": false,
      "divisible": false,
      "revocable": true,
      "burnable": false
    },
    "provider": { ... }
  },
  "claims": [
    {
      "@context": ["https://www.w3.org/2018/credentials/v1", ...],
      "type": ["VerifiableCredential", "KYCCredential"],
      "issuer": "did:web:finternet.io",
      "credentialSubject": { ... }
    }
  ],
  "identities": [...],
  "state": {
    "status": "active",
    "balance": "1"
  }
}
```

## Validation

```bash
# Validate example against JSON Schema
ajv validate -s credential/credential.schema.json -d credential/credential.jsonld

# Expand JSON-LD
jsonld expand credential/credential.jsonld
```

## Related Documentation

- **W3C VC Data Model 1.1**: https://www.w3.org/TR/vc-data-model/
- **W3C VC Data Model 2.0**: https://www.w3.org/TR/vc-data-model-2.0/
- **W3C DID Core**: https://www.w3.org/TR/did-core/
- **UNITS Token Schema**: ../token/v1/README.md
- **Provider Mapping**: ../../tmp/signzy-vc-mapping.jsonc

## Key Types

| Type | Description |
|------|-------------|
| `CredentialToken` | Soulbound NFT wrapping a verifiable credential (extends NonFungibleToken) |
| `CredentialMetadata` | Extended metadata with credential-specific fields (fungibility=nonFungible, decimals=0) |
| `CredentialState` | Runtime state including balance="1" and validity period |
| `VerifiableCredential` | W3C VC embedded in claims array |
| `VerificationProvider` | Third-party provider information |
| `DocumentVerification` | Document verification results |
| `BiometricVerification` | Biometric verification results |
| `IdentityEvidence` | W3C VC Evidence extension for identity |

## Naming Conventions

- **Credential Types**: camelCase enum values (e.g., `identityVerification`, `kycVerification`)
- **Verification Levels**: lowercase enum values (e.g., `basic`, `standard`, `enhanced`)
- **Document Types**: lowercase enum values (e.g., `passport`, `pan`, `aadhaar`)
