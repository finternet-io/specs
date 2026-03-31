# Finternet UNITS — Schemas

This folder contains the schemas for representing different types of digital assets and credentials in the Finternet ecosystem.

## Credential Schemas

The following schemas define the W3C Verifiable Credentials 2.0 compliant data structures used by the UNITS credential token system for privacy-preserving compliance enforcement.

| Schema | Description |
| ------ | ----------- |
| [verifiable-credential.yaml](./verifiable-credential.yaml) | W3C VC 2.0 credential structure for UNITS — defines KYC, identity, and financial claims issued by Trust Providers |
| [verifiable-presentation.yaml](./verifiable-presentation.yaml) | W3C VC 2.0 Verifiable Presentation with selective disclosure and ZK predicate proof support |
| [transfer-policy-credential-requirement.yaml](./transfer-policy-credential-requirement.yaml) | Token Manager policy configuration for specifying accepted proof types (VC / VP / ZKP) per transfer |

## How Credentials Flow in UNITS

```
┌─────────────────┐     issues      ┌──────────────────────┐
│  Trust Provider  │ ──────────────► │  Verifiable          │
│  (KYC Authority) │                 │  Credential (VC)     │
└─────────────────┘                  └──────────┬───────────┘
                                                │
                                     stored in  │
                                                ▼
                                     ┌──────────────────────┐
                                     │  Holder's Credential  │
                                     │  Store (UNITS)        │
                                     └──────────┬───────────┘
                                                │
                              at transfer time  │  builds
                                                ▼
                    ┌───────────────────────────────────────────────┐
                    │  Proof for Policy Evaluator (one of):        │
                    │                                               │
                    │  • VC  — full credential (legacy)             │
                    │  • VP  — selected claims only (BBS+)          │
                    │  • ZKP — predicate proof (Longfellow-ZK /     │
                    │          Groth16 / SP1) e.g. "age > 18"       │
                    └───────────────────────────────────────────────┘
                                                │
                                     verified   │
                                                ▼
                                     ┌──────────────────────┐
                                     │  Transfer Policy     │
                                     │  Evaluator           │
                                     │  ✅ PASS / ❌ FAIL   │
                                     └──────────────────────┘
```

## Supported ZK Schemes

| Scheme | Type | Best For |
| ------ | ---- | -------- |
| BBS+ (`bbs-2023`) | Signature-based | Selective claim disclosure without revealing other fields |
| Google Longfellow-ZK | Purpose-built | Threshold proofs (age > 18, credit_score > 750) — used in Google Wallet |
| circom/Groth16 | Circuit-based | Custom arithmetic predicates |
| SP1 (Succinct) / Jolt (a16z) | ZKVM | General-purpose proofs compiled from Rust |

## References

- [W3C Verifiable Credentials Data Model 2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- [W3C VC Data Integrity BBS Cryptosuites](https://www.w3.org/TR/vc-di-bbs/)
- [Google Longfellow-ZK](https://github.com/nicebyte/niceshade)
- [IETF draft-google-cfrg-libzk](https://datatracker.ietf.org/doc/)
- [Finternet Technology Architecture](http://bit.ly/finternet-tech)

If your digital asset or credential type is not found here, feel free to open a PR to add it.
