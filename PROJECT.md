# CeliKey

**Checkpoint:** 2026-08-18

## Goal

Build a reversible, OEM-like passive-entry system for the Celica using a phone as the primary credential, BLE for discovery/connection, and UWB for precise proximity. The factory key/remote/immobilizer remain intact. The architecture should also leave room for NFC backup, mirror control, and future keyless/push-button start without forcing a redesign.

## Proven Milestones

- **DWM3001CDK brought up successfully.**
  - Known-good firmware: `DWM3001CDK-QANI-FreeRTOS_full.hex`.
  - J-Link/RTT confirms the Qorvo Nearby Interaction application boots under FreeRTOS.
- **iPhone ↔ DWM3001CDK UWB ranging works.**
  - Distance and direction arrow work well in the Qorvo demonstrator.
- **Functional proximity logic works on the accessory.**
  - Existing QANI code computes `distance_cm`.
  - Stock thresholds in the recovered source are:
    - `< 100 cm` → enter secure bubble.
    - `> 180 cm` → exit/rearm.
  - Onboard LEDs visibly change state at the thresholds.
- **Locked-screen/background operation works.**
  - Custom-built Qorvo-derived iOS sample continued ranging with the phone locked and in a pocket.
  - `Secure bubble` / `Outside secure bubble` notifications were delivered while the app was backgrounded.
  - Notifications also appeared on Apple Watch.
- **Background recovery after a long absence works at least at proof-of-concept level.**
  - After roughly one hour away with the app backgrounded, returning near the board caused the secure-bubble event to recover automatically after approximately **13 seconds**.
  - Exact source of the delay is not yet known.
- **Force-quitting the iOS app stops passive operation.**
  - Normal backgrounding works.
  - Swiping the app away stops the behavior.

## Locked Decisions — Rev A Concept

### Main ECU / Packaging
- Main CeliKey ECU mounted **behind the rearview mirror, against/near the windshield**.
- Primary radio module: **DWM3001C** on a future custom PCB.
- Keep the DWM3001C antenna area clear and place it at/near a PCB edge.
- Use a small, removable enclosure; no permanent interior modification.

### Vehicle Power
- Use the existing **constant-powered overhead/dome-light circuit**.
- Connect through an **OEM-style inline T-harness/pass-through adapter**:
  - factory roof harness → CeliKey T-harness → factory dome assembly.
  - no permanent cuts or taps in the car.
- Custom PCB requires an automotive power front end:
  - transient protection,
  - reverse-polarity protection,
  - filtering,
  - low-quiescent-current conversion from vehicle 12 V to the required low-voltage rail(s), including 3.3 V for the DWM3001C system.

### UWB Topology
- Start with **one primary DWM3001C**.
- Preserve hardware/software expansion for **two additional UWB nodes** if real vehicle testing shows that one node cannot provide good coverage or inside/outside discrimination.
- Do not commit to the exact multi-node protocol until needed.

### NFC
- NFC is the physical backup credential path.
- Reader/antenna concept: **driver-side A-pillar / windshield**, where the harness is already expected to route.
- Prefer a small NFC daughterboard/reader rather than forcing the NFC antenna onto the main behind-mirror PCB.

### Diagnostics / Service
- Rev A PCB should include small visual status LEDs, roughly:
  - `PWR`
  - `PHONE`
  - `UWB`
  - `ACCESS`
- LEDs should support debug/normal/dark behavior so they are not annoying at night.
- Include a small service/pairing button.
- Include accessible programming/debug pads.

### Vehicle Integration
- Keep UWB/credential logic separate from Celica-specific vehicle I/O.
- Reserve dedicated control paths for:
  - lock/unlock,
  - power-folding mirrors,
  - future keyless/push-button start.
- Initial vehicle testing should be reversible and preferably begin in a `WOULD UNLOCK` / shadow mode before connecting real lock outputs.

### User Controls
Future app should retain:
- manual `LOCK` / `UNLOCK`,
- `Passive Entry` ON / PAUSED,
- temporary or indefinite pause so washing/working around the car does not cause cycling.

## Current Known-Good Baseline

### Hardware
- Qorvo `DWM3001CDK`
- iPhone 12 Pro Max used for the POC
- DWM3001CDK connected/programmed through onboard J-Link

### Firmware
Recovered through the `Smartlight_UWB` GitHub repository because the Qorvo download portal would not provide the requested SDK.

Relevant path inside that repository:

```text
UwbModuleDWM3001CDK/
  Qorvo_Nearby_Interaction_3_1_0/
    Software/Accessory/Sources/
      QANI-All-FreeRTOS_QNI_3_0_0/
        Projects/Projects/QANI/FreeRTOS/DWM3001CDK/
```

Known-good binary:

```text
ses/Output/Common/Exe/DWM3001CDK-QANI-FreeRTOS_full.hex
```

Relevant ranging logic:

```text
Projects/Src/Apps/Src/fira/QANI/fira_niq.c
```

Observed stock constants:

```c
#define MIN_CM_DISTANCE_THRESHOLD 100
#define MAX_CM_DISTANCE_THRESHOLD 180
```

### iOS
Opened the top-level workspace:

```text
NINearbyAccessorySample.xcworkspace
```

Working scheme/target encountered during setup:

```text
Qorvo NI Background
```

The source-built sample successfully demonstrated locked-screen/background UWB behavior.

## Open Questions

1. What causes the approximately **13 s** reacquisition delay after being away for a long period?
   - BLE advertising/reconnect?
   - Core Bluetooth background restoration?
   - Nearby Interaction session restart?
   - first valid UWB range?
2. How quickly and reliably does the system recover after true BLE/UWB loss in realistic daily use?
3. What is the parked current budget and best low-power strategy for a permanently powered vehicle module?
4. Does one windshield-mounted UWB node provide acceptable coverage and inside/outside discrimination in the Celica?
5. If additional nodes are needed, what is the cleanest master/satellite architecture?
6. What exact OEM dome connector/terminal family should be used for the T-harness?
7. What exact NFC reader/antenna implementation will be used?
8. What vehicle-side interface will be used for lock/unlock while preserving factory logic and reversibility?

## Next When Resumed

1. Add timestamped iOS logging for:
   - BLE disconnect/connect,
   - NI suspend/resume/start,
   - first valid range,
   - secure-bubble enter/exit.
2. Repeat the long-away/return test and locate the source of the ~13 s latency.
3. Begin cleaning the Qorvo-derived iOS sample into a minimal CeliKey app while preserving the known-good background behavior.
