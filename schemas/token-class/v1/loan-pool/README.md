# LoanPoolTokenClass (v1)

A non-fungible token class representing a **securitised loan pool**. Each token
instance models one pool with its own underlying receivables, performance metrics,
risk parameters, and lifecycle.

## Standards

`UNITS-LOAN-POOL` (primary), `UNITS-NFT` (parent surface).

## Schema highlights

| Field | Description |
|-------|-------------|
| `poolType` | `personal`, `mortgage`, `sme`, `microfinance`, `vehicle`, `education`, `mixed` |
| `originator` | Servicer / originator identity |
| `economics` | Original / outstanding principal, cumulative payouts, realised + projected IRR |
| `dpdBuckets` | Snapshot of loans bucketed by days-past-due |
| `rating` | Latest rating (agency, symbol, outlook, timestamp) |
| `fldg` | First-Loss Default Guarantee state (guarantor, coverage, consumed) |
| `closedAt` | Timestamp at which the pool was wound down (set by `pool_closed`) |

## Operations

| Operation | Purpose |
|-----------|---------|
| `mint` | Create a new pool with initial composition |
| `dpd_bucket_updated` | Refresh DPD bucket counts |
| `principal_update` | Update outstanding principal |
| `payout` | Record a distribution to holders |
| `irr_update` | Update realised / projected IRR |
| `fldg_update` | Update FLDG state |
| `pool_rating_updated` | Update credit rating |
| `pool_closed` | Mark pool wound down — terminates the token's life-cycle |

Each operation has a payload schema in [`attributes.yaml`](./attributes.yaml).

## Example: mint

```json
{
  "operation": "mint",
  "payload": {
    "poolType": "microfinance",
    "originator": "did:fi:acme-mfi",
    "economics": {
      "currency": "INR",
      "originalPrincipal": "100000000",
      "outstandingPrincipal": "100000000",
      "irrProjected": 0.18
    },
    "rating": {
      "agency": "CRISIL",
      "rating": "A1+",
      "outlook": "stable",
      "ratedAt": "2026-04-01T00:00:00Z"
    },
    "fldg": {
      "guarantor": "did:fi:guarantor-corp",
      "coverageAmount": "10000000",
      "consumedAmount": "0",
      "currency": "INR"
    }
  }
}
```
