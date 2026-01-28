# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Single bash script (`gopro-format`) that prepares SD cards for a GoPro Hero13 on Ubuntu Linux. It auto-detects removable devices, shows device info for confirmation, optionally checks for bad blocks and fake flash, creates an msdos partition table, and formats exFAT with a unique date-sequenced label. Every format is logged for tracking.

## Key details

- **Script**: `gopro-format` — the only source file; everything is in one bash script
- **Target filesystem**: exFAT with a unique volume label (`GP` + `YYMMDD` + 2-digit sequence, e.g. `GP26012701`)
- **Partition table**: msdos (MBR), single primary partition, 1MiB-aligned
- **Device detection**: scans `/sys/block/` for removable devices; supports both `sdX` (USB) and `mmcblkX` (built-in) naming
- **Bad block check** (`-b`): destructive write test via `badblocks -wsv`, runs before partitioning
- **Fake flash check** (`-f`): counterfeit/capacity fraud detection via `f3probe --destructive`, runs before partitioning
- **Check/lookup** (`-c [LABEL]`): look up a card's format history by label, or auto-detect from inserted card
- **List history** (`-l`): display full format log
- **Format log**: `gopro-format.log` in the script's directory; pipe-delimited lines with timestamp, label, device, size, model

## Running

```bash
sudo ./gopro-format [-b] [-f] [-c [LABEL]] [-l] [/dev/sdX]
```

No build step. No tests. Script requires root (except `-c LABEL` and `-l`) and depends on `parted`, `exfatprogs`, and optionally `e2fsprogs` (for `badblocks`) and `f3` (for `f3probe`).
