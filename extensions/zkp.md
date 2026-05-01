# BFIP Extension: Zero Knowledge Proofs
## Status: Reserved — v1.0.0

This extension is reserved for a future protocol version. The fields to accommodate it are present in the v0.1.0 reference schema but are nullable and unused.

---

## Overview

In a future version of BFIP, Zero Knowledge Proofs will allow users to prove their verified status without revealing any identifying information — not even to Box Fraise. A third party receives a cryptographic proof that the presenter is a BFIP-verified human without learning who they are, when they were verified, or where they were verified.

---

## Reserved fields

- `soultokens.vc_credential_json` — reserved for W3C Verifiable Credential issuance

---

## Migration path

When ZK proofs are introduced:
- Soultokens are issued as W3C Verifiable Credentials
- The `vc_credential_json` field is populated at issuance
- A ZK proof generation endpoint is added to the API
- Third-party verification accepts ZK proofs in addition to attestation tokens
- The protocol version increments to v1.0.0
