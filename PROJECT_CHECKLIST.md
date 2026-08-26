# CeliKey Milestone Roadmap

**Checkpoint:** 2026-08-26

This file is a **stable milestone/context map**, not a second task manager.

- `tasks.csv` is the canonical executable work queue and task-status source.
- `PROJECT.md` is the canonical current engineering state.
- Detailed architecture belongs in `CELIKEY_PCB_CONCEPT.md` and topic-specific reference files.

Keep these section headings stable because `tasks.csv` links into them for context.

## 1. Core UWB / iPhone Proof of Concept

Proven:

- DWM3001CDK bring-up and known-good QANI firmware.
- BLE connection and UWB distance/direction ranging from iPhone.
- Accessory-side threshold/hysteresis behavior.
- locked-screen/background ranging and notifications.
- long-away recovery at proof-of-concept level.
- force-quit stops passive behavior.

Current gate:

- instrument the lifecycle;
- repeat long-away/return tests;
- identify where the observed ~13 s reacquisition delay occurs;
- establish a practical passive-entry reacquisition target.

## 2. iOS / CeliKey App

Current direction:

- preserve the known-good Qorvo-derived sample until logging explains background recovery;
- then reduce it into a minimal CeliKey app without breaking background behavior;
- add manual LOCK/UNLOCK and explicit Passive Entry ON/PAUSED controls;
- define enrollment/re-pairing and a secure authentication mechanism distinct from proximity alone.

Implementation details and status belong in `tasks.csv`.

## 3. Vehicle RDA Characterization

The FNIRSI scope/multimeter ordered for initial characterization has been delivered and is available for use.

Characterization sequence:

1. positively identify the installed receiver RDA wire on the actual retrofitted car;
2. measure idle/high/low voltage and infer source/sink topology before attaching logic-level hardware;
3. build a protected high-impedance capture interface based on the measured electrical behavior;
4. capture repeated LOCK and UNLOCK commands plus useful staged/PANIC behavior;
5. analyze timing, framing, repeatability, counters/checksums, and PRG participation where relevant.

Preserve raw captures and test conditions. Do not call a frame decoded from a single correlation.

## 4. RDA Replay / Lock Integration

Only after passive characterization:

- build a safe isolation/replay fixture;
- replay LOCK and UNLOCK commands;
- verify Body ECU behavior, staged unlock, chirp, and light-flash acknowledgement;
- confirm the OEM receiver still works after restoration;
- select the final coexistence topology.

If RDA proves unsuitable, evaluate Body ECU lock-switch input emulation as the fallback.

## 5. Power-Folding Mirrors

Before selecting a driver:

- bench-characterize fold/unfold polarity, startup/running/end-stop current, and end-stop behavior;
- determine coexistence requirements with the OEM/JDM switch;
- define command timeout/fault handling and ignition inhibit.

Only then integrate fold/unfold with accepted lock/unlock events.

## 6. Vehicle RF / Placement Testing

Start with one high/central UWB node.

Test real-car approach coverage and inside/outside discrimination from representative directions and phone positions. Add satellite nodes only if single-node testing demonstrates a real need. Final thresholds/hysteresis should follow measured vehicle behavior and background-recovery data.

## 7. Power / Harness Definition

Current direction:

- use the identified M3 overhead connector path for constant B+;
- verify actual-car cavities/wire colors and a true ground point;
- use a temporary removable T-harness for Rev 0 as needed;
- define parked-current budget, sleep/wake strategy, automotive input protection, and low-quiescent regulation before Rev A;
- final architecture is a reversible PCB-integrated passive M3 pass-through with a protected CeliKey B+ branch.

## 8. NFC Backup

NFC is an independent backup credential path, not UID-only convenience access.

Work includes reader/controller selection, secure card credential selection, driver-side mounting feasibility, daughterboard interface definition, bench authentication, then integration into the common authorization state machine.

## 9. Rev A PCB

Do not freeze Rev A until the vehicle interface, proximity policy, power architecture, secure authentication, mirror interface, and NFC interface are mature enough to size the hardware honestly.

Rev A should include only justified functional interfaces plus sensible debug/service access and reserved future-start capacity. Schematic and layout reviews precede ordering.

## 10. Vehicle Integration

Install through reversible harnessing and validate progressively:

1. shadow / `WOULD UNLOCK` behavior;
2. realistic approach/departure/rearm behavior;
3. real lock/unlock only after RDA acceptance and shadow-mode confidence;
4. OEM remote and mechanical-key fallback;
5. ignition inhibit, wash/service pause, mirror behavior;
6. long-duration parked-current behavior.

## 11. Future Keyless / Push-Button Start

This is **not part of the current functional milestone**.

Reserve interfaces now where inexpensive, but defer implementation until access control is mature. Future start authorization must be a separate safety-critical state machine with clutch interlock, running detection, immobilizer strategy, emergency/manual fallback, and a rule that credential loss while driving can never shut the engine off.

## 12. Documentation / Checkpoints

The public repository should remain sufficient to resume engineering without chat history while keeping unverified third-party binaries/source out of the public tree.

After meaningful work:

- update `PROJECT.md` when current architecture/state changes;
- update `tasks.csv` status/dependencies;
- preserve sources, measurements, raw-capture references, troubleshooting lessons, and decisions in the appropriate durable document;
- do not duplicate the task queue in this roadmap.
