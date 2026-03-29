# Token definitions

This folder contains the schemas 'recommended' for representing different type of assets in the digital way.

If your digital asset is not found here, feel free to open a PR to add it.

## Available Schemas

| Schema | Token Type | Fungibility | Description |
| ------ | ---------- | ----------- | ----------- |
| [regulated-money.yaml](./regulated-money.yaml) | Regulated Money | Fungible | Fiat currency issued by regulated financial institutions (e.g., CBDC, tokenized bank deposits) |
| [land.yaml](./land.yaml) | Land | Non-Fungible | Tokenized land parcels backed by government land records |
| [art.yaml](./art.yaml) | Art | Non-Fungible (Self-Attested) | Self-tokenized artwork with optional third-party attestations |

## Schema Design Principles

- **Consistent structure**: All token schemas share common fields (`token_id`, `token_type`, `fungible`, `issuer`, `holder_id`, `status`, `allowed_verbs`)
- **Finternet verbs**: The `allowed_verbs` field maps to Finternet operations (`transfer`, `pledge`, `lease`, `nominate`, etc.)
- **DID-based identity**: Issuers and holders are identified using decentralized identifiers (DIDs)
- **OpenAPI 3.0.3**: All schemas follow the OpenAPI specification for interoperability
