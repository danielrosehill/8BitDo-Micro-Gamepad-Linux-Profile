# 8BitDo Micro gamepad: Linux profile (D mode)

Captured 2026-11-24 on Kubuntu over Bluetooth, mode switch on **D**. Machine-readable: [`device.json`](device.json).
Each mode is a different device: D `2dc8:9020` (gamepad, this file), K `2dc8:9021` (keyboard), S not checked.

| Button | evdev | joydev index (raw Gamepad API) | standard index (predicted) | Recorder action |
|---|---|---|---|---|
| Y (left) | `BTN_Y/WEST` 308 | 4 | 3 | `record` |
| A (right) | `BTN_A/SOUTH` 304 | 0 | 0 | `save` |
| X (top) | `BTN_X/NORTH` 307 | 3 | 2 | `retake` |
| B (bottom) | `BTN_B/EAST` 305 | 1 | 1 | `skip` |
| L | `BTN_TL` 310 | 6 | 4 | `last` |
| R | `BTN_TR` 311 | 7 | 5 | `pause` |
| L2 | `BTN_TL2` 312 | 8 | 6 | `skip2` |
| R2 | `BTN_TR2` 313 | 9 | 7 | `stop` |
| minus | `BTN_SELECT` 314 | 10 | 8 | `layouts` |
| plus | `BTN_START` 315 | 11 | 9 | `auto` |
| D-pad | left stick: `ABS_X` / `ABS_Y`, 0 / 127 / 255 | axes 0, 1 | 12 to 15 | +1 / -1 sentence, discard, silence |

L2 and R2 also send `ABS_BRAKE` / `ABS_GAS`. Home and star were not pressed. Labels are Nintendo's: A right, B bottom, X top, Y left.
Electron 38 (Chromium 140) has no mapping entry for this id (binary search of its table), so a page should get the raw order; this was not read from inside a browser.
8BitDo's FAQ: hold minus + D-pad up 5 s for a real D-pad, minus + left for left stick (not tried).

SDL line (built from the codes above, not tested in SDL; SDL button numbers follow evdev code order):

```
05000000c82d00002090000000010000,8BitDo Micro,a:b1,b:b0,x:b4,y:b3,leftshoulder:b6,rightshoulder:b7,lefttrigger:b8,righttrigger:b9,back:b10,start:b11,leftx:a0,lefty:a1,platform:Linux,
```

## Pairing: "Rejected connection from !bonded device"

Symptom: connected in `bluetoothctl` (Paired yes, **Bonded no**), no `/dev/input` node, and bluetoothd logs that line. Fix: hold **pair** (bottom
edge) until the light blinks fast, then in one `bluetoothctl` session: `remove <MAC>`, `agent NoInputNoOutput`, `default-agent`, `pairable on`,
`scan bredr`, `pair <MAC>`, `trust <MAC>`, `connect <MAC>`. Each mode has its own address and pairs separately.
