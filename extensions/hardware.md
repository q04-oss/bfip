# BFIP Extension: Hardware Binding
## Status: Reserved — v0.2.x

This extension is reserved for a future protocol version. The fields and tables to accommodate it are present in the v0.1.0 reference schema but are nullable and unused.

---

## Overview

In a future version of BFIP, proprietary Box Fraise hardware will replace Apple App Attest as the primary device attestation mechanism. This hardware will:

- Be embedded in or accompany every Box Fraise delivery box
- Contain a hardware security element capable of cryptographic signing
- Be provisioned with a unique key pair at manufacture
- Communicate with the Box Fraise backend via a secure channel
- Be tamper-evident — physical compromise destroys the signing key

---

## Reserved fields

The following fields are present in the v0.1.0 schema and reserved for this extension:

- `beacons.hardware_key_id` — the hardware security element identifier for beacon hardware
- `presence_events.hardware_identifier` — the hardware device identifier for presence events
- `beacon_health_log.firmware_version` — firmware version for hardware health monitoring

---

## Migration path

When hardware binding is introduced:
- `presence_events.hardware_identifier` becomes required (NOT NULL)
- `beacons.hardware_key_id` becomes required for hardware beacons
- A new credential type is added to the attestation token scope
- The protocol version increments to v0.2.0
