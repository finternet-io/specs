# Finternet Schema Specifications

This directory contains JSON-LD schema definitions for the Finternet protocol, following JSON-LD best practices with a layered architecture pattern.

## Architecture

The schema follows a **core-extension pattern** inspired by protocol-specifications-new:

```
schema/
├── core/v1/           # Base schema with common types and enumerations
│   ├── context.jsonld # Core JSON-LD context mappings
│   ├── vocab.jsonld   # RDF vocabulary definitions
│   ├── core.yaml      # OpenAPI 3.1 schema definitions
│   └── README.md      # Core schema documentation
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

## Account Schema (v1)

Extends core Entity to represent user and organizational accounts:

### Key Features
- **Decentralized Identity** - DID support with cryptographic keys
- **Finternet Address** - `primaryAddress` using core `Identifier` type (e.g., `{identifierValue: "alice", identifierDomain: "finternet"}`)
- **Contact Information** - Primary email/phone plus secondary contact methods with verification timestamps
- **Entity Classification** - Account-specific `EntityType` enumeration (PERSONAL/BUSINESS)
- **Contact Methods** - Account-specific `ContactMethod` type with `ContactMethodType` enum (EMAIL/PHONE/SMS/WHATSAPP)
- **Preferences & Permissions** - Account-specific `Flags` type with messaging permissions, UI preferences, and notification settings
- **Aliases** - Alternative addresses using core `Identifier` type
- **Multi-Signature Support** - `controllers` array for delegated control

### Account-Specific Types
The account schema defines its own types that are specific to account management:
- `EntityType` - PERSONAL or BUSINESS classification
- `ContactMethodType` - EMAIL, PHONE, SMS, WHATSAPP
- `ContactMethod` - Structured contact with verification
- `Flags` - Account preferences and feature flags

### Usage

```json
{
  "@context": "https://finternet.io/schema/account/v1/context.jsonld",
  "@type": "Account",
  "@id": "urn:uuid:...",
  "did": "did:key:...",
  "primaryAddress": {
    "identifierValue": "alice",
    "identifierDomain": "finternet"
  },
  "entityType": "PERSONAL",
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
| `finternet` | `https://finternet.io/schema/account/v1#` | Account-specific terms |
| `finternet` | `https://finternet.io/schema/core/v1#` | Core Finternet terms |
| `schema` | `https://schema.org/` | Schema.org vocabulary |
| `di` | `https://www.w3.org/ns/did/v1#` | W3C Decentralized Identifiers |
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

## Migration from Previous Schema

Previous schema used:
- Snake_case properties (`primary_address`, `entity_type`)
- Inline vocabulary definitions in context
- No versioning structure

New schema uses:
- CamelCase properties (`primaryAddress`, `entityType`)
- Separate vocabulary files with proper RDF definitions
- Version directories for evolution
- Strict typing with enumeration classes
- References to core shared types

To migrate existing data:
1. Rename properties from snake_case to camelCase
2. Update `@context` to reference versioned context URL
3. Convert `primaryAddress` from string to Identifier object: `"alice@finternet"` → `{"identifierValue": "alice", "identifierDomain": "finternet"}`
4. Ensure enum values match defined vocabulary (e.g., `PERSONAL`, `BUSINESS`)
5. Rename `masterPublicKey` to `publicKey`
6. Remove `verificationLevel`, `nationality`, `countryOfResidence`, `countryOfIncorporation` (these are runtime attributes, not core schema)
7. Update `contact_methods` to `contactMethods` with `verifiedAt` timestamp
8. Update `flags` to use strict schema with defined properties

## References

- [JSON-LD 1.1 Specification](https://www.w3.org/TR/json-ld11/)
- [Schema.org](https://schema.org/)
- [W3C DID Specification](https://www.w3.org/TR/did-core/)
- [OpenAPI 3.1 Specification](https://spec.openapis.org/oas/v3.1.0)
- [RDF Schema](https://www.w3.org/TR/rdf-schema/)

## License

See LICENSE file in repository root.
