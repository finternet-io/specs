# Finternet APIs

This folder contains the API definitions for different layers involved in Finternet implementation.

## Available Specs

| Spec | Layer | Description |
| ---- | ----- | ----------- |
| [ledger-interfaces.yaml](./ledger-interfaces.yaml) | Ledger | APIs for interacting with the underlying ledger infrastructure — identity management, smart contract deployment and execution |
| [token-manager-interfaces.yaml](./token-manager-interfaces.yaml) | Token Manager | APIs for token managers — tokenization/detokenization, token lifecycle operations (transfer, pledge, freeze), querying, and ledger synchronization |
| [application-interfaces.yaml](./application-interfaces.yaml) | Application | APIs for the application layer — user management, accounts, contacts, and asset management with Finternet verbs (transfer, pledge, lease, nominate) |

Feel free to contribute by reviewing the APIs, providing feedback on improvements, and bring usecase specific APIs (if any).
