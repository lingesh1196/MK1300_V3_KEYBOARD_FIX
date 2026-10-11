# MK1300 V2 toolkit (Ant Esports, USB ID 36ae:fead)

Browser tools (Chrome or Edge, WebHID) to control and repair an Ant Esports MK1300 V2 keyboard without the official software.
Open an .html file in Chrome, click Connect, pick the keyboard. Close the official software first.

| File | What it does | Writes? |
|---|---|---|
| `MK1300_Control_Panel.html` | Lighting effects, color, speed, key remap, backup/restore | yes (safe commands only) |
| `MK1300_FINAL_fix_everything.html` | Check, then repair every key (live + stored copy), Fn layer defaults, lighting default | yes |
| `MK1300_compare_copies_readonly.html` | Compares stored copy, live copy and official read | no |
| `MK1300_key_audit_readonly.html` | Lists empty or wrong keys on both layers | no |
| `MK1300_lighting_readonly.html` | Shows lighting state and modes | no |

**Never** write colors past the normal color area, and never use the old "RGB Studio" page: see `docs/NOTES.md`.

Not affiliated with Ant Esports. Use at your own risk.
