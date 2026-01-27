# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Single bash script (`gopro-format`) that prepares SD cards for a GoPro Hero13 on Ubuntu Linux. It auto-detects removable devices, shows device info for confirmation, creates an msdos partition table, and formats exFAT with the `GOPRO` label.

## Key details

- **Script**: `gopro-format` — the only source file; everything is in one bash script
- **Target filesystem**: exFAT with volume label `GOPRO` (required by GoPro cameras)
- **Partition table**: msdos (MBR), single primary partition, 1MiB-aligned
- **Device detection**: scans `/sys/block/` for removable devices; supports both `sdX` (USB) and `mmcblkX` (built-in) naming
- **Bad block check** (`-b`): destructive write test via `badblocks -wsv`, runs before partitioning

## Running

```bash
sudo ./gopro-format [-b] [/dev/sdX]
```

No build step. No tests. Script requires root and depends on `parted`, `exfatprogs`, and optionally `e2fsprogs` (for `badblocks`).
