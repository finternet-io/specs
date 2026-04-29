# FungibleTokenClass Schema v1

Token class for fungible/interchangeable tokens (UNITS-FT).

## Overview

FungibleTokenClass defines the template for tokens where each unit is interchangeable with another. One USDC is the same as another USDC.

## Characteristics

| Property | Value | Description |
|----------|-------|-------------|
| `fungibility` | `fungible` | Always fungible |
| `decimals` | 0-18 | Precision for fractional amounts |
| `transferable` | `true` | Can transfer to others |
| `divisible` | `true` | Can split balance |
| `burnable` | `true` | Holder can burn |

## Examples

- **Stablecoins**: USDC, USDT, DAI
- **Governance tokens**: COMP, UNI
- **Utility tokens**: Reward points, credits
- **Wrapped assets**: WETH, WBTC

## Token Standards

```json
["UNITS-FT"]                    // Native Finternet FT
["UNITS-FT", "ERC-20"]          // With Ethereum interop
["UNITS-FT", "SPL-Token"]       // With Solana interop
```

## Example TokenClass

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/token-class/v1/context.jsonld",
  "@type": "FungibleTokenClass",
  "tokenClassId": "urn:finternet:token-class:usdc",
  "tokenStandard": ["UNITS-FT", "ERC-20"],
  "name": "USD Coin",
  "symbol": "USDC",
  "decimals": 6,
  "fungibility": "fungible",
  "flags": {
    "transactable": true,
    "transferable": true,
    "divisible": true,
    "locked": false,
    "revocable": false,
    "burnable": true
  },
  "identities": [
    { "id": "did:web:circle.com", "type": "issuer" }
  ],
  "supply": {
    "totalSupply": "50000000000000000",
    "circulatingSupply": "50000000000000000"
  },
  "programId": "reference-ft"
}
```

## Related

- [Base TokenClass](../base/README.md)
- [NonFungibleTokenClass](../non-fungible/README.md)
- [Token Instance](../../../token/v1/README.md)
