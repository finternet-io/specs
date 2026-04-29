# NonFungibleTokenClass Schema v1

Token class for non-fungible/unique tokens (UNITS-NFT).

## Overview

NonFungibleTokenClass defines the template for tokens where each instance is unique and distinct. Unlike fungible tokens, NFTs are not interchangeable.

## Characteristics

| Property | Value | Description |
|----------|-------|-------------|
| `fungibility` | `nonFungible` | Always non-fungible |
| `decimals` | `0` | NFTs are atomic (no fractions) |
| `transferable` | `true` | Can transfer ownership |
| `divisible` | `false` | Cannot split an NFT |
| `burnable` | `true` | Holder can burn by default |

## Examples

- **Real Estate**: Property deeds, land titles
- **Certificates**: Diplomas, licenses, certifications
- **Collectibles**: Art, digital collectibles
- **Tickets**: Event tickets, access passes

## Token Standards

```json
["UNITS-NFT"]                   // Native Finternet NFT
["UNITS-NFT", "ERC-721"]        // With Ethereum interop
```

## Example TokenClass

```json
{
  "@context": "https://finternet-io.github.io/specs/schemas/token-class/v1/context.jsonld",
  "@type": "NonFungibleTokenClass",
  "tokenClassId": "urn:finternet:token-class:property-deed",
  "tokenStandard": ["UNITS-NFT"],
  "name": "Property Deed",
  "symbol": "DEED",
  "decimals": 0,
  "fungibility": "nonFungible",
  "flags": {
    "transactable": true,
    "transferable": true,
    "divisible": false,
    "locked": false,
    "revocable": false,
    "burnable": true
  },
  "identities": [
    { "id": "did:web:land-registry.gov", "type": "issuer" }
  ],
  "programId": "reference-nft"
}
```

## Subtypes

NonFungibleTokenClass has a subtype for soulbound tokens:

- **[CredentialTokenClass](../credential/README.md)**: Non-transferable (soulbound) credentials for identity verification, KYC, etc.

## Related

- [Base TokenClass](../base/README.md)
- [FungibleTokenClass](../fungible/README.md)
- [CredentialTokenClass](../credential/README.md)
- [Token Instance](../../../token/v1/README.md)
