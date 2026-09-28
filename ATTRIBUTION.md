# Attribution and source provenance

The diagnosis documented here depends on work by existing printer-tool developers. We did not develop the USB protocol, EEPROM access mechanism, or the original reset definition.

## Ircama / epson_print_conf

- Project: https://github.com/Ircama/epson_print_conf
- Contribution to the case: status decoding and USB EEPROM reads/writes.
- Inspected revision: `67f03e716e85fc3c38870efe7e8dbf7df85413ce`.
- [Model profile and aliases](https://github.com/Ircama/epson_print_conf/blob/67f03e716e85fc3c38870efe7e8dbf7df85413ce/epson_print_conf.py#L407-L434).
- [Read/write command parser](https://github.com/Ircama/epson_print_conf/blob/67f03e716e85fc3c38870efe7e8dbf7df85413ce/epson_print_conf.py#L3943-L3980).
- [Upstream license](https://github.com/Ircama/epson_print_conf/blob/67f03e716e85fc3c38870efe7e8dbf7df85413ce/LICENSE): EUPL-1.2, as stated in the inspected file.

## CiRIP / ez-reset

- Project: https://github.com/CiRIP/ez-reset
- Contribution to the case: initial counter-reset attempt; model data that describes the final bit-clear step.
- Inspected revision: `82fa560dc6b3f7f9b018441821d2bf9d7909ebcd`.
- [ET-2810/2811 family mapping](https://github.com/CiRIP/ez-reset/blob/82fa560dc6b3f7f9b018441821d2bf9d7909ebcd/src/ez_reset/devices.xml#L1043).
- [L3250 profile containing the reset-finalization operation](https://github.com/CiRIP/ez-reset/blob/82fa560dc6b3f7f9b018441821d2bf9d7909ebcd/src/ez_reset/devices.xml#L33856-L33889).
- [Model parser](https://github.com/CiRIP/ez-reset/blob/82fa560dc6b3f7f9b018441821d2bf9d7909ebcd/src/ez_reset/devices.py).
- [Reset implementation](https://github.com/CiRIP/ez-reset/blob/82fa560dc6b3f7f9b018441821d2bf9d7909ebcd/src/ez_reset/printer.py#L95-L97).

No repository-wide license file or license declaration in `pyproject.toml` was found in the inspected ez-reset checkout. Some individual files have third-party notices; that is not a blanket license for the whole project or database. We link to it and describe factual behavior without copying its implementation or redistributing its device database. Check upstream licensing before copying any of its files into another project.

## This repository's license choice

MIT is selected for the original documentation and short command examples in this repository to permit broad reuse with attribution. The full text is in [LICENSE](LICENSE). This choice does not relicense either upstream project, its dependencies, or its model data. If upstream code is incorporated later, its license obligations must be reviewed separately.

The checked-out revisions above were inspected while preparing this guide on 2026-09-29. They are not independently confirmed as the exact versions used in the earlier hardware repair. References to an omitted finalization step are scoped to these revisions.

Epson and EcoTank are names belonging to their respective owners and are used only to identify the relevant products. No affiliation or endorsement is implied.
