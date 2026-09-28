# Case record and evidence limits

## Reported environment

- Device: Epson ET-2811, as identified in the repair conversation.
- Host: Windows with PowerShell and a Python virtual environment.
- Transport: USB; retained output identified the `usbprint` backend and D4 revision `0x10`.
- CLI model: `ET-2810`.
- Physical pads: replacement was described in the prior repair summary, not independently inspected.
- Firmware, original package versions, before-write raw transcript, and complete post-restart diagnostics: not available.

## Evidence ledger

| Observation | Evidence available |
| --- | --- |
| Main, borderless, and third waste counters were zero | Earlier assistant's repair summary; original query output unavailable |
| Overflow error and full maintenance-box status persisted | Earlier assistant's repair summary; original status output unavailable |
| `0x100` was initially `0x01` | Earlier assistant's repair summary; original before-read unavailable |
| `0x100` read back as `0x00` | User-pasted terminal command and output retained |
| Printer worked afterward | Owner's explicit success report following restart advice |
| Bit-clear operation exists in model data | Independently inspected local upstream source at the pinned revision |
| Current inspected parser omits nested closing step | Independently inspected parser and reset-loop source |

The owner reported success in Turkish. This repository records that outcome as an owner report; it does not claim a new independent hardware test. Nor does it establish long-term reliability, success on all firmware, or universal ET-2810/2811 compatibility.

## Retained command and sanitized output

```powershell
python .\epson_print_conf.py -m ET-2810 --usb -R 256 -d
```

The full prompt and USB connection line were removed to avoid publishing the Windows account name and device path. The retained diagnostic line is:

```text
EEPROM_ADDR 0x100 = 256: 0x00 = 0
```

The same extract is in [examples/readback.sanitized.txt](../examples/readback.sanitized.txt). It is a one-line excerpt, not a complete log. The missing before-output and final status are not filled in with simulated output.

## Source-based interpretation

The inspected XML maps the ET-2810 family to `L3250`. Its waste-reset definition contains ordinary counter writes and a nested final operation that reads address `0x100`, applies a mask of `0xFE`, and writes the result back.

The inspected `ez-reset` parser builds a dictionary from the reset element's direct text; its printer method writes that dictionary. The nested operation is not represented there. The inspected `epson_print_conf` ET-2812 profile, which includes ET-2810 and ET-2811 aliases, has a raw reset map without address `0x100`.

This supports the explanation that a separate reset-finalization bit was left set in the reported case. It is not proof of the complete internal firmware semantics or that all later upstream releases retain the same behavior. The closing operation was already present in upstream data; this project documents its application and the reported result rather than claiming to have invented it.

## Revision provenance

On 2026-09-29, the existing local upstream checkouts were inspected read-only. The `epson_print_conf` checkout had no tracked modifications. The `ez-reset` checkout had no tracked modifications and contained untracked environment/build artifacts, none of which are published here.

The inspected revisions are recorded in [ATTRIBUTION.md](../ATTRIBUTION.md). The original conversation did not capture revision identifiers at repair time, so these must not be described as proven versions used during the repair.
