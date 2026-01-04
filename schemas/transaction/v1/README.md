# Finternet Transaction Schema v1

This directory contains the schema definitions for Finternet transaction entities.

## Overview

The transaction schemas provide data models for tracking and recording token operations in the Finternet ecosystem. They follow a two-tier architecture:

1. **TransactionLog** - System-wide transaction envelope
2. **TokenTransaction** - Per-token state change records

## Architecture

### TransactionLog (System-Wide Envelope)

The TransactionLog represents a complete business transaction that may affect one or more tokens. It serves as:
- **Correlation Point**: Links related token operations together
- **Proof Anchor**: References cryptographic proofs covering multiple operations
- **Workflow Context**: Links to workflow orchestration
- **Ledger Anchor**: References external blockchain confirmations

**Key Properties:**
- `txId` - Globally unique transaction identifier
- `initiator` - Account that initiated the transaction
- `tokenTransactions` - References to TokenTransaction records
- `workflowInstanceId` - Optional workflow reference
- `status` - Execution status (submitted, executing, completed, failed, etc.)
- `timestamps` - Lifecycle timestamps (submitted, started, completed, finalized)
- `proofId` - Reference to cryptographic proof
- `ledgerAnchors` - External blockchain references

**Example Use Cases:**
- Atomic swap: burn USDC + mint FUSD (single TransactionLog, two TokenTransactions)
- Batch transfer: multiple transfers in one transaction
- Complex workflow: multi-step operations with approvals

### TokenTransaction (Per-Token Record)

The TokenTransaction records a single state change to a specific token. It provides:
- **State Tracking**: Before/after state commitments
- **Participant Recording**: Who was involved and in what role
- **Operation Details**: Type of operation and amounts affected
- **Proof References**: Links to validation proofs

**Key Properties:**
- `tokenTxId` - Unique identifier for this token transaction
- `txId` - Reference to parent TransactionLog
- `tokenId` - Token being operated on
- `operation` - Operation type (mint, burn, transfer, freeze, etc.)
- `participants` - Involved accounts and their roles
- `stateBefore` / `stateAfter` - State commitments with optional snapshots
- `amount` - Quantity affected (for fungible tokens)
- `proofId` - Proof validating this state change

**Supported Operations:**
- State-changing: `mint`, `burn`, `transfer`, `freeze`, `unfreeze`, `lock`, `unlock`, `redeem`
- Administrative: `updateData`, `updateMetadata`, `addClaim`, `revokeClaim`
- Read-only: `read`

### AuditEvent (Admin-Only)

The AuditEvent tracks administrative changes to token metadata, data, and claims. These events are generated internally for compliance, monitoring, and audit purposes and are not exposed via public APIs.

**Key Properties:**
- `auditEventId` - Unique identifier for this audit event
- `tokenId` - Token that was modified
- `actor` - Account or system that performed the action
- `eventType` - Type of administrative action
- `changeDetails` - Before/after snapshots of what changed
- `reason` - Justification for the change
- `approvals` - Required approvals for the change
- `timestamp` - When the event occurred

**Event Types:**
- `MetadataUpdated` - Token metadata was modified (e.g., name, symbol)
- `DataCorrected` - Token data was corrected (e.g., jurisdiction update)
- `ClaimAttached` - Verifiable claim was added to token
- `ClaimRevoked` - Verifiable claim was revoked
- `StateOverride` - Manual override of token state (emergency operations)
- `PermissionChanged` - Token permissions were modified

**Use Cases:**
- Compliance audit trails
- Regulatory reporting
- Change management tracking
- Security monitoring
- Forensic analysis

## Design Principles

### 1. Separation of Concerns
- **TransactionLog** handles business transaction coordination
- **TokenTransaction** handles individual token state changes
- Clear one-to-many relationship (one TransactionLog → many TokenTransactions)

### 2. State Commitments
- Every TokenTransaction includes cryptographic state commitments
- `stateBefore` and `stateAfter` enable proof generation
- Optional `stateSnapshot` for debugging and audit trails

### 3. Proof Integration
- Transactions reference ProofRecords via `proofId`
- Proofs can be batched (multiple transactions → one proof)
- Supports multiple proof types (ZK-SNARKs, Merkle proofs, TEE attestations)

### 4. Workflow Orchestration
- TransactionLog can reference WorkflowInstance
- Enables complex multi-step operations
- Separates transaction execution from orchestration logic

### 5. Ledger Anchoring
- Optional anchoring to external blockchains
- Multiple anchors supported (e.g., Ethereum + Polygon)
- Asynchronous anchoring (doesn't block transaction completion)

## Relationship to Other Entities

```
Account ──initiates──> TransactionLog
                            │
                            │ contains
                            ↓
                       TokenTransaction ──operates on──> Token
                            │                              │
                            │ validates                    │ admin changes
                            ↓                              ↓
                       ProofRecord                    AuditEvent (Admin-Only)
                            │                              │
                            │ anchors to                   │ tracks
                            ↓                              ↓
                       LedgerAnchor              ChangeDetails + Approvals
```

## Usage Patterns

### Pattern 1: Simple Transfer
```json
{
  "txId": "tx-001",
  "initiator": "alice",
  "tokenTransactions": [
    {
      "tokenTxId": "tokentx-001",
      "operation": "transfer",
      "fromAccount": "alice",
      "toAccount": "bob",
      "amount": {"amount": "100", "decimals": 6}
    }
  ],
  "status": "completed"
}
```

### Pattern 2: Atomic Swap
```json
{
  "txId": "tx-002",
  "initiator": "alice",
  "tokenTransactions": [
    {
      "tokenTxId": "tokentx-002",
      "tokenId": "token-usdc",
      "operation": "burn",
      "amount": {"amount": "100"}
    },
    {
      "tokenTxId": "tokentx-003",
      "tokenId": "token-fusd",
      "operation": "mint",
      "amount": {"amount": "100"}
    }
  ],
  "status": "completed",
  "proofId": "proof-001"
}
```

### Pattern 3: Workflow-Driven
```json
{
  "txId": "tx-003",
  "initiator": "alice",
  "workflowInstanceId": "workflow-inst-001",
  "tokenTransactions": [...],
  "status": "executing",
  "timestamps": {
    "submitted": "2025-01-04T14:30:00Z",
    "started": "2025-01-04T14:30:01Z"
  }
}
```

## Files

- `attributes.yaml` - OpenAPI 3.1.0 schema definitions with JSON-LD annotations
- `context.jsonld` - JSON-LD context mappings
- `vocab.jsonld` - RDF vocabulary definitions

## References

- [Entities.md](../../../Entities.md) - Entity relationships and design
- [TransactionLog Schema](../transaction-log.schema.json) - JSON Schema validator
- [TokenTransaction Schema](../token-transaction.schema.json) - JSON Schema validator
- [AuditEvent Schema](../audit-event.schema.json) - JSON Schema validator (admin-only)
- [Example TransactionLog](../transaction-log.jsonld) - Sample instance
- [Example TokenTransaction](../token-transaction.jsonld) - Sample instance
- [Example AuditEvent](../audit-event.jsonld) - Sample instance (admin-only)
