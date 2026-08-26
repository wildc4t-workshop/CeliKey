# CeliKey Project State

**Checkpoint:** 2026-08-26

## Goal

Build a reversible, OEM-like passive-entry system for the 2000 US-spec Celica GT-S using a phone as the primary credential, BLE for discovery/session transport, UWB for proximity discrimination, and secure NFC as backup. Preserve the factory key/remote/immobilizer path.

Detailed hardware and vehicle-interface decisions live in [`CELIKEY_PCB_CONCEPT.md`](CELIKEY_PCB_CONCEPT.md). Executable work and dependencies live in [`tasks.csv`](tasks.csv). Third-party provenance lives in [`SOURCES.md`](SOURCES.md).

## Proven Milestones

### UWB / BLE bring-up

- DWM3001CDK brought up successfully with known-good Qorvo Nearby Interaction firmware.
- iPhone ↔ DWM3001CDK UWB distance and direction ranging work.
- Existing accessory-side threshold/hysteresis behavior works.

### iOS background operation

- Locked-screen/background ranging works while the app remains backgrounded.
- Secure-bubble notifications are delivered while backgrounded.
- Long-away recovery has worked at proof-of-concept level after roughly one hour away.
- Observed reacquisition latency was approximately **13 seconds**; the source of that delay remains unresolved.
- Force-quitting the app stops passive operation.

### Vehicle-interface preparation

- Preferred Toyota lock path is receiver **RDA → Body ECU** if actual-car characterization/replay proves viable.
- OEM/JDM power-folding mirrors are part of the required controller interface.
- Overhead M3 constant-power connector family and reversible pass-through concept have been identified.
- The FNIRSI 2D15P scope/multimeter intended for first RDA characterization has been delivered and is available.

## Current Architecture Baseline

Treat these as working architecture unless testing changes them:

- DWM3001C as preferred future UWB/BLE module.
- One high/central primary UWB node first; add satellites only if vehicle testing demonstrates a need.
- Main controller high/central behind the mirror/windshield as the starting package location.
- Reversible overhead M3 power pass-through/T-harness architecture.
- Toyota Body ECU remains responsible for normal lock logic if RDA emulation works.
- Factory Toyota key/remote/immobilizer remain fallback paths.
- Dedicated interfaces for lock/unlock, power-folding mirrors, and reserved future keyless-start functionality.
- Secure authentication remains separate from proximity alone.

## Vehicle / Hardware

Known/current development hardware includes:

- Qorvo DWM3001CDK proof-of-concept board;
- iPhone used for Nearby Interaction development;
- FNIRSI 2D15P scope/multimeter/DDS unit, now available for vehicle characterization;
- Arduino, Raspberry Pi, breadboard/test hardware;
- OEM/JDM Celica power-folding mirrors;
- actual Toyota receiver/Body ECU path in the retrofitted car.

Hardware ownership does not freeze the final PCB architecture.

## Current Engineering Gates

### Software / background behavior

The immediate software question is where the approximately 13-second long-away reacquisition delay occurs. Add timestamped BLE/Nearby Interaction/ranging lifecycle logging, then repeat controlled away/return tests.

Do not aggressively refactor the known-good sample until the recovery path is observable.

### Vehicle / RDA

The immediate vehicle question is the actual electrical behavior of the installed RDA line.

Sequence:

1. positively identify the installed RDA wire;
2. measure idle/high/low voltage and topology with the available scope/multimeter;
3. design the protected capture interface from measured behavior;
4. capture repeated LOCK/UNLOCK commands;
5. analyze repeatability/frame structure;
6. only then perform controlled replay/isolation testing.

### Mirrors

Bench-characterize polarity, startup/running/end-stop current, end-stop behavior, and JDM-switch coexistence before selecting the final driver topology.

### RF placement / power

Test one high/central UWB node in the actual vehicle before committing to satellite hardware. Measure parked current early enough that Rev A power/sleep architecture follows data rather than assumptions.

## Overhead Power T-Harness — Connector Identified

Toyota EWD399U identifies overhead connector **M3** and the constant-B+ path at **M3 pin 2, L-W (Blue/White)** from the unswitched 7.5 A DOME circuit.

Current mating architecture:

| Interface | Toyota PN | Tokai Rika PN | Role |
|---|---|---|---|
| Female housing | `90980-11533` | `4F0800-0000` | mates to overhead assembly |
| Male housing | `90980-11532` | `4G0800-0000` | mates to existing roof-harness female plug |
| Contacts | — | unsealed 050-series | pin/socket terminal family |

Rev 0 may use a removable spliced T-adapter. Selected Rev A direction is a PCB-integrated passive M3 pass-through with a protected CeliKey B+ branch.

Still verify actual-car cavity population, wire colors, true-ground location, voltage/continuity, and selected wire/crimp compatibility before release.

## Open Questions

Highest-value unresolved questions are:

1. What causes the observed background reacquisition delay, and how repeatable is it?
2. What are the actual RDA voltage levels/topology/frame characteristics on this car?
3. Can synthetic RDA replay preserve Toyota chirp/light-flash/staged-lock semantics and OEM receiver coexistence?
4. Does one high/central UWB node provide adequate real-world coverage and inside/outside discrimination?
5. What parked-current budget and sleep/wake behavior should drive Rev A power design?
6. What mirror driver topology follows actual motor and switch-coexistence measurements?
7. What secure phone/NFC authentication implementation should be selected?

## Current Focus

Use [`tasks.csv`](tasks.csv) for exact status. At this checkpoint the highest-value parallel work is:

- instrument and repeat iOS background-recovery testing;
- identify and characterize RDA using the now-available scope;
- bench-characterize folding mirrors;
- test one-node vehicle RF placement;
- continue reversible M3 power-interface verification.

## Current Phase Success Criteria

The current software phase succeeds when background recovery is repeatable and its latency is understood well enough to tune for passive-entry use.

The current vehicle-interface phase succeeds when the Celica Body ECU accepts synthetic RDA LOCK/UNLOCK commands while preserving the desired factory behavior and OEM fallback path.
