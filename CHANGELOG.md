# Changelog

All notable changes to the Box Fraise Identity Protocol are documented here.

Versions follow [Semantic Versioning](https://semver.org):
- Major version — breaking protocol changes, existing soultokens may require migration
- Minor version — new features, backward compatible
- Patch version — clarifications, editorial fixes, no protocol changes

---

## v0.1.3 — 2026-05-01

Removed mesh extension from BFIP. Mesh protocol is now maintained
as a separate specification at q04-oss/bfmp.

### Changed
- Removed extensions/mesh.md — moved to q04-oss/bfmp
- Section 18.6: updated reference to point to BFMP at github.com/q04-oss/bfmp
- Section 19.6: updated reference to point to BFMP at github.com/q04-oss/bfmp

### Not changed
- No protocol changes
- No schema changes
- All other sections unchanged

---

## v0.1.2 — 2026-05-01

Added cleared status, trust registry, business participation lifecycle, and mesh network extension.

### Added
- Section 7b: Cleared status — optional post-attestation elevation requiring full background check suite (sanctions + identity fraud + criminal + sex offender + adverse media)
- Section 18: Trust Registry — Box Fraise as credential issuer not directory, public key infrastructure, offline verification model
- Section 19: Business participation lifecycle — from strawberry delivery to critical business infrastructure
- `cleared` token_type on soultokens — separate credential, independently revocable
- `attested_soultoken_id` on soultokens — cleared soultoken references the attested soultoken that qualified it
- `cleared_at` and `cleared_soultoken_id` fields on users
- `criminal`, `sex_offender`, `adverse_media` check types activated in background_checks
- `cleared_status_granted` and `cleared_status_revoked` verification event types
- `extensions/mesh.md` — mesh network extension reserved for v0.3.x covering beacon mesh nodes, iPhone mesh nodes, and verified physical encounters
- Reserved mesh tables documented in schema.sql

### Not changed
- All existing stages unchanged
- Sanctions and identity fraud remain Stage 1b requirements for attested
- No breaking changes

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
