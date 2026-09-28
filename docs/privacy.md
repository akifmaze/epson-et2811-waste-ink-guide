# Sharing diagnostics without personal data

The published case uses a minimal excerpt instead of a raw transcript. The Windows prompt and the entire USB connection line were removed; only the relevant EEPROM read-back was retained. No printer dump, full conversation, screenshot, or personal account information is included.

## Review before publishing

1. Keep originals and backups in a private location outside this repository.
2. Make a new extract containing only the fields needed to explain the result.
3. Remove Windows/macOS/Linux usernames and home-directory paths; printer serial numbers; complete USB interface/device instance paths; IP and MAC addresses; hostnames; Wi-Fi names; tokens; and unrelated history.
4. Inspect both structured fields and free-form/debug text. Device identifiers may appear inside escaped strings or binary/hex output.
5. Open the final extract and review every line. Generic search-and-replace is not sufficient proof that a raw log is anonymous.
6. Review staged files and diffs before committing. Git history keeps previously committed content, even after deleting it in a later commit.

Never publish a full EEPROM dump: it may contain identifiers or settings that are not obvious in plain text. Avoid screenshots when a short text extract will do. Do not upload files first and clean them later.

## Useful non-identifying report fields

- Model and firmware version, without serial number.
- Operating system and tool commit or release.
- Whether the physical waste pads were serviced.
- Numeric waste counters and relevant error labels.
- The byte at `0x100`, only when that address is applicable to the model.
- Exact intended change, read-back result, and result after restart.

The `.gitignore` provides guardrails for common raw-log and backup names. It does not prevent every privacy leak or remove files already tracked. Public reports should include only manually reviewed excerpts like [the supplied example](../examples/readback.sanitized.txt).
