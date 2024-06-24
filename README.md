# Finternet API Specs

Welcome to the Finternet API specs Documentation repository.

If you wish to learn more about the concept in detail, it is recommended to read the below BIS published papers:

- [The Vision for the Future Financial System](http://bit.ly/finternet-vision)
- [Technology Vision and Architecture](http://bit.ly/finternet-tech)

Or check out this Fireside Chat at the BIS Innovation Hub: [Fireside Chat](https://www.youtube.com/watch?v=2-ukiKchQsI)

# Ongoing Effort

To make this vision a reality, multiple stakeholders, volunteers, and organizations are working together on a developer sandbox for the Finternet.

The Finternet effort is being planned in three phases:

### Phase I (From now to Aug 2024) - Building the Sandbox

Establishing a sandbox for interested developers to try and provide feedback, and contribute back with more use cases which could be possible on the Finternet. Prototype user experiences will be built on the Finternet sandbox to provide decision-makers with a broader understanding of the concept and its possibilities.

### Phase II (Aug 2024 to Feb 2025) - Hackathon in US and ASEAN

A rolling hackathon will be conducted across the globe for more developers and organizations to participate and make the sandbox richer with more use cases.

### Phase III (Feb 2025 onwards) - Implementation phase

Working with interested governments and central bank stakeholders to turn the Finternet vision into a reality for specific target use cases.

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

The specifications are being defined from the perspective of an “Application” which enables users with various use cases by connecting to four parties:

1. Asset Managers
2. Unified Ledger
3. Trust Service Providers
4. Digital Infrastructure as defined in _Finternet: The Vision for the Future Financial System_

### Unified Asset Management Platform

The platform comprises the ledger layer and the programmability layer. This facilitates bookkeeping and workflow management of tokenized assets.

### Higher Order Verbs

This layer facilitates the composability of Finternet ‘verbs’ by enabling new workflows that are made up of foundational asset management verbs. For example, a ‘trade’ operation could be composed of two ‘transfer’ operations.
