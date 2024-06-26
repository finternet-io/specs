# Finternet API Specs

Welcome to the Finternet API specs Documentation repository.

If you wish to learn more about the concept in detail, it is recommended to read the below BIS published papers:

- [The Vision for the Future Financial System](http://bit.ly/finternet-vision)
- [Technology Vision and Architecture](http://bit.ly/finternet-tech)

Or check out this Fireside Chat at the BIS Innovation Hub: [Fireside Chat](https://www.youtube.com/watch?v=2-ukiKchQsI)


# Use Cases

The Finternet Sandbox is being modeled for the use cases and status as below:

| Use Case                               | Sandbox Status |
| -------------------------------------- | -------------- |
| Domestic money transfer                | In progress    |
| Pledge land and obtain commercial loan | Yet to start   |
| Cross Border                           | Yet to start   |
| More use cases will be updated here    | Yet to start   |

# Approach

![Finternet API Specs](/static/finternetApiSpec.png)

The specifications are being defined from the perspective of an “Application” which enables users with various use cases by connecting the following parties or entities:

1. Application
2. Higher order workflow layer
3. Unified asset management platform
4. Token/Asset manager

### Application

The application layer provides the user interface to the finternet. It enables users with various workflows that can be performed across asset classes

### Higher order workflow layer

This layer facilitates the composability of Finternet ‘verbs’ by enabling new workflows that are made up of foundational asset management verbs. For example, a ‘trade’ operation could be composed of two ‘transfer’ operations.

### Unified Asset Management Platform

The platform comprises the ledger layer and the programmability layer. This facilitates bookkeeping and workflow management of tokenized assets.

### Token/Asset manager

An institution that is responsible for the issuance (tokenisation, detokenisation), management and synchronisation of a token with their private ledger

