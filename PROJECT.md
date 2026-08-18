# CeliKey Project State

**Checkpoint:** 2026-08-18

## Goal

Build a reversible, OEM-like passive-entry system for the Celica using a phone as the primary credential, BLE for discovery/connection, and UWB for precise proximity. The factory key/remote/immobilizer remain intact.

Detailed hardware and vehicle-interface decisions live in [`CELIKEY_PCB_CONCEPT.md`](CELIKEY_PCB_CONCEPT.md). Third-party provenance lives in [`SOURCES.md`](SOURCES.md).

## Proven Milestones

### UWB / BLE bring-up

- **DWM3001CDK brought up successfully.**
  - Known-good firmware: `DWM3001CDK-QANI-FreeRTOS_full.hex`.
  - J-Link/RTT confirms the Qorvo Nearby Interaction application boots under FreeRTOS.
- **iPhone ↔ DWM3001CDK UWB ranging works.**
  - Distance and direction arrow work in the Qorvo demonstrator.

### Accessory-side proximity logic

- Existing QANI firmware computes `distance_cm`.
- Stock hysteresis thresholds were confirmed:
  - `< 100 cm` → enter secure bubble.
  - `> 180 cm` → exit/rearm.
- Onboard LEDs visibly change state at those thresholds.

### iOS background operation

- **Locked-screen/background ranging works.**
  - The source-built Qorvo-derived iOS sample continued ranging with the phone locked and in a pocket.
  - `Secure bubble` / `Outside secure bubble` notifications were delivered while backgrounded.
  - Notifications also appeared on Apple Watch.
- **Long-away recovery works at proof-of-concept level.**
  - After roughly one hour away with the app backgrounded, returning near the board caused a secure-bubble event without touching the phone.
  - Observed reacquisition time was approximately **13 seconds**.
  - The source of that latency is not yet known.
- **Force-quitting the app stops passive operation.**
  - Ordinary backgrounding works.
  - Explicitly swiping the app away stops it.

## Current Architecture Baseline

The following are treated as current Rev A decisions unless testing invalidates them:

- DWM3001C as the primary future PCB UWB/BLE module.
- Main ECU high/central behind the rearview mirror / windshield.
- Constant vehicle power from an OEM-style dome-light inline T-harness.
- Automotive-protected low-quiescent power conversion.
- One primary UWB node initially, with provision for two optional satellite nodes.
- NFC fallback through a separate reader/antenna near the driver-side windshield/A-pillar.
- Diagnostic `PWR`, `PHONE`, `UWB`, and `ACCESS` LEDs.
- Service/pairing button and debug/programming access.
- Dedicated vehicle-side control paths for:
  - lock/unlock,
  - power-folding mirrors,
  - future keyless/push-button start.
- Toyota Body ECU remains responsible for normal lock logic if RDA emulation proves viable.
- Factory Toyota key/remote remains a fallback.

See [`CELIKEY_PCB_CONCEPT.md`](CELIKEY_PCB_CONCEPT.md) for the detailed implementation concept.

## Known-Good Development Baseline

### Hardware
- Qorvo DWM3001CDK
- iPhone 12 Pro Max
- onboard J-Link programming/debug

### Firmware
- `DWM3001CDK-QANI-FreeRTOS_full.hex`
- exact SHA-256 and recovered-source commit are recorded in [`SOURCES.md`](SOURCES.md)

### iOS
- Qorvo-derived `NINearbyAccessorySample`
- top-level `.xcworkspace`
- working scheme/target: `Qorvo NI Background`
- full known-good working copy preserved under `reference/private/`

## Open Questions

### Background behavior
1. What causes the approximately **13 s** reacquisition delay?
   - BLE advertising/reconnect?
   - Core Bluetooth restoration?
   - Nearby Interaction session restart?
   - first valid UWB range?
2. How consistent is recovery after true BLE/UWB loss over repeated daily-use tests?

### Vehicle / hardware
3. What are the actual RDA voltage levels, topology, and command waveforms on this car?
4. Can synthetic RDA replay preserve factory lock logic, chirp, and light-flash acknowledgement?
5. Does one high/central UWB node provide adequate real-car coverage and inside/outside discrimination?
6. What is the parked current budget and final low-power strategy?
7. What exact OEM dome connector/terminal family should be used for the T-harness?
8. What final mirror-driver topology is required after bench characterization?

## Next

### Software
1. Add timestamped logging for:
   - BLE disconnect/connect,
   - NI suspend/resume/start,
   - first valid UWB range,
   - secure-bubble enter/exit.
2. Repeat long-away/return tests and locate the ~13 s delay.
3. Reduce the Qorvo-derived iOS sample toward a minimal CeliKey app without breaking background operation.

### Vehicle
1. Obtain/use a protected logic-analyzer interface.
2. Characterize RDA voltage and signaling.
3. Capture repeated LOCK/UNLOCK frames.
4. Only after passive characterization, attempt controlled RDA replay.

## Current Phase Success Criteria

The immediate software phase succeeds when background recovery is repeatable and its latency is understood well enough to tune for passive-entry use.

The vehicle-interface phase succeeds when the Celica Body ECU accepts synthetic RDA LOCK/UNLOCK commands while preserving the desired factory behavior.
