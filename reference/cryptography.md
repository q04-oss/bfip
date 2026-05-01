# BFIP Cryptographic Primitives
## Reference v0.1.0

---

## 1. Beacon UUID derivation

Every beacon broadcasts a UUID that rotates daily. The UUID is derived as follows:

```
input   = business_id_string || ":" || date_string
uuid    = HMAC-SHA256(secret_key, input)
```

Where:
- `secret_key` is the beacon's registered HMAC secret (32 bytes minimum, stored encrypted at rest)
- `business_id_string` is the decimal string representation of the business integer ID
- `date_string` is the current date in `YYYY-MM-DD` format in UTC
- The 32-byte HMAC output is formatted as a standard UUID v4 string by inserting hyphens at positions 8, 12, 16, 20 and setting the version bits

Example:
```
secret_key      = "a3f8e2b1c9d4f7a6e5b8c3d2f1a4e7b0..."
business_id     = "42"
date_string     = "2026-05-01"
input           = "42:2026-05-01"
hmac_output     = HMAC-SHA256(secret_key, "42:2026-05-01")
uuid            = format as UUID v4
```

---

## 2. Beacon witness HMAC

A beacon witness HMAC proves a presence event occurred at a specific beacon for a specific user on a specific day.

```
input   = business_id_string || ":" || date_string || ":" || user_id_string
witness = HMAC-SHA256(secret_key, input)
```

Where:
- `user_id_string` is the decimal string representation of the user integer ID
- All other inputs are identical to the UUID derivation

The witness HMAC is included in every presence event record and in the evidence package for attestations.

---

## 3. Soultoken display code derivation

The display code is a human-readable identifier derived from the soultoken UUID.

```
hmac_output = HMAC-SHA256(display_code_key, uuid_bytes)
base36      = base36_encode(hmac_output[0:9])
display_code = format(base36, "XXXX-XXXX-XXXX")
```

Where:
- `display_code_key` is a platform-level HMAC key (separate from beacon keys)
- `uuid_bytes` is the raw 16-byte representation of the soultoken UUID
- `base36_encode` encodes using characters A-Z and 0-9
- The output is formatted as three groups of 4 characters separated by hyphens

The `display_code_key_version` field on the soultoken record indicates which version of the display code key was used, enabling key rotation without invalidating existing display codes.

---

## 4. Soultoken signature

Soultokens are signed with Box Fraise's Ed25519 private key.

The signed payload is the SHA-256 hash of:
```
uuid || "|" || holder_user_id_string || "|" || issued_at_iso8601 || "|" || expires_at_iso8601 || "|" || display_code
```

Fields are concatenated with pipe separators. Timestamps are ISO 8601 format in UTC.

The signature covers `expires_at` to prevent database-level extension of soultoken validity without invalidating the signature.

Verification:
```
payload_hash = SHA-256(payload_string)
valid        = Ed25519.verify(public_key, payload_hash, signature)
```

Box Fraise's Ed25519 public key is published at a well-known URL for third-party verification.

---

## 5. Evidence package hash

The evidence package hash covers the delivery photo, GPS coordinates, and beacon witness HMAC.

```
photo_hash    = SHA-256(photo_bytes)
gps_json      = {"lat": arrived_latitude, "lng": arrived_longitude}
beacon_hmac   = beacon_witness_hmac (see section 2)

evidence_hash = SHA-256(
    photo_hash || "|" ||
    gps_json_string || "|" ||
    beacon_hmac
)
```

Reviewers sign the evidence hash. Their signature proves they evaluated specific, unmodified evidence.

---

## 6. Attestation token

Attestation tokens are short-lived credentials issued to verified users for third-party verification.

```
token_bytes  = secure_random(32)
token_hash   = SHA-256(token_bytes)
```

The raw `token_bytes` are returned to the user once and never stored. Only `token_hash` is stored in the database. Third-party verification presents the raw token; the server hashes it and looks up the stored hash.

---

## 7. Identity credential response integrity

The raw Stripe webhook payload is hashed on receipt to prove the stored verification result has not been tampered with.

```
response_hash = HMAC-SHA256(webhook_integrity_key, raw_payload_bytes)
```

Where `webhook_integrity_key` is a platform-level key separate from all other keys.

---

## 8. Key management

| Key | Type | Purpose | Rotation |
|-----|------|---------|----------|
| Beacon secret keys | HMAC-SHA256 | Daily UUID derivation | Per-beacon, on compromise |
| Display code key | HMAC-SHA256 | Soultoken display code | Platform-level, versioned |
| Ed25519 private key | Ed25519 | Soultoken signatures | Annual or on compromise |
| Webhook integrity key | HMAC-SHA256 | Credential response integrity | Annual or on compromise |

All keys are stored encrypted at rest. Private keys are never logged.

---

## 9. Algorithm versions

| Algorithm | Version used | Notes |
|-----------|-------------|-------|
| HMAC | SHA-256 | All HMAC operations |
| Soultoken signature | Ed25519 | Per NIST recommendations |
| Evidence hash | SHA-256 | Evidence package integrity |
| Token hash | SHA-256 | Attestation token storage |
| UUID | v4 | Internal soultoken identifier |
