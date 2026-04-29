# TokenClass Base Schema v1

This directory contains the base TokenClass schema for the Finternet UNITS specification.

## Overview

**TokenClass** is a template/definition that describes a category of tokens. Individual Token instances reference a TokenClass and inherit its static configuration.

## TokenClass vs Token

| Aspect | TokenClass | Token |
|--------|------------|-------|
| Purpose | Template/definition | Instance |
| Fields | ~15 fields | ~10 fields |
| Supply | totalSupply, maxSupply, circulatingSupply | (none) |
| Balance | (none) | balance |
| Relationships | (none) | dependsOn, hasDependents |
| Identities | issuer, creator, manager | owner, operator, custodian |

## TokenClass Types

```
TokenClass (base)
    ├── FungibleTokenClass (UNITS-FT)
    │   ├── decimals: 0-18
    │   ├── divisible: true
    │   └── transferable: true
    │
    └── NonFungibleTokenClass (UNITS-NFT)
        ├── decimals: 0
        ├── divisible: false
        ├── transferable: true
        │
        └── CredentialTokenClass (UNITS-SBT)
            ├── transferable: false (soulbound)
            ├── revocable: true
            ├── burnable: false
            └── claims: W3C Verifiable Credentials
```

## Key Fields

| Field | Required | Description |
|-------|----------|-------------|
| `tokenClassId` | Yes | Unique identifier (URN or URI) |
| `tokenStandard` | Yes | Array of standards (first = primary) |
| `name` | Yes | Human-readable name |
| `symbol` | Yes | Short ticker symbol (e.g., "USDC") |
| `decimals` | Yes | Decimal precision (0 for NFTs, 0-18 for FTs) |
| `fungibility` | Yes | fungible, nonFungible, or semiFungible |
| `identities` | Yes | Class-level roles (issuer, creator, manager) |
| `flags` | No | Behavioral permissions |
| `supply` | No | Supply tracking (FTs only) |
| `programId` | No | Token program handling operations |

## Token Standards (Multi-Value Array)

The `tokenStandard` field is an array where the first element is the primary standard:

```json
["UNITS-FT"]                    // Simple fungible
["UNITS-FT", "ERC-20"]          // Fungible with Ethereum interop
["UNITS-NFT", "ERC-721"]        // NFT with Ethereum interop
["UNITS-SBT", "W3C-VC-2.0"]     // Soulbound credential
```

## Behavioral Flags

| Flag | FT Default | NFT Default | Credential Default |
|------|------------|-------------|-------------------|
| `transactable` | true | true | true |
| `transferable` | true | true | **false** |
| `divisible` | true | **false** | **false** |
| `locked` | false | false | false |
| `revocable` | false | varies | **true** |
| `burnable` | true | true | **false** |

## Class-Level Identities

TokenClass supports these identity types:

| Type | Description |
|------|-------------|
| `issuer` | Entity that issues tokens of this class |
| `creator` | Original creator of the token class |
| `manager` | Entity managing token class configuration |
| `controller` | Entity with control over the token class |

Note: Instance-level identities (owner, operator, custodian) are on the Token, not TokenClass.

## Example

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/token-class/v1/context.jsonld",
  "@type": "FungibleTokenClass",
  "tokenClassId": "urn:finternet:token-class:usdc",
  "tokenStandard": ["UNITS-FT", "ERC-20"],
  "name": "USD Coin",
  "symbol": "USDC",
  "decimals": 6,
  "fungibility": "fungible",
  "description": "USD-pegged stablecoin",
  "flags": {
    "transactable": true,
    "transferable": true,
    "divisible": true,
    "locked": false,
    "revocable": false,
    "burnable": true
  },
  "identities": [
    { "id": "did:web:circle.com", "type": "issuer" }
  ],
  "supply": {
    "totalSupply": "50000000000000000",
    "maxSupply": null,
    "circulatingSupply": "50000000000000000"
  },
  "stateModel": "proxy",
  "programId": "reference-ft"
}
```

## Files

| File | Purpose |
|------|---------|
| `context.jsonld` | JSON-LD context definitions |
| `vocab.jsonld` | RDF vocabulary definitions |
| `attributes.yaml` | OpenAPI schema with validation |
| `README.md` | This documentation |

## Related

- [FungibleTokenClass](../fungible/README.md)
- [NonFungibleTokenClass](../non-fungible/README.md)
- [CredentialTokenClass](../credential/README.md)
- [Token Instance Schema](../../../token/v1/README.md)
