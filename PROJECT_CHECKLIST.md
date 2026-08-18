# CeliKey Project Checklist

**Last updated:** 2026-08-18

Use this as the simple working checklist for the project. Detailed reasoning belongs in the other project documents.

## 1. Core UWB / iPhone Proof of Concept

- [x] Bring up DWM3001CDK.
- [x] Flash known-good QANI firmware.
- [x] Verify RTT boot output.
- [x] Confirm BLE connection from iPhone.
- [x] Confirm UWB distance ranging.
- [x] Confirm UWB direction.
- [x] Confirm onboard threshold / hysteresis logic.
- [x] Confirm locked-screen background ranging.
- [x] Confirm notifications while backgrounded.
- [x] Confirm background recovery after long absence.
- [ ] Measure where the ~13 s reacquisition delay occurs.
- [ ] Repeat long-away / return test multiple times.
- [ ] Establish acceptable passive-entry reacquisition target.

## 2. iOS / CeliKey App

- [x] Build and run the recovered Qorvo-derived iOS sample.
- [x] Install and run on physical iPhone.
- [x] Confirm force-quit stops passive behavior.
- [ ] Add timestamped logging for:
  - [ ] BLE disconnect.
  - [ ] BLE reconnect.
  - [ ] Nearby Interaction suspend.
  - [ ] Nearby Interaction resume/start.
  - [ ] First valid UWB range.
  - [ ] Secure-bubble enter.
  - [ ] Secure-bubble exit.
- [ ] Remove unnecessary demo/MQTT functionality.
- [ ] Rename / reduce project into a minimal CeliKey app.
- [ ] Preserve known-good background operation during cleanup.
- [ ] Add manual `LOCK` / `UNLOCK` controls.
- [ ] Add `Passive Entry` ON / PAUSED control.
- [ ] Add temporary / indefinite pause behavior.
- [ ] Define credential enrollment / pairing workflow.
- [ ] Define secure phone authentication implementation.

## 3. Vehicle RDA Characterization

- [ ] Obtain/use a sigrok/PulseView-compatible logic analyzer.
- [ ] Locate and positively identify the installed receiver RDA wire.
- [ ] Measure RDA idle/high/low voltage before digital capture.
- [ ] Build protected high-impedance RDA capture interface.
- [ ] Capture at least 10 LOCK commands.
- [ ] Capture at least 10 UNLOCK commands.
- [ ] Capture staged / second UNLOCK behavior.
- [ ] Capture PANIC or other useful remote commands if available.
- [ ] Determine whether LOCK / UNLOCK frames are repeatable.
- [ ] Determine RDA output topology.
- [ ] Determine whether PRG participates in normal operation.
- [ ] Document waveform timing and electrical characteristics.

## 4. RDA Replay / Lock Integration

- [ ] Design a safe temporary RDA isolation/replay fixture.
- [ ] Replay captured LOCK waveform.
- [ ] Verify door-lock behavior.
- [ ] Verify factory chirp behavior.
- [ ] Verify factory light-flash behavior.
- [ ] Replay captured UNLOCK waveform.
- [ ] Verify staged unlock behavior if applicable.
- [ ] Confirm OEM receiver still works after restoration.
- [ ] Select final RDA coexistence topology:
  - [ ] switched source;
  - [ ] compatible parallel injection;
  - [ ] temporary isolation during injection.
- [ ] If RDA fails, evaluate Body ECU lock-switch input emulation fallback.

## 5. Power-Folding Mirrors

- [ ] Bench-test JDM mirror fold wires.
- [ ] Confirm fold / unfold polarity.
- [ ] Measure startup current.
- [ ] Measure running current.
- [ ] Observe end-stop behavior / current.
- [ ] Confirm OEM/JDM switch coexistence requirements.
- [ ] Select mirror driver topology.
- [ ] Define ignition inhibit behavior.
- [ ] Define command timeout / fault behavior.
- [ ] Integrate mirror fold with valid lock event.
- [ ] Integrate mirror unfold with valid unlock event.

## 6. Vehicle RF / Placement Testing

- [ ] Temporarily place DWM3001CDK at proposed behind-mirror location.
- [ ] Test approach from driver side.
- [ ] Test approach from passenger side.
- [ ] Test approach from front.
- [ ] Test approach from rear.
- [ ] Test inside-vs-outside discrimination.
- [ ] Determine whether one UWB node is sufficient.
- [ ] If needed, define satellite-node locations.
- [ ] Finalize practical approach / departure thresholds.

## 7. Power / Harness Definition

- [X] Verify dome-light connector pinout on the actual car.
- [X] Identify constant battery supply.
- [ ] Identify true ground.
- [X] Confirm any courtesy-switched ground behavior.
- [X] Identify OEM Toyota connector / terminal family.
- [ ] Verify occupied M3 cavities on the actual car.
- [ ] Verify 20 AWG insulation OD is compatible with selected terminals.
- [ ] Build temporary removable M3 T-harness for Rev 0 testing.
- [ ] Design reversible inline dome-light T-harness.
- [ ] Define parked current budget.
- [ ] Define sleep / wake strategy.
- [ ] Select automotive input protection.
- [ ] Select low-quiescent 12 V → 3.3 V regulator.

## 8. NFC Backup

- [ ] Select NFC reader / controller.
- [ ] Select secure card credential.
- [ ] Confirm driver A-pillar / windshield mounting feasibility.
- [ ] Define NFC daughterboard interface.
- [ ] Bench-test card authentication.
- [ ] Integrate NFC authorization into access logic.

## 9. Rev A PCB

- [ ] Freeze minimum Rev A requirements.
- [ ] Finalize MCU selection.
- [ ] Finalize DWM3001C placement / antenna clearance.
- [ ] Add automotive power front end.
- [ ] Add RDA sense / drive / isolation circuitry.
- [ ] Add mirror interface.
- [ ] Add NFC daughterboard connector.
- [ ] Add two optional satellite-UWB ports.
- [ ] Add vehicle I/O connector.
- [ ] Add `PWR`, `PHONE`, `UWB`, and `ACCESS` LEDs.
- [ ] Add service / pairing button.
- [ ] Add programming / debug pads.
- [ ] Reserve future keyless-start I/O and power capacity.
- [ ] Perform schematic review.
- [ ] Layout PCB.
- [ ] Review RF keepout / antenna placement.
- [ ] Order Rev A boards.
- [ ] Assemble and bench-test Rev A.

## 10. Vehicle Integration

- [ ] Install Rev A with reversible harnessing.
- [ ] Start in `WOULD UNLOCK` / shadow mode.
- [ ] Validate passive approach detection in real parking scenarios.
- [ ] Validate departure / rearm behavior.
- [ ] Connect real lock/unlock output only after shadow-mode confidence.
- [ ] Validate OEM remote fallback.
- [ ] Validate mechanical key fallback.
- [ ] Validate ignition-on inhibits unwanted automatic actions.
- [ ] Validate wash/service pause behavior.
- [ ] Validate mirror behavior.
- [ ] Measure parked current over meaningful duration.

## 11. Future Keyless / Push-Button Start

_Not part of the current functional milestone._

- [ ] Define start-button behavior.
- [ ] Map accessory / ignition / starter circuits.
- [ ] Define clutch interlock.
- [ ] Define engine-running detection.
- [ ] Define immobilizer integration.
- [ ] Define emergency/manual fallback.
- [ ] Ensure phone/credential loss while driving cannot shut off engine.
- [ ] Implement start authorization as a separate safety-critical state machine.

## 12. Documentation / Checkpoints

- [x] Create project README.
- [x] Create project-state document.
- [x] Create PCB concept document.
- [x] Create troubleshooting log.
- [x] Create source/provenance record.
- [x] Archive known-good firmware privately.
- [x] Archive known-good iOS sample privately.
- [x] Record source commit hash.
- [x] Record firmware SHA-256.
- [ ] Update `PROJECT.md` after each meaningful milestone.
- [ ] Add new troubleshooting entries only when a problem is worth remembering.
- [ ] Keep GitHub public repo free of unverified third-party binaries/source.

---

## Current Next Three

- [ ] Add timestamped BLE / NI / ranging logs to isolate the ~13 s reacquisition delay.
- [ ] Repeat long-away / locked-phone return testing.
- [ ] Begin RDA electrical characterization on the Celica.
