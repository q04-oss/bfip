# Changelog

All notable changes to the Box Fraise Identity Protocol are documented here.

Versions follow [Semantic Versioning](https://semver.org):
- Major version — breaking protocol changes, existing soultokens may require migration
- Minor version — new features, backward compatible
- Patch version — clarifications, editorial fixes, no protocol changes

---

## v0.1.1 — 2026-05-01

Added Stage 1b: background checks (sanctions screening and identity fraud).

### Added
- Stage 1b between identity confirmation and cooling period
- Sanctions screening against OFAC, UN, and EU sanctions lists
- Identity fraud check against known synthetic identity databases
- `background_checks` table in reference schema
- Section 3b in PROTOCOL.md covering background check requirements
- `background_checks` table in reference/schema.sql

### Not changed
- All four existing stages unchanged
- All existing tables and fields unchanged
- No breaking changes

---

## v0.1.0 — 2026-05-01

Initial draft specification.

Protocol designed, not yet implemented. All sections subject to revision as implementation reveals gaps. Third-party implementations are not recommended against this version.

### Defined in this version
- Four-stage verification protocol (identity, cooling, presence, attestation)
- Soultoken specification (UUID internal, display code external, Ed25519 signature)
- Beacon protocol (daily HMAC UUID derivation)
- Box NFC chip chain of custody
- Staff visit and dual-reviewer attestation model
- Attestation token specification for third-party verification
- Revocation and renewal mechanisms
- Reference PostgreSQL schema (36 tables)
- Cryptographic primitive specifications

### Reserved for future versions
- Hardware binding (v0.2.x)
- Government credential integration — mDL, eIDAS (v0.2.x)
- Zero Knowledge Proofs (v1.0.0)
- On-chain soultoken migration (v1.0.0)
