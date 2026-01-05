# Finternet Schema Specifications

This directory contains JSON-LD schema definitions for the Finternet protocol, following JSON-LD best practices with a layered architecture pattern.

## Architecture

The schema follows a **core-extension pattern**:

```
schema/
├── core/              # Core schema domain
│   └── v1/            # Version 1 of core schema
│       ├── context.jsonld # Core JSON-LD context mappings
│       ├── vocab.jsonld   # RDF vocabulary definitions
│       ├── core.yaml      # OpenAPI 3.1 schema definitions
│       └── README.md      # Core schema documentation
│
└── account/           # Account domain schema
    ├── v1/            # Version 1 of account schema
    │   ├── context.jsonld   # Account-specific context (imports core)
    │   ├── vocab.jsonld     # Account vocabulary definitions
    │   ├── attributes.yaml  # OpenAPI schema with x-jsonld annotations
    │   └── README.md        # Account schema documentation
    │
    ├── account.jsonld       # Example account instance
    └── account.schema.json  # JSON Schema validator (references v1/)
```

## Design Principles

### 1. **Separation of Concerns**
- **Core schemas** define base types, enumerations, and common properties
- **Domain schemas** extend core and add domain-specific properties
- Each schema is versioned independently

### 2. **JSON-LD Native**
- All schemas include proper `@context` definitions
- Properties map to established vocabularies (schema.org, W3C DID, etc.)
- Controlled vocabularies are defined as proper RDF classes

### 3. **Strict Typing with Enumerations**
- Enumerations are defined as `rdfs:Class` with `rdfs:subClassOf` `schema:Enumeration`
- Each enum value is a typed instance (e.g., `fin:PERSONAL` is of type `fin:EntityType`)
- This ensures semantic precision and enables SPARQL queries

### 4. **Multiple Representations**
- **context.jsonld** - JSON-LD context for term mapping
- **vocab.jsonld** - RDF vocabulary with `@graph` for semantic definitions
- **attributes.yaml** - OpenAPI 3.1 schema with validation rules and `x-jsonld` annotations
- **schema.json** - JSON Schema for validation with `$ref` to yaml definitions

### 5. **Progressive Enhancement**
- Start with minimal required properties
- Add optional properties as needed
- Use `additionalProperties` judiciously
- Leverage `flags` object for extensible custom attributes

## Core Schema (v1)

Provides lean, reusable primitives for all Finternet schemas:

### Generic Types
- **Identifier** - Structured addressing (value@domain pattern)
  - `identifierValue` - Local part (before @)
  - `identifierDomain` - Domain part (after @)

### Base Classes
- **Entity** - Base class with common properties
  - Core identifiers: `@id`, `@type`
  - Display: `name`, `description`
  - Address: `primaryAddress` (Identifier type)
  - Metadata: `labels` (maps to schema:additionalProperty for key-value metadata)
  - Timestamps: `created`, `modified`
  - State: `version`, `status`

**Design Philosophy**: Core contains **only truly generic primitives** reusable across all entity types (Account, Token, ACL, etc.). Domain-specific types remain in their respective schemas.

## Token Schema (v1)

Implements the **Universal Token Specification (UNITS)** for representing all types of financial assets as tokens.

### Key Features

- **UNITS Specification** - Four-section token structure
  - **Metadata** - Immutable token identity (name, symbol, issuer, standard)
  - **Data** - Mutable business data (reserves, jurisdiction, audit info)
  - **Claims** - Verifiable credentials (compliance certificates, attestations)
  - **State** - Current state and lifecycle (supply, ownership, status, locks)

- **Token Standards** - Support for multiple token types
  - `UNITS-FT` - Fungible tokens (stablecoins, securities)
  - `UNITS-NFT` - Non-fungible tokens (unique assets)
  - `UNITS-SFT` - Semi-fungible tokens (hybrid)

- **Lifecycle Management** - Comprehensive state tracking
  - Status: active, frozen, redeemed, burned, expired
  - Supply tracking: total supply, circulating supply
  - Lock states: escrow, collateral, compliance holds
  - Restrictions: jurisdiction, use cases

- **Verifiable Claims** - W3C Verifiable Credentials as first-class objects
  - Public, private, or protected visibility
  - Issuer attestations and proofs
  - Status tracking: asserted, verified, revoked, expired

### Token-Specific Types

The token schema defines comprehensive types for financial asset management:
- `TokenMetadata` - Immutable token identity and configuration
- `TokenState` - Current state, supply, ownership, locks
- `Claim` - W3C Verifiable Credential structure
- `Fungibility` - fungible, nonFungible, semiFungible
- `LifecycleStatus` - active, frozen, redeemed, burned, expired
- `ValuationType` - fixed, pegged, market

### Usage

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/token/v1/context.jsonld",
  "@type": "FinancialProduct",
  "id": "urn:uuid:...",
  "metadata": {
    "tokenStandard": "UNITS-FT",
    "name": "USD Coin",
    "symbol": "USDC",
    "decimals": 6,
    "fungibility": "fungible",
    "issuer": "did:web:circle.com",
    "valuationType": "fixed"
  },
  "data": {
    "reserveAccount": "did:web:circle.com:accounts:reserve-001",
    "jurisdiction": "US"
  },
  "claims": [
    {
      "@type": "VerifiableCredential",
      "issuer": "did:web:auditor.example",
      "credentialSubject": {
        "id": "urn:uuid:...",
        "reserveRatio": "100%"
      }
    }
  ],
  "state": {
    "status": "active",
    "supply": {
      "totalSupply": "10000000000000",
      "circulatingSupply": "8500000000000"
    }
  }
}
```

## Transaction Schema (v1)

Implements a two-tier transaction architecture for tracking and recording token operations.

### Key Features

- **Two-Tier Architecture**
  - **TransactionLog** - System-wide transaction envelope
  - **TokenTransaction** - Per-token state change record
  - **AuditEvent** - Admin-only tracking for compliance

- **State Commitments** - Cryptographic state tracking
  - `stateBefore` and `stateAfter` for each token transaction
  - Supports proof generation and verification
  - Optional state snapshots for debugging

- **Proof Integration**
  - Multiple proof types: ZK-SNARKs, Merkle proofs, TEE attestations
  - Batch proof support (multiple transactions → one proof)
  - References to ProofRecord entities

- **Workflow Orchestration**
  - Links to WorkflowInstance for multi-step operations
  - Separates transaction execution from orchestration logic

- **Ledger Anchoring**
  - Optional anchoring to external blockchains
  - Multiple anchors supported (Ethereum, Bitcoin, Solana)
  - Asynchronous anchoring (doesn't block completion)

### Transaction-Specific Types

**TransactionLog:**
- `ExecutionStatus` - submitted, pending, executing, completed, failed, rolled_back
- `ProofProfile` - zk-snark, zk-stark, merkle, tee-attestation, signature-bundle
- `LedgerAnchor` - External blockchain reference
- `ExecutionTimestamps` - Lifecycle timestamps

**TokenTransaction:**
- `OperationType` - mint, burn, transfer, freeze, unfreeze, lock, unlock, redeem, update, addClaim, revokeClaim
- `Participant` - Transaction participants with roles
- `StateReference` - State commitment + optional snapshot
- `AmountTransferred` - Quantity affected

**AuditEvent (Admin-Only):**
- `AuditEventType` - MetadataUpdated, DataCorrected, ClaimAttached, ClaimRevoked, StateOverride, PermissionChanged
- `ChangeDetails` - Array of field-level changes
- `Approval` - Multi-party approval records

### Usage

**Atomic Swap Example:**
```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/transaction/v1/context.jsonld",
  "@type": "TransactionLog",
  "txId": "urn:uuid:tx-002",
  "initiator": "did:web:alice.example",
  "tokenTransactions": [
    {
      "tokenTxId": "urn:uuid:tokentx-002",
      "tokenId": "usdc",
      "operation": "burn"
    },
    {
      "tokenTxId": "urn:uuid:tokentx-003",
      "tokenId": "usdt",
      "operation": "mint"
    }
  ],
  "status": "completed",
  "proofId": "urn:uuid:proof-001"
}
```

## Account Schema (v1)

Extends core Entity to represent user and organizational accounts:

### Key Features
- **Decentralized Identity** - DID support with cryptographic keys
- **Finternet Address** - `primaryAddress` using core `Identifier` type (e.g., `{identifierValue: "alice", identifierDomain: "finternet"}`)
- **Contact Information** - Primary mobile/email plus secondary contact methods with verification timestamps
- **Entity Classification** - Account-specific `EntityType` enumeration (PERSONAL/BUSINESS)
- **Account Status** - Account-specific `AccountStatus` enumeration (ACTIVE/SUSPENDED)
- **Contact Methods** - Account-specific `ContactMethod` type with `ContactMethodType` enum (EMAIL/MOBILE)
- **Preferences & Permissions** - Account-specific `Flags` type with messaging permissions, UI preferences, and notification settings
- **Aliases** - Alternative addresses using core `Identifier` type
- **Multi-Signature Support** - `controllers` array for delegated control

### Account-Specific Types
The account schema defines its own types that are specific to account management:
- `EntityType` - PERSONAL or BUSINESS classification
- `AccountStatus` - ACTIVE or SUSPENDED status
- `ContactMethodType` - EMAIL, MOBILE
- `ContactMethod` - Structured contact with verification
- `Flags` - Account preferences and feature flags

### Usage

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/account/v1/context.jsonld",
  "@type": "Account",
  "@id": "urn:uuid:...",
  "did": "did:key:...",
  "primaryAddress": {
    "identifierValue": "alice",
    "identifierDomain": "finternet"
  },
  "entityType": "PERSONAL",
  "status": "ACTIVE",
  "contactMethods": [
    {
      "type": "EMAIL",
      "value": "alice@example.com",
      "verified": true
    }
  ]
}
```

## Extending the Schema

To create a new domain schema:

1. **Create domain directory** under `schema/`
2. **Create version directory** (e.g., `v1/`)
3. **Define files**:
   - `context.jsonld` - Import core context, add domain terms
   - `vocab.jsonld` - Define domain classes and properties
   - `attributes.yaml` - OpenAPI schema with validation
   - `README.md` - Documentation
4. **Reference core types** - Use `$ref` to core enumerations and types
5. **Create examples** - Instance files showing usage
6. **Add validator** - JSON Schema file for validation

## Namespaces

| Prefix | Namespace | Purpose |
|--------|-----------|---------|
| `finternet` | `https://finternet-io.github.io/specs/schemas/account/v1#` | Account-specific terms |
| `finternet` | `https://finternet-io.github.io/specs/schemas/core/v1#` | Core Finternet terms |
| `schema` | `https://schema.org/` | Schema.org vocabulary |
| `didCore` | `https://www.w3.org/ns/did/v1#` | W3C Decentralized Identifiers |
| `sec` | `https://w3id.org/security#` | W3C Security vocabulary |
| `rdf` | `http://www.w3.org/1999/02/22-rdf-syntax-ns#` | RDF vocabulary |
| `rdfs` | `http://www.w3.org/2000/01/rdf-schema#` | RDF Schema |
| `xsd` | `http://www.w3.org/2001/XMLSchema#` | XML Schema datatypes |

## Tools & Validation

### JSON Schema Validation
```bash
# Validate an account instance against the schema
ajv validate -s account/account.schema.json -d account/account.jsonld
```

### JSON-LD Processing
```bash
# Expand JSON-LD to see full URIs
jsonld expand account/account.jsonld

# Compact with custom context
jsonld compact -c account/v1/context.jsonld account/account.jsonld
```

### RDF Conversion
```bash
# Convert to N-Triples
jsonld format -q account/account.jsonld

# Convert to Turtle
riot --output=turtle account/account.jsonld
```

## Best Practices

1. **Always version your schemas** - Use semantic versioning (v1, v2, etc.)
2. **Import, don't duplicate** - Reference core types via context import
3. **Document enumerations** - Use `rdfs:comment` for each enum value
4. **Provide examples** - Include realistic instance data
5. **Validate strictly** - Use JSON Schema for runtime validation
6. **Think semantically** - Map to established vocabularies when possible
7. **Use URNs for IDs** - Prefer `urn:uuid:` for entity identifiers
8. **Enable discovery** - Include proper `@type` and `@id` in all instances
9. **Follow naming conventions** - Use PascalCase for class names (Account, Entity), camelCase for property names (primaryAddress, entityType)

## References

- [JSON-LD 1.1 Specification](https://www.w3.org/TR/json-ld11/)
- [Schema.org](https://schema.org/)
- [W3C DID Specification](https://www.w3.org/TR/did-core/)
- [OpenAPI 3.1 Specification](https://spec.openapis.org/oas/v3.1.0)
- [RDF Schema](https://www.w3.org/TR/rdf-schema/)

## License

See LICENSE file in repository root.
