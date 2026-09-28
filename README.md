# Epson ET-2810 / ET-2811 waste ink reset notes

**A documented ET-2811 recovery case: counters were reported as zero, but the printer still reported a full maintenance box.**

[Türkçe rehber](README.tr.md) · [Commands](docs/commands.md) · [Case evidence](docs/case-study.md) · [Safety](SAFETY.md) · [Credits](ATTRIBUTION.md)

This project records an owner's successful recovery using existing community tools. Its contribution is the diagnosis, a reproducible explanation of an additional reset step, and a privacy-conscious case record. The USB protocol, reset tools, and model definitions come from the upstream projects credited below.

> **Service the waste ink pads before resetting.** A software reset does not remove ink. Writing the wrong EEPROM value can damage configuration or make the printer unusable. Read [SAFETY.md](SAFETY.md) before using any write command.

## What happened

The repair conversation describes this sequence:

1. The waste ink pads had been replaced.
2. After an attempted reset with `ez-reset`, all three waste counters were reported as zero.
3. `epson_print_conf` still reported `Ink overflow error` and `maintenance_box_1: full (2)`.
4. The byte at EEPROM address `0x100` was reported as `0x01`. Clearing its lowest bit changed it to `0x00`.
5. A retained user-pasted read-back confirms `EEPROM_ADDR 0x100 = 256: 0x00 = 0`. The owner subsequently reported that the printer worked.

The available evidence includes that read-back and the owner's success report. The earlier values and pad replacement are recorded in the conversation's repair summary; a complete before/after terminal log and firmware version are unavailable. See the [case study](docs/case-study.md) for the evidence boundary.

## Why counters alone were not enough

The inspected `ez-reset` device definition maps the ET-2810/2811 family to its `L3250` profile. That profile contains a final reset operation equivalent to:

```text
address = 0x100
new_value = old_value & 0xFE
```

This clears bit 0 and preserves bits 1–7. In the reported case, `0x01 & 0xFE` is `0x00`. **It does not mean that every printer should receive `0x00` at this address.** For example, `0x05 & 0xFE` is `0x04`.

At the inspected revision, the `ez-reset` parser reads the direct reset address/value text but does not interpret the nested closing operation. The inspected `epson_print_conf` profile also omits address `0x100` from its raw reset list. These source observations support the explanation; the exact firmware meaning of this bit has not been independently reverse engineered here. Newer upstream versions may behave differently.

Source links and exact revisions are in [ATTRIBUTION.md](ATTRIBUTION.md).

## Scope and compatibility

| Item | Evidence / status |
| --- | --- |
| Epson ET-2811 | One owner-reported successful recovery |
| Epson ET-2810 | Related model definition; no separate hardware test in this project |
| Other models | Not validated; do not generalize this address or procedure |
| Connection | Windows PowerShell, USB; `-m ET-2810 --usb` in the retained command |
| Firmware / original dependency versions | Not captured |
| Long-term reliability / print quality | Not measured |

## Start with read-only diagnostics

Install and review upstream `epson_print_conf` using the [command guide](docs/commands.md). From its folder, with its Python environment active:

```powershell
python .\epson_print_conf.py -m ET-2810 --usb -q printer_status
python .\epson_print_conf.py -m ET-2810 --usb -q waste_ink_levels
python .\epson_print_conf.py -m ET-2810 --usb -R 256
```

These queries can print device identifiers. Keep raw output private. A zero counter alone does not establish that the pads are safe or that a reset succeeded.

The [command guide](docs/commands.md) separates setup, diagnostics, private backup, the narrowly scoped write example, read-back, and restart checks. This repository contains documentation, not an automatic reset program. Preparing this guide did not perform any additional printer writes.

## Sharing a result

Read [the privacy guide](docs/privacy.md) before posting logs. Prefer a short, manually reviewed extract with model, firmware, tool revision, counter values, the relevant byte, and the outcome. Never upload a full EEPROM dump or a raw debug transcript.

Corrections and independently tested results are welcome; see [CONTRIBUTING.md](CONTRIBUTING.md). A related model name alone is not evidence of a successful test.

## Credits and license

- [Ircama/epson_print_conf](https://github.com/Ircama/epson_print_conf): status decoding, USB access, and EEPROM read/write commands.
- [CiRIP/ez-reset](https://github.com/CiRIP/ez-reset): the initial reset tool in the case, and the model definition containing the final bit-clear operation.

See [ATTRIBUTION.md](ATTRIBUTION.md) for source references and licensing boundaries. This repository's original writing and examples are under the [MIT License](LICENSE). No upstream implementation or device database is bundled or relicensed. Epson names are used for identification; this project is not affiliated with or endorsed by Epson.
