# NfhVoucherTokenClass (v1)

A non-fungible token class for **NFH (National Financial Hub) vouchers** —
programmable, purpose-bound payment instruments backed 1:1 by a sponsoring entity
(government scheme, employer benefit, donor program).

Each voucher token carries a face value, a beneficiary, a redemption window, and
constraints (merchants, MCCs, geographies) on where it may be spent.

## Standards

`UNITS-NFH-VOUCHER` (primary), `UNITS-NFT` (parent surface).

## Default flags

| Flag | Default | Why |
|------|---------|-----|
| `transferable` | `false` | Vouchers are bound to a beneficiary |
| `divisible` | `false` | One voucher = one allocation |
| `revocable` | `true` | Sponsor can claw back unspent vouchers |
| `burnable` | `false` | Lifecycle is `revoke`, not `burn` |

## Operations

| Operation | Purpose |
|-----------|---------|
| `mint` | Pre-fund the sponsor's voucher pool — establishes capacity |
| `issue` | Allocate a voucher to a specific beneficiary |
| `redeem` | Spend a voucher (whole or partial) at a permitted merchant |
| `revoke` | Invalidate an outstanding voucher |

Each operation has a payload schema in [`attributes.yaml`](./attributes.yaml).

## Example: issue

```json
{
  "operation": "issue",
  "payload": {
    "beneficiary": "did:fi:beneficiary-12345",
    "faceValue": {
      "amount": "5000",
      "currency": "INR",
      "remaining": "5000"
    },
    "constraints": {
      "merchantCategories": ["5411", "5912"],
      "geographies": ["IN"],
      "validUntil": "2026-12-31T23:59:59Z",
      "maxRedemptions": 5
    },
    "externalRef": "scheme:pmgky:2026:lot-9821"
  }
}
```

## Example: redeem

```json
{
  "operation": "redeem",
  "payload": {
    "tokenId": "urn:finternet:token:nfh-voucher-7af3",
    "merchant": "did:fi:fairprice-store-42",
    "merchantCategory": "5411",
    "amount": "1250",
    "redeemedAt": "2026-04-08T11:23:00Z",
    "receiptRef": "RCPT-99821"
  }
}
```
