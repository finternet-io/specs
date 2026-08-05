# UNITS MCP Tool Mapping and Delegation-Safety Audit (Draft)

## Context

This note is a public audit/alignment draft for C4GT DMP 2026 Issue `#749`:

- **Issue:** `MCP Server for UNITS API — Agentic Tooling and Gap Remediation`
- **Focus:** Workstream 1 (audit/remediation) and midpoint tool contracts

This draft is intentionally based only on currently public surfaces and the issue definition.

## Goals of this Document

1. Provide a clear MCP tool inventory aligned with the issue scope.
2. Define delegation-aware safety invariants for agent-facing tools.
3. Propose a structured error contract suitable for agent workflows.
4. Provide midpoint tool contracts for phased implementation.
5. Define a practical test matrix for quality and safety.

## Tool Inventory (Issue-Aligned)

### Midpoint tools

- `get_account_summary`
- `check_transfer_eligibility`
- `get_delegation_scope`

### Full issue tools

- `initiate_transfer`
- `subscribe_lifecycle_events`
- `present_credential_vp`

## Delegation-Safety Invariants

All MCP tools should preserve the following invariants:

1. Every tool call executes in a validated delegation context.
2. Scope is enforced server-side; out-of-scope operations fail explicitly.
3. Agents do not receive raw private keys or raw credential claim values.
4. Transfer commit paths cannot bypass a pre-flight eligibility evaluation.
5. Errors are structured and machine-usable.
6. Calls are audit-loggable against the acting account context.

## Proposed Error Contract

All MCP tool errors should follow:

```json
{
  "code": "SCOPE_EXCEEDED",
  "message": "Delegated scope does not allow this operation",
  "detail": {
    "operation": "initiate_transfer",
    "constraint": "token_class",
    "expected": "INR_TOKEN",
    "received": "USD_TOKEN"
  }
}
```

Suggested initial error enum:

- `UNAUTHORIZED`
- `INVALID_DELEGATION`
- `SCOPE_EXCEEDED`
- `VALIDATION_ERROR`
- `POLICY_CHECK_FAILED`
- `UPSTREAM_ERROR`
- `INTERNAL_ERROR`

## Midpoint Tool Contracts (Draft)

### 1) `get_account_summary`

**Purpose**
Provide a safe unified summary of account state for agent planning.

**Input (logical)**

- delegated account context
- optional summary filters (if supported safely)

**Output (logical)**

- safe account reference
- native token holdings summary
- proxy token holdings summary
- active delegations summary
- credential store summary (metadata only)
- optional scope-limitation metadata

**Must not expose**

- raw key material
- raw credential values
- unrestricted account internals outside scope

### 2) `check_transfer_eligibility`

**Purpose**
Dry-run policy check for a proposed transfer without committing state.

**Input (logical)**

- delegated account context
- destination reference
- token class / asset identifier
- amount
- policy-relevant metadata

**Output (logical)**

- explicit eligibility result
- policy evaluation result summary
- dependency pointer pass/fail summary (if available)
- credential-check summary (without raw claims)
- explicit non-commit confirmation

**Must not do**

- mutate state
- hide policy failures behind generic errors

### 3) `get_delegation_scope`

**Purpose**
Allow agents to self-check permission boundaries before action attempts.

**Input (logical)**

- delegated request context

**Output (logical)**

- current scope representation
- token/asset constraints
- amount/frequency/time constraints (if defined)
- delegation validity status

**Must not expose**

- raw credential secrets
- unrelated claims

## Full-Scope Tool Notes

### `initiate_transfer`

- must enforce pre-flight eligibility before any commit
- should return both pre-flight and commit outcomes in a structured form

### `subscribe_lifecycle_events`

- should be delegated-scope filtered
- should define event taxonomy and subscription boundaries

### `present_credential_vp`

- should construct/present proof material without revealing raw claim values
- should report policy satisfaction outcome, not private credential payloads

## Safety-Driven Non-Exposure Guidance

Operations should be intentionally withheld from MCP if they:

- reveal secret material
- bypass delegation/policy controls
- are unsafe for autonomous invocation without additional guardrails

These should be documented as explicit non-exposure decisions with rationale.

## Test Matrix

### Unit

- tool input/output schema validation
- error mapping to enum
- scope enforcement checks
- dry-run no-side-effect verification

### Integration

- request/response against dev UNITS surface
- delegation chain propagation and validation
- policy-evaluation behavior checks

### End-to-End (agent workflow)

At least two complete flows:

1. `get_account_summary` -> `get_delegation_scope` -> `check_transfer_eligibility`
2. `get_delegation_scope` -> `check_transfer_eligibility` -> `initiate_transfer` (post-midpoint)

## Current Public-Access Limitations

This draft does not claim endpoint-complete implementation mapping because:

- the MCP server implementation repo is not publicly visible from issue context
- full API coverage details may evolve as additional public specs are published

## Next Steps

1. Reconcile this draft with newly published OpenAPI or MCP surfaces.
2. Convert logical tool contracts into concrete JSON Schema contracts.
3. Use this as the baseline for Workstream 1 coverage tracking.
