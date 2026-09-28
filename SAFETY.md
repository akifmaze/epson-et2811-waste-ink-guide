# Hardware safety and limitations

## Physical service comes first

A waste ink counter estimates discarded ink; resetting it does not empty, replace, or repair an absorbent pad. Do not clear the warning to keep printing with saturated pads. Follow the manufacturer's service instructions or use a qualified technician. Stop using the printer if ink is leaking.

## EEPROM writes are model specific

- Verify the physical model, applicable profile, current byte, and relevant firmware limitations before changing memory.
- Keep a private backup before writing. A saved single byte is not a full recovery image.
- Never write `0x00` simply because another owner did so. The referenced operation preserves other bits by using `old_value & 0xFE`.
- Do not try random addresses, change unrelated settings, use another device's EEPROM dump, or repeatedly retry an ambiguous write.
- Keep power and USB stable during writes. Allow a normal shutdown before disconnecting power.
- Read back independently, then recheck state after restart. A successful command exit or acknowledgment is insufficient.

## What this project does and does not establish

There is one owner-reported ET-2811 recovery, an actual post-write byte read-back, and a source inspection. There is no hardware test matrix, validated ET-2810 unit, complete original before/after transcript, or warranty of effectiveness. Different firmware or errors may require a different diagnosis.

Do not treat a cleared warning as proof of physical safety, print quality, or complete repair. The maintenance-box field is the tool's decoded status label; it does not establish that this printer has a user-replaceable maintenance cartridge.

This repository is independent community documentation. It is not Epson service documentation or an Epson-approved tool. The [MIT License](LICENSE) contains the warranty terms for this repository's own material.
