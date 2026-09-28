# Contributing

Useful contributions include corrections to source interpretation, clearer translations, and independently documented results for an exact model and firmware.

Before opening an issue or pull request:

1. Read [SAFETY.md](SAFETY.md) and [docs/privacy.md](docs/privacy.md).
2. Identify the tool version/commit and distinguish actual terminal output from a reconstructed sequence or recollection.
3. State the model, firmware if known, physical service performed, counters, relevant status, and before/after byte values.
4. Explain whether the state was rechecked after restart. Record failures as well as successes.
5. Provide a small sanitized excerpt, never a full EEPROM dump or raw debug log.

Do not label a model as tested merely because it shares a profile. Do not turn this case into a blanket instruction to zero a memory address. Preserve attribution and check licensing before proposing copied code or model data.

The project does not promise device repair support or encourage speculative writes. General documentation corrections can be reported publicly. See [SECURITY.md](SECURITY.md) for sensitive reports.
