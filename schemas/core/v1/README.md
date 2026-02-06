# Finternet Core Schema v1

This directory contains the core schema definitions for the Finternet protocol. These schemas provide base types, enumerations, and common properties used across all domain-specific schemas.

## Files

- **context.jsonld** - JSON-LD context mapping core properties to schema.org and Finternet namespace IRIs
- **vocab.jsonld** - RDF vocabulary definitions for core classes and properties
- **core.yaml** - OpenAPI 3.1 schema definitions with JSON-LD annotations
- **README.md** - This file

## Core Concepts

### Identifier
Structured identifier for Finternet addressing:
- `identifierValue` - Local part of the identifier (before @)
- `identifierDomain` - Domain part of the identifier (after @)

Examples:
- `alice` → `{identifierValue: "alice", identifierDomain: "finternet"}`
- `bob@example.com` → `{identifierValue: "bob", identifierDomain: "example.com"}`

### Entity
Base class for all Finternet entities. Provides common properties:
- `@id` - Unique identifier (URN or URI)
- `@type` - JSON-LD type
- `name` - Display name
- `description` - Description
- `primaryAddress` - Identifier object representing the primary Finternet address
- `labels` - Key-value metadata labels (maps to schema:additionalProperty)
- `created` - Creation timestamp
- `modified` - Last modification timestamp
- `version` - Version
- `status` - Current status

## Design Philosophy

The core schema contains **only truly generic, reusable primitives** that can be used across all entity types in the Finternet system:

- **Identifier** - Any entity that needs a `value@domain` addressing pattern
- **Entity** - Base properties common to all entities (includes `labels` which maps to schema.org's `additionalProperty` for key-value metadata)

Domain-specific types (like EntityType, ContactMethod, Flags for accounts) are kept in their respective domain schemas. This follows the **YAGNI principle** (You Aren't Gonna Need It) - types are only extracted to core when proven to be needed by multiple domains.

## Usage

Domain-specific schemas (like Account) should:
1. Import the core context in their own context.jsonld
2. Extend Entity or reference core types in their schemas
3. Use core enumerations for standardized values

## Namespace

The core namespace is: `https://finternet-io.github.io/specs/schemas/core/v1#`

Prefix: `finternet`
