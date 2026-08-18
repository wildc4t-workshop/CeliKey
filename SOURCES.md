# CeliKey Sources and Provenance

**Checkpoint:** 2026-08-18

This file records external material that materially informed the CeliKey design or proof of concept.

## 1. Toyota Vehicle Wiring

### 2000 Celica Electrical Wiring Diagram — EWD399U

Factory publication:

```text
Toyota Celica Electrical Wiring Diagram — 2000 Model
Publication: EWD399U
Applicable platform: ZZT230 / ZZT231
```

Relevant section: **Overall Electrical Wiring Diagram — Multiplex Communication System**, printed pages **207–208**.

The diagram identifies:

```text
D2 — Door Control Receiver

D2 pin 2  ↔ RDA ↔ Body ECU connector B7 (connector B), pin 10
D2 pin 3  ↔ PRG ↔ Body ECU connector B7 (connector B), pin 9
D2 pin 5  → +B
D2 pin 1  → GND
```

The EWD establishes these connections and signal names but does not mark RDA or PRG signal direction. The working interpretation is that RDA carries receiver data toward the Body ECU and PRG carries programming/communication from the Body ECU toward the receiver; electrical characterization must confirm that interpretation. This is the source of the current preferred RDA interception/emulation hypothesis.

Public reference mirrors / indexes used during research:

```text
https://www.scribd.com/doc/80237543/51-Overall-Electrical-Wiring-Diagram
https://www.car-inform.com/celica/
https://celica-club.lv/forums/viewtopic.php?t=538
```

See `reference/RDA_PATH.md` for a project-created redraw.

Because this Celica's installed security/immobilizer arrangement includes retrofitted Toyota hardware, verify connector identity, wire color, voltage, and continuity on the actual car before connecting test equipment.

### Toyota RDA reverse-engineering / prior art

#### Toyota 4Runner Forum — OEM Fob with Remote Start Solved

- [Original thread](https://www.toyota-4runner.org/4th-gen-t4rs/232473-oem-fob-remote-start-solved.html)
- [Page 2 — measurement method and timing data](https://www.toyota-4runner.org/4th-gen-t4rs/232473-oem-fob-remote-start-solved-2.html)

The author reports reverse-engineering the RDA messages on a 2008 4Runner and building a custom board that monitors the RDA line. Page 2 is the most technically valuable part of the thread: it describes using an Arduino with optocoupler isolation and publishes measured pulse timing for LOCK, UNLOCK, and PANIC messages.

This is useful prior art for CeliKey's protected-input and waveform-analysis approach, but the reported 4Runner voltage levels, timing, and frames must not be assumed to apply to the 2000 Celica. Capture the Celica's signals directly.

### Cross-model Toyota architecture references

#### YotaTech — Adding Door Lock Control Receiver to a 2006 Tundra Double Cab

[Forum thread](https://www.yotatech.com/forums/f131/adding-door-lock-control-receiver-2006-tundra-dc-298856/)

The referenced Tundra receiver uses the same four-signal architecture seen in the Celica documentation: `B+`, `GND`, `PRG`, and `RDA`. The thread supports the broader Toyota architecture pattern but is not evidence that the Tundra and Celica use identical voltage levels, messages, or timing.

#### Toyota Tacoma service-manual mirror — Wireless Door Lock Control ECU terminals

[Terminals of ECU / Wireless Door Lock Control](https://www.ttguide.net/terminals_of_ecu-1819.html)

The Tacoma diagnostic table explicitly describes:

- `RDA` as a signal output from the Door Control Receiver to the Main Body ECU, with pulse generation when a transmitter button is pressed;
- `PRG` as a signal input to the Door Control Receiver from the Main Body ECU, with pulse generation during a documented key-state transition.

This cross-model factory-style diagnostic information supports the current working interpretation of RDA and PRG direction. It does not replace electrical verification on the Celica because the Tacoma receiver, connector pins, steady-state voltages, and protocol may differ.

## 2. NewCelica — JDM Power-Folding Mirror Prior Art

Thread:

```text
https://www.newcelica.org/threads/power-folding-mirrors-from-jdm-zzt23-photo-amp-video-story.348388/
```

Why it matters:

- documents 7th-gen Celica JDM power-folding mirror installation;
- reports that the OEM folding function is controlled by the switch without a separate mirror ECU/relay;
- identifies two additional folding-control conductors;
- documents a prior external-module implementation.

Project notes recorded from the thread:

```text
JDM switch fold-control colors reported:
- Light Green / Black
- Blue / Black
```

Prior external-module behavior described:

```text
double LOCK remote action → fold mirrors
ignition ON              → unfold mirrors
```

Treat this as prior art, not a substitute for bench-testing the actual mirrors.

## 3. Smartlight_UWB — Recovered Qorvo Development Source

Repository:

```text
https://github.com/Zekke-e/Smartlight_UWB
```

Exact commit used:

```text
458bd834b9c5b69fc5d3c187859093a987bf8fec
```

Why it matters:

- used after the Qorvo SDK download portal failed;
- contains the Qorvo Nearby Interaction source tree used for the DWM3001CDK;
- contains the Qorvo-derived iOS Nearby Interaction sample used for the background POC;
- provided the known-good full firmware image.

Relevant areas:

```text
UwbModuleDWM3001CDK/
iOSModule/
```

No top-level license was visible at the project checkpoint. Do not assume its contents or embedded Qorvo material may be redistributed publicly.

## 4. Known-Good DWM3001CDK Firmware

Known-good image:

```text
DWM3001CDK-QANI-FreeRTOS_full.hex
```

SHA-256:

```text
E5A0BB73BA69DBBF42AC00541B9B2B168942E5FC35C2D43F870A562021C73C39
```

The non-`full` image was not the known-good standalone flash for this POC.

Private archive:

```text
reference/private/Qorvo/
```

## 5. Qorvo DWM3001C / DWM3001CDK

Primary module reference:

```text
https://www.qorvo.com/products/p/DWM3001C
```

The future PCB concept uses the integrated DWM3001C module rather than reproducing the raw UWB RF/antenna design.

Qorvo's module page also links the DWM3001CDK evaluation kit, schematic, data sheet, and design resources.

## 6. Apple Nearby Interaction

Framework:

```text
https://developer.apple.com/documentation/nearbyinteraction
```

Accessory configuration:

```text
https://developer.apple.com/documentation/nearbyinteraction/ninearbyaccessoryconfiguration
```

Background-capable BLE-paired initializer:

```text
https://developer.apple.com/documentation/nearbyinteraction/ninearbyaccessoryconfiguration/init(accessorydata:bluetoothpeeridentifier:)
```

These are the primary Apple references for third-party UWB ranging and background interaction with BLE-paired accessories.

## 7. Car Connectivity Consortium Digital Key

Public architecture reference:

```text
https://carconnectivity.org/car-connectivity-consortium-publishes-digital-key-release-3-0-businesswire/
```

Current specification access page:

```text
https://carconnectivity.org/digital-key-specification-download-2/
```

CCC Digital Key is used as an architectural reference for the general BLE + UWB + NFC model. CeliKey is not currently claiming CCC certification or full specification compliance.

## 8. Qorvo-Derived iOS Sample

Recovered through `Smartlight_UWB`.

Known working workspace:

```text
NINearbyAccessorySample.xcworkspace
```

Working scheme/target:

```text
Qorvo NI Background
```

Known-good behavior includes physical-iPhone installation, UWB ranging, locked-screen/background operation, notifications, and autonomous recovery after a long absence.

Preserve the entire working iOS project privately under:

```text
reference/private/Qorvo/Qorvo_NI_Background_v1.0.0/
```

## 9. SEGGER J-Link Tools

Vendor:

```text
https://www.segger.com/products/debug-probes/j-link/
```

RTT Viewer:

```text
https://www.segger.com/products/debug-probes/j-link/tools/rtt-viewer/
```

Tools used:

- J-Flash Lite
- J-Link RTT Viewer

Known-good target settings:

```text
Device:     nRF52833_xxAA
Interface:  SWD
Speed:      4000 kHz
```

## 10. Flipper Zero Logic Analyzer Experiment

App catalog:

```text
https://catalog.flipperzero.one/application/68ed3134721e74ef7e6a3ac2/page
```

Source:

```text
https://github.com/g3gg0/flipper-logic_analyzer
```

Evaluated as a possible PulseView-based RDA capture tool. The current setup failed to expose the expected analyzer serial endpoint on Windows, so the experiment was abandoned in favor of a portable oscilloscope approach. Keep the detailed failure path in `TROUBLESHOOTING.md`.

## 11. Private Reference Archive

Recommended layout:

```text
reference/
├── README.md
├── RDA_PATH.md
└── private/
    ├── EWD399U.pdf
    └── Qorvo/
        ├── DWM3001CDK-QANI-FreeRTOS_full.hex
        ├── DWM3001CDK_Quick_Start_Guide_QM33SDK-1.0.0.pdf
        └── Qorvo_NI_Background_v1.0.0/
            └── [full known-good iOS project]
```

Public GitHub should contain CeliKey-created documentation, links, hashes, commit IDs, and project-created diagrams—not third-party manuals or binaries unless redistribution rights are clear.
