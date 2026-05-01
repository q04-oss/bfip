# BFIP Extension: Mesh Network
## Status: Reserved — v0.3.x

This extension defines the Box Fraise mesh network — a distributed physical network of beacon nodes and iPhone nodes that enables offline verification, encrypted message routing, and verified physical encounter records.

---

## Overview

In the current protocol (v0.1.x), each beacon operates independently. Presence events flow directly from a user's iPhone to the Box Fraise server. Verification requires an internet connection.

The mesh extension transforms this into a distributed physical network where:
- Every beacon is a network node
- Every attested user's iPhone is a network node
- Presence events route through the mesh when internet is unavailable
- Encrypted messages route through the most efficient path — server, mesh, or direct peer-to-peer
- Two attested users in physical proximity create a verified encounter record without server involvement

---

## Beacon mesh nodes

### Hardware requirements
Mesh-capable beacons require:
- BLE (existing) — for user presence detection
- Mesh radio — for beacon-to-beacon communication
- Hardware security element — for signing mesh communications
- Tamper-evident enclosure — physical compromise destroys signing key

### Beacon peer relationships
Each beacon discovers and maintains relationships with neighboring beacons. Peer relationships are:
- Established during delivery visits by delivery staff
- Validated by both beacons signing a mutual acknowledgment
- Recorded in the `beacon_peers` table (reserved)
- Monitored for health — a beacon that stops communicating with peers is flagged

### Mutual validation
Neighboring beacons validate each other continuously. A beacon that begins broadcasting an unexpected UUID is detected by its peers and reported to the server. This closes the beacon cloning attack vector — a rogue beacon cannot operate undetected when surrounded by legitimate peers.

### Offline presence relay
When a user's iPhone has no internet connectivity:
1. iPhone detects beacon, generates signed presence event
2. Beacon receives the signed event
3. Beacon relays the event through the mesh to a beacon with internet connectivity
4. That beacon forwards to the server
5. Server records the presence event with the relay path for audit

The relay path is recorded — every beacon that touched the event is logged.

---

## iPhone mesh nodes

### Technology
iPhone mesh nodes use:
- **MultipeerConnectivity** — device-to-device communication via WiFi, Bluetooth, and peer-to-peer WiFi. No internet required. Used by AirDrop.
- **CoreBluetooth** — beacon detection and short-range device discovery
- **Nearby Interaction + UWB** — centimeter-level proximity measurement on supported devices

### Participation
An attested user's iPhone becomes a mesh node automatically when:
- The user has enabled mesh participation in app settings
- The device is in a BFIP-registered location
- The device has sufficient battery

Mesh participation is opt-in. Users who do not enable it are not relay nodes but can still use the mesh for their own presence events.

### Message routing
Signal-protocol encrypted messages between attested users route through the most efficient available path:

| Condition | Routing |
|-----------|---------|
| Both users online | Standard server routing |
| One user offline | Mesh relay through nearby devices |
| Both users nearby | Direct peer-to-peer, no server |
| Cleared-to-cleared | Direct peer-to-peer only, never server |

The encryption is end-to-end. Relay devices see only an encrypted blob they cannot read. The mesh is a transport layer — privacy is guaranteed by Signal protocol regardless of route.

### Cleared-to-cleared communications
Users with cleared status have the option to route all communications through direct peer-to-peer only — never touching Box Fraise servers. This is the highest privacy guarantee the platform offers. Box Fraise never sees cleared-to-cleared message content or metadata.

---

## Verified physical encounters

When two attested users are in physical proximity and both consent, the platform records a verified physical encounter.

### Detection
Proximity is detected via:
- UWB (Ultra Wideband) — centimeter-level precision on supported devices
- Bluetooth RSSI — meter-level precision on all devices

### Record structure
A verified encounter record contains:
- User A identifier (attested)
- User B identifier (attested)
- Nearest beacon identifier (location proof)
- Proximity measurement in centimeters
- User A device signature (App Attest)
- User B device signature (App Attest)
- Timestamp
- Both users' consent signatures

### Privacy
Encounter records are held by both users on their devices. Neither user's record is stored on Box Fraise servers unless both users explicitly share it for a specific purpose (such as proving attendance at a specific event).

### Commercial applications
Verified encounter records prove that two specific verified humans were physically in the same place at the same time. Commercial applications include:
- Proof of in-person meeting for contract execution
- Attendance verification for regulated events
- Physical presence proof for legal proceedings
- Healthcare patient-provider encounter records
- Government service delivery confirmation

---

## Reserved schema tables

The following tables are reserved for the mesh extension and are documented in `reference/schema.sql`:

**`beacon_peers`** — neighboring beacon relationships
- beacon_id, peer_beacon_id, discovered_at, last_seen_at, mutual_signature

**`mesh_events`** — inter-beacon communications
- source_beacon_id, destination_beacon_id, event_type, payload_hash, relayed_at

**`offline_presence_events`** — presence events relayed through mesh
- presence_event_id, relay_beacon_id, relay_device_id, relayed_at, server_received_at

**`verified_encounters`** — two attested users in physical proximity
- user_a_id, user_b_id, location_beacon_id, proximity_cm, user_a_signature, user_b_signature, occurred_at

---

## Migration path

When mesh is introduced:
- `beacons.hardware_key_id` becomes required for mesh-capable beacons
- `presence_events.hardware_identifier` is extended to include mesh relay path
- Four reserved tables are created
- App settings gain mesh participation toggle
- The protocol version increments to v0.3.0
