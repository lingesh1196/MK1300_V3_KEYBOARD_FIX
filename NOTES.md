# Protocol notes (MK1300 V2, 36ae:fead, profile Z61-ARGB)

Device: HID interface usagePage 0xFF00, usage 0x02. 64-byte reports, report id 0. Byte 0 is the command (6), then the payload.
Replies start with 0xAA; data starts at reply byte 8.

## Keys
- Read keys:   payload `[8, 58, offLo, offHi, 0, layer]`, 56 bytes per read, 4 bytes per key (key number x 4). Layer 0 = base, 1 = Fn.
- Write a key: payload `[16, 7, offLo, offHi, 0, layer, 0, b0, b1, b2, b3]` with offset = 4 x key number.
- Key record: normal key `32,0,HIDcode,0`; modifier `32,bitmask,0,0` (Ctrl 1, Shift 2, Alt 4, Win 8, then right side x16);
  lighting function `31,code,0,0` (0 off, 1 bright+, 2 bright-, 3 effect, 4 color, 5 speed+, 6 speed-, 7 win lock, 16 reset, 36 WASD/arrows).
- Key numbers: Esc 0, number row 1-13, Tab 15, Q row 16-28, Caps 30, A row 31-41, Enter 43, Shift 45, Z row 47-56, Shift 58, Ctrl 60, Win 61, Alt 62, Space 66, AltGr 70, Fn 71, Lighting 72, Ctrl 73.

## Lighting
- Read:  `[10]`, state = reply bytes 5..15 = type, 0, mode, brightness, speed, direction, colorFlag, single, h, s, v.
- Write: `[11, 11, 0, 0, ...those 11 bytes]`. Modes 0-19 only. There is no per-key "custom" mode on this firmware.
- Color memory: read `[19, 58, offLo, offHi]`, write `[18, len+3, offLo, offHi, 0, 0, 0, ...data]`.

## What broke the keyboard (lessons)
- The color memory (74 keys x 3 bytes = 222 bytes) sits directly before the stored key tables in memory.
  Writing more than 222 color bytes (the old RGB Studio page wrote 357) overwrote the stored copy of keys 0-32.
- Memory map (color-memory offsets): stored base layer copy at 228 (75 slots x 4 bytes), live base layer at 528, Fn layer at 828.
- Every color write also overwrites the 4 bytes right after the written range. End such writes on a slot that has no physical key.
- A keyboard reset (Fn+Space, or command `[15,255]`) reloads the live layer from the stored copy.
- Official key writes (`16,7`) only change the live copy and do not touch neighbours.
- The official Electron software only whitelists product ID FE12; FEAD is commented out in `main/assets/vid_pid_offline.js`
  and `productId:65042` in the page code. Patching both and repacking `app.asar` makes it connect.
