# CeliKey Sources and Provenance

**Checkpoint:** 2026-08-18

This file records the exact external material used for the proof of concept and what must be preserved to reproduce it.

## 1. Smartlight_UWB

Repository:

```text
https://github.com/Zekke-e/Smartlight_UWB
```

Why it matters:

- Used after the Qorvo SDK download portal repeatedly failed.
- Contains the recovered Qorvo Nearby Interaction tree used for the DWM3001CDK.
- Contains the Qorvo-derived iOS Nearby Interaction sample used for the working background POC.

Exact source commit used:

```text
458bd834b9c5b69fc5d3c187859093a987bf8fec
```

### Licensing caution

No top-level license was visible in the recovered repository at this checkpoint. Do **not** assume its source or embedded Qorvo material may be redistributed publicly.

The known-good third-party material is therefore preserved privately under `reference/private/` and documented publicly here by source, commit, filename, and hash.

## 2. Known-Good DWM3001CDK Firmware

Known-good image:

```text
DWM3001CDK-QANI-FreeRTOS_full.hex
```

Recovered source path:

```text
UwbModuleDWM3001CDK/
  Qorvo_Nearby_Interaction_3_1_0/
    Software/Accessory/Sources/
      QANI-All-FreeRTOS_QNI_3_0_0/
        Projects/Projects/QANI/FreeRTOS/DWM3001CDK/
          ses/Output/Common/Exe/
            DWM3001CDK-QANI-FreeRTOS_full.hex
```

SHA-256:

```text
E5A0BB73BA69DBBF42AC00541B9B2B168942E5FC35C2D43F870A562021C73C39
```

Important: the non-`full` image was **not** the known-good standalone flash for this POC.

Private archive location:

```text
reference/private/
```

## 3. Qorvo Hardware

Development kit:

```text
DWM3001CDK
https://www.qorvo.com/products/p/DWM3001CDK
```

Planned custom-PCB module:

```text
DWM3001C
https://www.qorvo.com/products/p/DWM3001C
```

The project intends to use the integrated DWM3001C module rather than reproduce the raw UWB RF/antenna design.

## 4. Qorvo Nearby Interaction iOS Demonstrator

Initial validation app:

```text
Qorvo Nearby Interaction
App Store ID: 1615369084
```

Used to confirm:

- BLE connection,
- UWB distance,
- direction arrow,
- compatibility of the recovered full firmware with Apple's Nearby Interaction flow.

## 5. Qorvo-Derived iOS Source Project

Recovered through `Smartlight_UWB`.

Known working workspace:

```text
NINearbyAccessorySample.xcworkspace
```

Working scheme/target encountered:

```text
Qorvo NI Background
```

Known-good behavior:

- builds and installs on the physical iPhone,
- locked-screen/background UWB ranging works,
- secure-bubble notifications work,
- Apple Watch receives forwarded notifications,
- force-quitting the app stops passive behavior.

The **full known-good iOS project folder** is preserved privately under:

```text
reference/private/
```

Do not preserve only the `.xcworkspace`; the source/project/dependency structure is needed to reproduce the build.

## 6. Apple Nearby Interaction References

```text
https://developer.apple.com/documentation/nearbyinteraction
https://developer.apple.com/documentation/nearbyinteraction/ninearbyaccessoryconfiguration
```

The working sample behavior is consistent with Apple's BLE-paired Nearby Interaction accessory background model.

## 7. SEGGER J-Link

Tools used:

- J-Flash Lite
- J-Link RTT Viewer

Vendor:

```text
https://www.segger.com/products/debug-probes/j-link/
```

Known-good target settings:

```text
Device:     nRF52833_xxAA
Interface:  SWD
Speed:      4000 kHz
```

## 8. Toolchain Snapshot

```text
macOS: 15.7.7
Xcode: 26.3
iPhone: iPhone 12 Pro Max
iOS: 26.6
```

These versions are worth recording because Apple development-device and background behavior can change with toolchain/OS revisions.

## Private Archive Policy

Keep privately in Nextcloud / local storage:

- the known-good `DWM3001CDK-QANI-FreeRTOS_full.hex`,
- the full working Qorvo-derived iOS project folder,
- any future third-party snapshots needed for reproducibility,
- future modified firmware/app snapshots before they are cleanly separated into CeliKey-owned source.

Keep publicly in GitHub:

- CeliKey-created documentation,
- CeliKey-owned code/design files,
- source URLs,
- source commit IDs,
- binary hashes,
- patches or derived work only where licensing permits.
