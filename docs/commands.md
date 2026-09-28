# Windows PowerShell command guide

Read [SAFETY.md](../SAFETY.md) first. These commands are documented examples, not a script to run from top to bottom. They were checked against the inspected upstream source; they were not re-executed against a printer while preparing this repository.

## 1. Prepare the upstream tool

Use a supported Python version and the upstream installation instructions. The inspected requirements enable the USB dependency on Python 3.10 or later; dependency compatibility can change. For a fresh checkout:

```powershell
git clone https://github.com/Ircama/epson_print_conf.git
Set-Location .\epson_print_conf
git checkout 67f03e716e85fc3c38870efe7e8dbf7df85413ce
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe .\epson_print_conf.py --help
```

This pins the source inspected for this guide, not the complete dependency set or a proven reproduction environment for the original repair. Review upstream changes before choosing a newer version. Do not change an existing working checkout to this revision just to follow the example.

For the remaining commands, `python` means the interpreter from the upstream tool's environment. Either activate that environment according to your local policy or substitute `.\.venv\Scripts\python.exe` for `python`; no execution-policy change is needed.

Connect only the intended printer by USB. Finish print jobs and close other printer utilities to avoid competing USB access. The original read-back used model alias `ET-2810` for the reported ET-2811. This is not permission to select a similar model for an unrelated printer.

## 2. Read the current state

```powershell
python .\epson_print_conf.py -m ET-2810 --usb -q printer_status
python .\epson_print_conf.py -m ET-2810 --usb -q waste_ink_levels
python .\epson_print_conf.py -m ET-2810 --usb -R 256
```

`256` is decimal for `0x100`. Stop on connection errors, missing values, an unsupported model, or an unexpected status. The tool can print an EEPROM read error without a failing process exit code, so inspect the actual output.

## 3. Keep a private record before considering writes

Choose a private local folder outside any public repository or shared cloud folder. The following folder name is an example; do not later upload it:

```powershell
New-Item -ItemType Directory -Force .\private-backups | Out-Null
python .\epson_print_conf.py -m ET-2810 --usb -q printer_status 2>&1 |
    Out-File .\private-backups\status-before.txt -Encoding utf8
python .\epson_print_conf.py -m ET-2810 --usb -q waste_ink_levels 2>&1 |
    Out-File .\private-backups\counters-before.txt -Encoding utf8
python .\epson_print_conf.py -m ET-2810 --usb -R 256 2>&1 |
    Out-File .\private-backups\byte-0100-before.txt -Encoding utf8
```

Open these files and verify that the queries actually succeeded. This preserves the byte involved in the example and diagnostic context; **it is not a complete EEPROM backup or a guaranteed recovery method**. Obtain any further backup required by your service procedure before modifying the printer. Never restore another printer's dump.

## 4. Understand the original counter-reset stage

The case used `ez-reset` before the additional byte operation. Its documented GUI entry point is `python -m ez_reset` after installation from that project's own instructions; the GUI offers **Reset All**. That action writes printer state. Do not repeat it when the counters are already zero, and do not use it before physical pad service.

The original complete sequence of clicks and tool versions is unavailable. This guide therefore does not present the counter-reset stage as a fully reproduced procedure. Follow the exact model's current upstream/service documentation if counters still need resetting.

## 5. Conditional write example: only for the recorded `0x01` case

Proceed only after physical pad service and review of the model definition, with a private backup, successful reads, counters confirmed at zero, the same remaining overflow/full status, and the byte at `0x100` confirmed as **exactly `0x01` immediately before writing**.

If any condition differs, stop. If the byte is already `0x00`, this specific operation has nothing to change. This example deliberately does not automate an arbitrary read/modify/write across models.

The definition clears bit 0 with `old_value & 0xFE`. For exactly `0x01`, the source-verified CLI syntax for that result is:

```powershell
# MUTATING COMMAND: valid here only for a verified current value of 0x01.
python .\epson_print_conf.py -m ET-2810 --usb -W "256:0"
```

This is a reconstruction from the inspected CLI parser and reported byte transition. The original write command itself is not retained in the available conversation. Never treat `-W "256:0"` as a universal reset command. Do not blindly retry a failed or ambiguous write.

## 6. Verify, restart, and verify again

Read back in a separate command:

```powershell
python .\epson_print_conf.py -m ET-2810 --usb -R 256
```

For the specific transition above, the result should be:

```text
EEPROM_ADDR 0x100 = 256: 0x00 = 0
```

If the read fails or the value differs, stop and investigate. An acknowledgment alone is not proof of a successful write.

Once no write or print job is active, shut down using the printer's power button and wait for shutdown to finish. Restart normally following the printer's instructions. Do not remove power during a write or while the power light is flashing.

After restart, run the status, waste-level, and byte queries from step 2 again. Confirm that the error is gone and the byte remained at its intended value. A small test print and an inspection for leaks can provide additional checks after service; these checks were not preserved as logs in the original case. If the error remains, seek model-specific diagnosis instead of trying other addresses.
