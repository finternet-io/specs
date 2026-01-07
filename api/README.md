# Finternet APIs

This folder contains the API definitions for different layers involved in Finternet implementation.

## Available APIs

### Token Service API (`token-interfaces.yaml`)

Complete API specification for managing universal tokens (UNITS specification) with support for all lifecycle operations.

**Key Endpoints:**

#### Token Management
- **POST `/v1/token/get`** - Retrieve detailed token information
- **POST `/v1/token/search`** - Search tokens with filtering and pagination (owner inferred from JWT/ACL)
- **POST `/v1/token/transact`** - Execute token operations (async)

**Supported Operations:**
- `mint` - Create new token instances
- `burn` - Destroy tokens
- `transfer` - Transfer ownership
- `freeze`/`unfreeze` - Compliance holds
- `lock`/`unlock` - Escrow/collateral
- `redeem` - Redeem tokens
- `update` - Update token data or metadata
- `addClaim`/`revokeClaim` - Manage verifiable credentials

#### Transaction Tracking
- **POST `/v1/transaction/status`** - Poll async transaction status
- **POST `/v1/transaction/get`** - Get complete transaction details
- **POST `/v1/transaction/search`** - Search transactions with filters (initiator inferred from JWT/ACL)
- **POST `/v1/token/transactions`** - Get token transaction history

**API Design:**

All APIs follow a consistent **envelope pattern** for protocol-agnostic communication:

**Request Envelope:**
```json
{
  "context": {
    "id": "api.token.transact",
    "version": "1.0",
    "ts": "2025-01-04T10:00:00Z",
    "msgId": "unique-message-id",
    "developerToken": "dev_pk_...",
    "authorization": "Bearer user_jwt..."
  },
  "payload": {
    "operation": "mint",
    "metadata": { /* token metadata */ },
    "data": { /* token data */ }
  },
  "signature": {
    "type": "JsonWebSignature2020",
    "jws": "..."
  }
}
```

**Response Envelope (Async - 202 Accepted):**
```json
{
  "context": {
    "id": "api.token.transact",
    "status": "accepted",
    "transactionId": "urn:uuid:tx-..."
  },
  "response": {
    "txId": "urn:uuid:tx-...",
    "status": "submitted",
    "message": "Transaction submitted for processing"
  }
}
```

**Response Envelope (Success - 200 OK):**
```json
{
  "context": {
    "status": "successful"
  },
  "response": {
    /* operation results */
  }
}
```

**Response Envelope (Error):**
```json
{
  "context": {
    "status": "failed",
    "error": {
      "code": "INSUFFICIENT_BALANCE",
      "message": "Insufficient token balance for transfer"
    }
  },
  "response": {}
}
```

### Authentication & Authorization

Two-level security model:

1. **Platform-Level (Developer Authentication)**
   - `context.developerToken` - B2B Developer API Key/Token
   - `context.developerSignature` - B2B Developer Signature (RFC 9421)
   - Can be passed via HTTP headers: `Authorization: Bearer <token>` and `Signature: ...`

2. **User-Level (End-User Authorization)**
   - `context.authorization` - End-user JWT session token
   - `signature` (top-level) - End-user JWS for critical operations

### Async Transaction Pattern

Token operations are asynchronous to support proof generation and ledger anchoring:

1. **Submit** - Call `/v1/token/transact` → Returns `202 Accepted` with `txId`
2. **Poll** - Call `/v1/transaction/status` with `txId` → Check status progression
3. **Complete** - When status is `completed`, call `/v1/transaction/get` for full details

**Status Progression:**
```
submitted → pending → executing → completed/failed/rolled_back
```

## Account Service API (`accounts-interfaces.yaml`)

API specification for account management, identity, and access control.

*(Documentation to be expanded)*

## Contributing

Feel free to contribute by:
- Reviewing the APIs and providing feedback
- Suggesting improvements to the design
- Adding use-case specific APIs
- Reporting issues or inconsistencies
