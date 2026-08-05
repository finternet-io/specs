# Finternet API Specifications

OpenAPI specifications for the Finternet Developer Platform.

## Specs

| Spec | Description |
|------|-------------|
| `registry-interfaces.yaml` | Chain Registry and Wallet Provider Registry |
| `accounts-interfaces.yaml` | Account management, identity, and account keys |
| `clients-interfaces.yaml` | API client registration and credentials |
| `delegations-interfaces.yaml` | Delegation policies and authorization |
| `key-management-interfaces.yaml` | Cryptographic key lifecycle management |
| `token-interfaces.yaml` | Token lifecycle, transactions, and token classes |
| `token-class-config-interfaces.yaml` | Mapping between a token class and its token program (hooks, overrides) |
| `token-program-interfaces.yaml` | Token program registry |
| `scopes-interfaces.yaml` | RBAC scope catalogue and scope-approval requests |
| `terms-interfaces.yaml` | Terms of service and consent |
| `workflows-interfaces.yaml` | Durable (Restate) workflow execution and status |
| `adapter-interface.yaml` | Chain adapter interface |

## API Design

All APIs follow a consistent **envelope pattern** for protocol-agnostic communication:

**Request Envelope:**
```json
{
  "context": {
    "id": "api.token.transact",
    "version": "1.0",
    "ts": "2025-01-04T10:00:00Z",
    "msgId": "unique-message-id",
    "developerToken": "fnt_...",
    "authorization": "Bearer user_jwt..."
  },
  "payload": { },
  "signature": {
    "key_id": "0f8fad5b-d9cb-469f-a165-70867728950e",
    "jws": "base64-ed25519-signature-over-jcs(payload)"
  }
}
```

**Response Envelope:**
```json
{
  "context": {
    "status": "successful"
  },
  "response": { }
}
```

## Authentication

Two-level security model:

1. **Platform-Level (Developer Authentication)**
   - `context.developerToken` — B2B Developer API Key/Token
   - `context.developerSignature` — B2B Developer Signature (RFC 9421)

2. **User-Level (End-User Authorization)**
   - `context.authorization` — End-user JWT session token
   - `signature` (top-level) — End-user JWS for critical operations

## Async Transaction Pattern

Token operations are asynchronous:

1. **Submit** — `POST /v1/token/transact` → `202 Accepted` with `txId`
2. **Poll** — `POST /v1/transaction/status` → check status
3. **Complete** — `POST /v1/transaction/get` for full details

The base transaction `status` starts at `pending`; `POST /v1/transaction/status` returns it along with lifecycle timestamps (`submitted`, `started`, `completed`, `finalized`, `failed`). Federated transactions expose an extended lifecycle (`prepared`, `committing`, `committed`, …) via `POST /v1/transactions/status`.
