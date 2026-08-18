# CeliKey Sources and Provenance

**Checkpoint date:** 2026-08-18

This file records what the current proof-of-concept was built from and what should be preserved.

## 1. Smartlight_UWB — primary recovered source tree

Repository:

```text
https://github.com/Zekke-e/Smartlight_UWB
```

Why it matters:
- Used after the Qorvo SDK download portal repeatedly failed.
- Contains:
  - `UwbModuleDWM3001CDK`
  - Qorvo Nearby Interaction 3.1.0 material
  - `QANI-All-FreeRTOS_QNI_3_0_0`
  - DWM3001CDK QANI source/build output
  - `iOSModule`
  - Qorvo-derived `NINearbyAccessorySample`

The repository README states that the project used the Qorvo SDK and that its iOS app was based on Qorvo's example project.

### Exact commit — capture this from the local clone

Run inside the locally cloned repository:

```bash
git rev-parse HEAD
```

Record result here:

```text
Smartlight_UWB commit: TO CAPTURE FROM LOCAL CLONE
```

### Licensing caution
No top-level license file was visible in the GitHub repository at this checkpoint. Do **not** assume the repository or embedded Qorvo materials can be redistributed in a public CeliKey repository.

Keep private reference snapshots in Nextcloud/local storage unless licensing is verified.

---

## 2. Known-good DWM3001CDK firmware

Recovered path:

```text
UwbModuleDWM3001CDK/
  Qorvo_Nearby_Interaction_3_1_0/
    Software/Accessory/Sources/
      QANI-All-FreeRTOS_QNI_3_0_0/
        Projects/Projects/QANI/FreeRTOS/DWM3001CDK/
          ses/Output/Common/Exe/
            DWM3001CDK-QANI-FreeRTOS_full.hex
```

Known-good image:

```text
DWM3001CDK-QANI-FreeRTOS_full.hex
```

The non-`full` image was **not** the known-good standalone flash for this POC.

### Capture the exact binary hash

On macOS:

```bash
shasum -a 256 DWM3001CDK-QANI-FreeRTOS_full.hex
```

On Windows PowerShell:

```powershell
Get-FileHash .\DWM3001CDK-QANI-FreeRTOS_full.hex -Algorithm SHA256
```

Record result here:

```text
QANI full HEX SHA-256: TO CAPTURE FROM LOCAL FILE
```

---

## 3. Qorvo hardware

DWM3001CDK product page:

```text
https://www.qorvo.com/products/p/DWM3001CDK
```

Future custom-PCB module:

```text
https://www.qorvo.com/products/p/DWM3001C
```

Current architecture assumes a DWM3001C module on the eventual PCB rather than reproducing the raw UWB RF/antenna design.

---

## 4. Qorvo Nearby Interaction iOS demonstrator

App Store identifier used during the initial POC:

```text
Qorvo Nearby Interaction
App Store ID: 1615369084
```

Purpose:
- initial iPhone ↔ DWM3001CDK validation,
- live distance,
- direction arrow,
- confirmation that the recovered full firmware could establish a Qorvo/Apple Nearby Interaction session.

---

## 5. Qorvo-derived iOS source project

Recovered through the `Smartlight_UWB` repository.

Top-level Xcode workspace opened:

```text
NINearbyAccessorySample.xcworkspace
```

Working scheme/target encountered:

```text
Qorvo NI Background
```

POC result:
- built locally in Xcode,
- installed on the physical iPhone,
- background/locked-screen UWB ranging worked,
- secure-bubble notifications worked,
- Apple Watch displayed the forwarded notifications,
- force-quitting the app stopped operation.

---

## 6. Apple Nearby Interaction documentation

Main framework:

```text
https://developer.apple.com/documentation/nearbyinteraction
```

Accessory configuration:

```text
https://developer.apple.com/documentation/nearbyinteraction/ninearbyaccessoryconfiguration
```

Background-capable BLE-paired accessory initializer:

```text
https://developer.apple.com/documentation/nearbyinteraction/ninearbyaccessoryconfiguration/init(accessorydata:bluetoothpeeridentifier:)
```

Apple documents background UWB operation for BLE-paired and connected accessories; this matches the behavior successfully demonstrated in the source-built sample app.

---

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
nRF52833_xxAA
SWD
4000 kHz
```

---

## 8. Toolchain / Device Snapshot

Observed during this checkpoint:

```text
MacBook Pro: Intel-era MBP used for Xcode development
macOS: 15.7.7
Xcode: 26.3
iPhone: iPhone 12 Pro Max
iOS: 26.6
```

These versions are worth preserving because future behavior may differ.

---

## Private Reference Snapshot Checklist

For a durable **private** archive in Nextcloud, preserve:

1. The complete local `Smartlight_UWB` clone used for the POC.
2. Its `git rev-parse HEAD` result.
3. The known-good `DWM3001CDK-QANI-FreeRTOS_full.hex`.
4. The SHA-256 of that HEX file.
5. Any future modified QANI source.
6. The future modified CeliKey iOS source.

For a **public GitHub repository**, keep the CeliKey-created files and provenance documentation, but do not publish third-party/Qorvo source or binaries until their redistribution terms are verified.
