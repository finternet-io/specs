# Finternet Token Schema v1

This directory contains the schema definitions for the Finternet UNITS Token Specification.

## Overview

The UNITS Token Specification is a universal, extensible data model for representing any tokenized asset, contract, or entitlement in the Finternet ecosystem. It supports both fungible and non-fungible assets while enabling cryptographic verifiability, privacy, and interoperability.

## Architecture

The token specification follows a four-part structure:

### 1. Metadata
Static descriptive identity and behavioral profile of the token. Includes:
- Token identifiers (name, symbol, decimals)
- Token class and standard information
- Issuer and creator identifiers
- Valuation model (fixed, pegged, market)
- State model (native or proxy)
- Behavioral flags (transferable, locked, revocable)

### 2. Data
Type-specific, extensible domain data. This section is open-ended and governed by the token class definition. Examples:
- Financial tokens: face value, ISIN, maturity info
- NFTs: artwork metadata, traits, attributes
- RWA tokens: property docs, custody info, appraisal data

### 3. Claims
Verifiable credentials and attestations about the token. Includes:
- Compliance certifications
- KYC/AML assertions
- Legal rights/ownership confirmations
- Credit ratings or audit attestations

Each claim follows W3C Verifiable Credentials format with cryptographic proofs.

### 4. State
Runtime lifecycle and status information. Includes:
- Lifecycle status (active, frozen, redeemed, burned, expired)
- Supply data (for fungible tokens)
- Ownership state (for NFTs)
- Lock conditions and restrictions
- Time-based validity windows
- Cryptographic state commitment

## Design Principles

1. **Identity-State Separation**: Token identity (metadata) is immutable or versioned separately from mutable state
2. **State as Verifiable Commitment**: All states can be cryptographically verified
3. **Claims as Verifiable Assertions**: Claims are first-class, cryptographically verifiable objects
4. **Lifecycle as State Model**: Lifecycle is formally expressed through state transitions
5. **Extensible Semantics**: Uses open standards (schema.org, W3C VC) for interoperability

## Files

- `context.jsonld` - JSON-LD context definitions (planned)
- `vocab.jsonld` - Vocabulary definitions (planned)
- `attributes.yaml` - Detailed attribute schemas (planned)

## References

- [Token Entity Spec](../../../Token%20Entity%20Spec.md) - Full specification document
- [JSON Schema](../token.schema.json) - JSON Schema validator
- [Example Token](../token.jsonld) - Example token instance
