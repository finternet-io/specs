# Token Instance Schema v1

Lightweight token instance schema for the Finternet protocol.

## Overview

**Token** is an instance of a **TokenClass**. It references the class for static configuration and holds only instance-specific data (~10 fields vs ~15 for TokenClass).

## Token vs TokenClass

| Aspect | TokenClass | Token |
|--------|------------|-------|
| Purpose | Template/definition | Instance |
| Fields | ~15 fields | ~10 fields |
| Supply | totalSupply, maxSupply | (none) |
| Balance | (none) | **balance** |
| Relationships | (none) | **dependsOn, hasDependents** |
| Identities | issuer, creator, manager | **owner, operator, custodian** |

## Key Fields

| Field | Required | Description |
|-------|----------|-------------|
| `tokenId` | Yes | Unique instance identifier |
| `tokenClassId` | Yes | Reference to TokenClass template |
| `owner` | Yes | Denormalized owner address |
| `identities` | Yes | Instance-level roles |
| `state` | Yes | Runtime state (status, balance) |
| `relationships` | No | Token-to-token dependencies |
| `data` | No | Mutable business data |

## Instance-Level Identities

Token instances have different identity types than TokenClass:

| Type | Description |
|------|-------------|
| `owner` | Current holder of the token |
| `operator` | Delegated operator with permissions |
| `custodian` | Custodial holder (for custody scenarios) |

Note: Class-level identities (issuer, creator, manager) are on the TokenClass.

## Relationships

Tokens can have dependencies on other tokens:

| Type | Description | Example |
|------|-------------|---------|
| `dependsOn` | This token depends on another | LEASE → PROPERTY |
| `hasDependents` | Other tokens depend on this | PROPERTY → [LEASE, RENTAL_INCOME] |

## Balance

The `balance` field represents what the holder owns:

| Token Type | Balance | Example |
|------------|---------|---------|
| FungibleToken | Variable amount | "100500000" (100.5 USDC with 6 decimals) |
| NonFungibleToken | Always "1" | "1" |
| CredentialToken | Always "1" | "1" |

## Example: Fungible Token Instance

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/token/v1/context.jsonld",
  "@type": "Token",
  "tokenId": "urn:finternet:token:alice-usdc-001",
  "tokenClassId": "urn:finternet:token-class:usdc",
  "owner": "alice",
  "identities": [
    { "id": "alice", "type": "owner" }
  ],
  "state": {
    "status": "active",
    "balance": "100500000"
  },
  "createdAt": "2025-01-28T00:00:00Z"
}
```

## Example: NFT with Relationship

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/token/v1/context.jsonld",
  "@type": "Token",
  "tokenId": "urn:finternet:token:lease-001",
  "tokenClassId": "urn:finternet:token-class:property-lease",
  "owner": "bob",
  "identities": [
    { "id": "bob", "type": "owner" }
  ],
  "state": {
    "status": "active",
    "balance": "1",
    "effectiveFrom": "2025-01-01T00:00:00Z",
    "effectiveUntil": "2026-01-01T00:00:00Z"
  },
  "relationships": [
    {
      "type": "dependsOn",
      "targetTokenId": "urn:finternet:token:property-123",
      "description": "This lease is for property-123"
    }
  ],
  "createdAt": "2025-01-28T00:00:00Z"
}
```

## Example: Credential Token

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/token/v1/context.jsonld",
  "@type": "Token",
  "tokenId": "urn:finternet:token:kyc-alice-001",
  "tokenClassId": "urn:finternet:token-class:kyc-verification",
  "owner": "alice",
  "identities": [
    { "id": "alice", "type": "owner" }
  ],
  "state": {
    "status": "active",
    "balance": "1",
    "effectiveFrom": "2025-01-28T00:00:00Z",
    "effectiveUntil": "2026-01-28T00:00:00Z"
  },
  "createdAt": "2025-01-28T00:00:00Z"
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

- [Base TokenClass](../../token-class/v1/base/README.md)
- [FungibleTokenClass](../../token-class/v1/fungible/README.md)
- [NonFungibleTokenClass](../../token-class/v1/non-fungible/README.md)
- [CredentialTokenClass](../../token-class/v1/credential/README.md)
