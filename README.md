# BFIP

Box Fraise Identity Protocol — a cryptographically signed, in-person verified identity standard built on physical presence, hardware attestation, and human witness.

---

## What it is

BFIP is a protocol for establishing verified human identity through in-person presence at registered physical locations. It is not a digital identity system — it is a physical one. Verification requires a real person to show up somewhere real, repeatedly, and be confirmed by a Box Fraise employee in person.

The result is a soultoken: a cryptographically signed, non-transferable credential proving that a specific device belongs to a real, verified human who has been physically present at a registered location and confirmed by Box Fraise staff.

---

## What it is not

BFIP is not:
- An email verification system
- A phone number verification system
- A social graph verification system
- A government ID replacement

BFIP is a presence protocol. The only thing it proves is that a real person was physically somewhere, repeatedly, and that Box Fraise employees confirmed their identity in person.

---

## Protocol versions

| Version | Status | Date |
|---------|--------|------|
| v0.1.2 | Draft | 2026-05-01 |
| v0.1.1 | Superseded | 2026-05-01 |
| v0.1.0 | Superseded | 2026-05-01 |

---

## Structure

```
bfip/
├── PROTOCOL.md          — full specification
├── CHANGELOG.md         — version history
├── LICENSE.md           — CC BY 4.0
├── versions/            — frozen version snapshots
│   └── v0.1.0.md
├── extensions/          — reserved extension specifications
│   ├── hardware.md      — proprietary hardware binding (reserved)
│   └── zkp.md           — zero knowledge proofs (reserved)
└── reference/
    ├── schema.sql        — reference PostgreSQL implementation
    └── cryptography.md  — cryptographic primitive specifications
```

---

## Implementation

The reference implementation of BFIP v0.1.0 is built on:
- PostgreSQL — storage
- Rust / Axum — server
- iOS / Swift — client
- Apple App Attest — device attestation
- Stripe Identity — identity credential verification

Third-party implementations must conform to the cryptographic primitives defined in `reference/cryptography.md`.

---

## License

CC BY 4.0 — see `LICENSE.md`. Anyone may implement BFIP. Attribution to Box Fraise is required.