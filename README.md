# gopro-format

Prepare an SD card for GoPro Hero13 on Ubuntu Linux.

## Why this exists

Since the fall of 2019, starting with the Hero8, I have dealt with persistent SD card errors across multiple GoPro cameras. The cards have always been top-of-the-line SanDisk Extreme Pro — and I have always formatted exclusively through the GoPro's built-in UI. After extensive research and trial and error, I believe the root cause is GoPro's in-camera "quick format," which does not write a fresh partition table or perform a full format. This script replaces that process entirely by writing a clean msdos partition table and performing a proper exFAT format from a Linux workstation.

## Overview

Automates the full wipe-and-format workflow: device detection, partition table creation, and exFAT formatting with the `GOPRO` volume label.

![SD Card](sdcard.jpeg)

## Usage

```bash
sudo ./gopro-format              # auto-detect SD card
sudo ./gopro-format /dev/sdb     # specify device
sudo ./gopro-format -b           # check for bad blocks first
sudo ./gopro-format -b /dev/sdb  # both
```

## What it does

1. **Detects** removable block devices (`/sys/block/*/removable`) or accepts a device argument
2. **Displays** device info (`lsblk`) and current partition table (`parted print free`) for confirmation
3. **Unmounts** any mounted partitions on the device
4. **Bad block check** (optional `-b` flag) — runs a destructive write test (`badblocks -wsv`) that writes and verifies test patterns on every sector. Warns if the card should be replaced.
5. **Creates** an msdos partition table with a single primary partition spanning the whole card
6. **Formats** the partition as exFAT with volume label `GOPRO`

## Requirements

- `parted`
- `exfatprogs` (or `exfat-utils`)
- `e2fsprogs` (provides `badblocks`, only needed with `-b`)

All are available via `apt`:

```bash
sudo apt install parted exfatprogs e2fsprogs
```

## Notes

- Requires root (`sudo`)
- Supports both `/dev/sdX` (USB readers) and `/dev/mmcblkX` (built-in readers)
- The `-b` bad block test is destructive (writes test patterns) but this is fine since the card is about to be formatted anyway
- If bad blocks are found, the card's internal wear-leveling has exhausted its spare sectors and the card should be replaced
