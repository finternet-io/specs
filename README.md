# Finternet Specifications

Open specifications for the Finternet protocol — API contracts and JSON-LD schemas that define how applications interact with the Finternet developer platform.

To learn more about the Finternet concept:

- [The Vision for the Future Financial System](http://bit.ly/finternet-vision) (BIS Working Paper)
- [Technology Vision and Architecture](http://bit.ly/finternet-tech)

## Repository Structure

```
specs/
├── api/                        # OpenAPI 3.x API specifications
│   ├── registry-interfaces.yaml      # Chain & Wallet Provider Registry
│   ├── accounts-interfaces.yaml      # Account management & identity
│   ├── clients-interfaces.yaml       # API client registration
│   ├── delegations-interfaces.yaml   # Delegation & authorization
│   ├── key-management-interfaces.yaml # Cryptographic key management
│   ├── token-interfaces.yaml         # Token lifecycle & transactions
│   └── adapter-interface.yaml        # Chain adapter interface
│
└── schemas/                    # JSON-LD schema definitions
    ├── core/v1/                # Core primitives (Entity, Identifier)
    ├── account/v1/             # Account domain
    ├── token/v1/               # Token instances (UNITS)
    ├── token-class/v1/         # Token class templates (FT, NFT, Credential)
    ├── credential/v1/          # W3C Verifiable Credentials
    └── transaction/v1/         # Transaction & audit records
```

## API Specifications

The `api/` directory contains OpenAPI specs for the Finternet Developer Platform, organized into four domains:

| Domain | Spec | Description |
|--------|------|-------------|
| **Registry** | `registry-interfaces.yaml` | Chain and wallet provider registry lookups |
| **Access & Identity** | `accounts-interfaces.yaml` | Account creation, retrieval, and identity management |
| | `clients-interfaces.yaml` | API client registration and credential management |
| | `delegations-interfaces.yaml` | Delegation policies and authorization |
| | `key-management-interfaces.yaml` | Cryptographic key lifecycle |
| **Tokens** | `token-interfaces.yaml` | Token operations (mint, burn, transfer, freeze, lock), transaction tracking |
| **Chains** | `adapter-interface.yaml` | Chain adapter interface for multi-ledger support |

## Schemas

The `schemas/` directory contains JSON-LD schema definitions following a **core-extension pattern**. Schemas define the semantic data model for all Finternet entities — accounts, tokens, token classes, credentials, and transactions.

See [`schemas/README.md`](schemas/README.md) for the full architecture and usage guide.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to contribute.

## License

This work is licensed under [CC BY-NC-SA 4.0](LICENSE.md).
