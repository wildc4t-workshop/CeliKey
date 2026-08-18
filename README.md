# CeliKey

CeliKey is a reversible, OEM-style passive-entry project for a **2000 Toyota Celica GT-S**.

The current concept uses **BLE + UWB** for phone-based proximity access while preserving the factory Toyota key/remote path. The architecture also reserves room for **NFC backup access, power-folding mirror control, and future keyless/push-button start**.

## Current Status

The core phone/UWB proof of concept works:

- DWM3001CDK boots known-good Qorvo Nearby Interaction firmware.
- iPhone UWB distance and direction ranging work.
- Accessory-side distance thresholds and hysteresis work.
- Ranging continues with the iPhone locked and the app backgrounded.
- After roughly one hour away, background recovery occurred automatically on return, with about **13 s** of observed reacquisition latency.
- Force-quitting the iOS app stops passive operation.

Vehicle lock integration has **not** been connected yet. The preferred path is still to characterize and, if viable, emulate the Toyota wireless receiver's **RDA** command path into the Body ECU.

## Documentation

- [`PROJECT.md`](PROJECT.md) — current status, milestones, open questions, and next work.
- [`CELIKEY_PCB_CONCEPT.md`](CELIKEY_PCB_CONCEPT.md) — hardware/vehicle architecture and PCB requirements.
- [`TROUBLESHOOTING.md`](TROUBLESHOOTING.md) — problems that consumed meaningful time and the fixes worth remembering.
- [`SOURCES.md`](SOURCES.md) — exact third-party provenance, firmware hash, commit IDs, and archive notes.
- [`reference/README.md`](reference/README.md) — public/private reference-storage policy.

## Current Focus

1. Instrument the iOS sample to locate the source of the ~13 s background reacquisition delay.
2. Preserve the known-good background behavior while reducing the sample into a minimal CeliKey app.
3. Characterize the Celica RDA line before designing the final vehicle interface.

## Reference Material

Known-good third-party firmware and the full working iOS sample are archived locally under:

```text
reference/private/
```

That directory is intentionally excluded from the public repository. See [`SOURCES.md`](SOURCES.md) for provenance and hashes.
