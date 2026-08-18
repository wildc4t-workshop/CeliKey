# CeliKey Troubleshooting

This file keeps only the problems that consumed meaningful time and the fixes/lessons worth remembering.

## 1. Qorvo SDK download portal would not provide the firmware

### Symptom
Qorvo's download workflow repeatedly failed instead of emailing/providing the requested SDK download.

### Working path
Used:

```text
https://github.com/Zekke-e/Smartlight_UWB
```

That repository contains a Qorvo Nearby Interaction tree including the DWM3001CDK FreeRTOS QANI project and the iOS sample project.

### Lesson
Preserve exact source provenance and local copies of known-good dependencies. Do not assume vendor download portals will remain usable.

---

## 2. Windows Git clone failed with `Filename too long`

### Symptom

Clone downloaded successfully but checkout produced many errors like:

```text
Filename too long
```

The Qorvo/Nordic SDK tree contains extremely deep paths.

### Fix

```powershell
git config --global core.longpaths true
```

Delete the partial checkout and clone again.

Known working clone command used:

```powershell
cd D:\Downloads
git clone https://github.com/Zekke-e/Smartlight_UWB.git CeliKey
```

---

## 3. DWM3001CDK J-Link appeared as `BULK interface`

### Symptom
- Board connected to the J-Link/programming USB port.
- Windows Device Manager showed `BULK interface` with a warning.
- J-Flash Lite reported no USB probe.

### Fix / working path
- Install/use SEGGER J-Link USB support.
- Allow the onboard J-Link firmware update when prompted.
- After the driver/firmware setup, J-Flash Lite recognized the target.

### Known working J-Flash settings

```text
Device:     nRF52833_xxAA
Interface:  SWD
Speed:      4000 kHz
```

---

## 4. Normal QANI HEX flashed but the application did not boot correctly

### Symptom
Flashed:

```text
DWM3001CDK-QANI-FreeRTOS.hex
```

but:
- iPhone did not discover the accessory,
- RTT Viewer could not find a valid RTT control block/application output.

### Fix
Flash:

```text
DWM3001CDK-QANI-FreeRTOS_full.hex
```

After flashing the full image:
- the application booted,
- a board LED changed,
- RTT Viewer found the control block,
- logs identified the Nearby Interaction application,
- BLE advertising/ranging worked.

### Verification
Use SEGGER RTT Viewer through J-Link. A healthy boot showed application/board/FreeRTOS information and BLE advertising messages.

### Reset button
`SW1` is the board reset button. `SW2` is not the normal system reset.

---

## 5. Finding the ranging variable / threshold code

From the QANI source root:

```powershell
git grep -n "D_cm" -- "*.c" "*.h"
```

Found:

```text
Projects/Src/Apps/Src/fira/QANI/fira_niq.c
```

The callback computes:

```c
distance_cm = (int)rm->distance_mm / 10;
```

The recovered source already contained enter/exit hysteresis logic and onboard LED actions.

Stock thresholds found:

```c
#define MIN_CM_DISTANCE_THRESHOLD 100
#define MAX_CM_DISTANCE_THRESHOLD 180
```

This became the first functional passive-entry POC without adding external hardware.

---

## 6. iPhone visible in Finder but unavailable to Xcode

### Symptom
- Finder saw and trusted the iPhone.
- Xcode Device Hub showed no usable physical device.
- `xcrun devicectl list devices` showed the phone but `State = unavailable`.
- `devicectl` operations failed with CoreDevice error `1011`.
- Developer Mode was not initially visible on the iPhone.
- The Mac-side Apple USB Ethernet/CoreDevice interface existed but was inactive.

### Things investigated
- repeated Trust prompts,
- Mac restart,
- alternate USB ports,
- USB hub removal,
- simple USB adapter/direct connection,
- Tailscale/VPN/network-extension disable,
- CoreDevice/DDI compatibility,
- `remoted` restart,
- filesystem ownership,
- CoreDevice diagnostics.

### What actually got it working
**Reboot the iPhone.**

After the phone restart, CoreDevice pairing succeeded and Xcode began preparing the phone for development.

### Important nuance
The phone had initially been connected through a USB-C hub, so the hub may have contributed to the bad state, but it was **not proven to be the root cause**: the phone remained unavailable after moving to a simpler direct USB path until the iPhone itself was restarted.

### Future fast path
If Finder sees the phone but Xcode/CoreDevice says unavailable:

1. Use a simple direct USB/data path if possible.
2. Unlock the phone.
3. **Reboot the iPhone early.**
4. Reconnect.
5. Check:

```bash
xcrun devicectl list devices
```

Do this before spending hours reverse-engineering CoreDevice.

---

## 7. Xcode CodeSign asked for a password

### Symptom
Build stopped with:

```text
Command CodeSign failed with a nonzero exit code
```

macOS displayed a prompt that `codesign` wanted access to the signing key in the `login` keychain.

### Fix
Use the **macOS login keychain password**, not the Apple/iCloud password.

When appropriate, choose:

```text
Always Allow
```

so `/usr/bin/codesign` can use the development signing key without repeatedly prompting.

---

## 8. Background ranging stops if the app is force-quit

### Observed behavior
- Screen off / phone locked / app merely backgrounded: **works**.
- Swipe up and explicitly kill the app: **passive behavior stops**.

### Interpretation
Treat force-quit as a deliberate user stop condition unless later design work requires a different UX.

---

## 9. Long-away return works, but reacquisition is slow

### Test
- App left backgrounded.
- Phone taken away for roughly one hour.
- Returned to the board without manually reopening the app.
- Secure-bubble notification arrived after approximately **13 seconds**.

### Status
Functional result: **PASS** for autonomous recovery at POC level.

Unresolved:
- where the ~13 s latency occurs.

### Next diagnostic
Add timestamps for:
- BLE disconnected,
- BLE connected,
- NI session start/resume,
- first valid UWB range,
- secure-bubble event.
