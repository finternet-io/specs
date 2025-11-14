# Finternet Account Schema v1

This directory contains the account schema for the Finternet protocol. The account schema extends the core Entity schema and defines properties specific to user and organizational accounts.

## Files

- **context.jsonld** - JSON-LD context mapping account properties to Finternet, schema.org, DID, and security namespace IRIs
- **vocab.jsonld** - RDF vocabulary definitions for Account class and account-specific properties
- **attributes.yaml** - OpenAPI 3.1 schema definitions with JSON-LD annotations and validation rules
- **README.md** - This file

## Account Schema

The Account schema represents a user or organizational account in the Finternet system with the following key features:

### Core Properties

- **@id** - Unique URN identifier (e.g., `urn:uuid:...`)
- **@type** - Must be `Account`
- **did** - Decentralized Identifier (DID) for cryptographic verification
- **primaryAddress** - Unique Finternet address (e.g., `alice@finternet`) - inherited from core `Entity`
- **name** - Legal name of individual or business
- **email** - Primary contact email
- **phone** - Primary contact phone (E.164 format)
- **publicKey** - Root cryptographic public key

### Entity Classification

- **entityType** - Type of legal entity (account-specific enumeration):
  - `PERSONAL` - Individual person account
  - `BUSINESS` - Business or organizational account

### Contact Methods

- **contactMethods** - Array of secondary contact methods (account-specific type)
  - Each method has: `type` (EMAIL, PHONE, SMS, WHATSAPP), `value`, `verified`, `verifiedAt`, `primary`
  - Type enumeration: `EMAIL`, `PHONE`, `SMS`, `WHATSAPP`

### Preferences & Permissions

- **flags** - Structured preferences and feature flags (account-specific type)
  - Messaging permissions: `isWhatsAppSendingAllowed`, `isMarketingOptIn`, `isSMSAllowed`, `isEmailAllowed`
  - UI preferences: `userDefinedTheme` (light/dark/auto), `language` (ISO 639-1), `timezone` (IANA)
  - Notification settings: `notificationPreferences` object with email/push/sms booleans

### Advanced Features

- **controllers** - Array of controller DIDs for multi-signature or delegated control
- **aliases** - Array of alternative addresses using core `Identifier` type
  - Each alias has: `identifierValue`, `identifierDomain` (same structure as primaryAddress)

## Relationship to Core Schema

The Account schema:
1. Imports the core v1 context via context array
2. Extends `finCore:Entity` base class
3. Uses `finCore:Identifier` for primaryAddress property
4. Can use `finCore:Labels` for metadata (inherited from Entity)
5. Defines account-specific types: `EntityType`, `ContactMethodType`, `ContactMethod`, `Flags`

## Usage Example

```json
{
  "@context": "https://finternet.io/schema/account/v1/context.jsonld",
  "@type": "Account",
  "@id": "urn:uuid:018b41f8-37c8-7c83-9429-7a670d8a598a",
  "did": "did:key:z6MkpTHR8VNsBxYAAWHut2Geadd9jSwuBa82SsoPfnH4Ujp",
  "primaryAddress": {
    "identifierValue": "alice",
    "identifierDomain": "finternet"
  },
  "name": "Alice Smith",
  "email": "alice.smith@example.com",
  "phone": "+919876543210",
  "publicKey": "0x04ab3c5d7ef9a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6",
  "entityType": "PERSONAL",
  "flags": {
    "isWhatsAppSendingAllowed": true,
    "isMarketingOptIn": false,
    "userDefinedTheme": "dark",
    "language": "en",
    "timezone": "Asia/Kolkata"
  },
  "contactMethods": [
    {
      "type": "EMAIL",
      "value": "alice.recovery@gmail.com",
      "verified": true,
      "verifiedAt": "2025-11-10T10:30:00Z"
    }
  ],
  "aliases": [
    {
      "identifierValue": "alice_temp",
      "identifierDomain": "fin.act"
    }
  ]
}
```

## Namespace

The account namespace is: `https://finternet.io/schema/account/v1#`

Prefix: `finternet`

Core namespace: `https://finternet.io/schema/core/v1#`

Core prefix: `finternet` (referenced via full URL in vocab.jsonld)
