# Flashing the DGUS Display Assets

These instructions are for flashing the display resources in this repository to a DWIN DGUS touchscreen that uses a `DWIN_SET` folder. This updates the screen graphics, touch layout, variable configuration, and icon resources. It does not flash Marlin firmware to the printer control board.

## Before You Start

- Use this only with a compatible DWIN DGUS display. Do not use these files on a display that expects a different update folder or a different resolution.
- Keep the printer/display powered off while inserting or removing the microSD card.
- Use stable power during the update. Do not turn the printer off while the screen is writing files.
- Copy the `DWIN_SET` folder exactly. Do not rename it.

## What Gets Flashed

The `DWIN_SET` folder in this repo contains the display package:

```text
DWIN_SET/
  0.BootScreen.bmp
  10.Main.bmp
  13Touch_Control_Config.bin
  14Variable_Config.bin
  22_Config.bin
  30_ProgressBar.ICO
  ...other 480 x 272 BMP screens...
```

The BMP files are the screen images. The `13*.bin` file is the touch control configuration. The `14*.bin` file is the variable display configuration. The `22*.bin` file initializes display variable data. The `.ICO` file contains icon resources.

## Prepare the MicroSD Card

1. Use a small microSD card if possible. Many DGUS screens are picky, so an 8 GB or smaller card is a good first choice.
2. Format the card as FAT32.
3. Use an MBR partition table if your formatting tool asks.
4. Use a 4096 byte allocation unit size.
5. Copy the repo's `DWIN_SET` folder to the root of the card.

The final card layout should look like this:

```text
SD_CARD/
  DWIN_SET/
    0.BootScreen.bmp
    10.Main.bmp
    13Touch_Control_Config.bin
    14Variable_Config.bin
    22_Config.bin
    ...
```

Avoid this nested layout:

```text
SD_CARD/
  DWIN_SET/
    DWIN_SET/
      10.Main.bmp
```

## Flash the Display

1. Turn the printer/display off.
2. Insert the prepared microSD card into the SD slot on the display itself, not the printer control board SD slot.
3. Turn the printer/display on.
4. Wait for the DGUS update screen. Many DWIN panels show a blue screen while processing the `DWIN_SET` folder.
5. Wait until the update finishes. On many panels, the screen will show an `END` message or return to the first configured page.
6. Leave the card inserted for at least 6 seconds before removing power or removing the card.
7. Turn the printer/display off.
8. Remove the microSD card from the display.
9. Turn the printer/display back on and confirm the UI loads normally.

Remove the card after flashing. If the card stays in the display, some panels will try to process the `DWIN_SET` folder again on the next boot.

## Windows Formatting Example

Replace `X:` with the actual microSD card drive letter. Double-check this before running the command.

```bat
format X: /FS:FAT32 /A:4096 /Q
```

After formatting, copy `DWIN_SET` to the root of the card.

## Linux Formatting Example

Replace `/dev/sdX1` with the actual microSD card partition. Double-check this before running the command.

```sh
sudo mkfs.vfat -F 32 -s 8 -n DWIN /dev/sdX1
```

The `-s 8` option gives 8 sectors per cluster, which is normally 4096 bytes on cards with 512 byte sectors.

## Troubleshooting

If the screen does not start flashing:

- Confirm the folder is named exactly `DWIN_SET`.
- Confirm `DWIN_SET` is at the root of the card.
- Reformat as FAT32 with 4096 byte allocation units.
- Try a smaller or different microSD card.
- Make sure the card is in the display's SD slot, not the mainboard SD slot.
- Power the display off, wait a few seconds, then power it back on with the card inserted.

If flashing starts but does not finish:

- Wait a few minutes before deciding it is stuck.
- Power off only after you are sure it is not progressing.
- Reformat the card and copy a fresh `DWIN_SET` folder.
- Try another microSD card.

If the graphics load but controls do not behave correctly:

- Make sure the printer firmware expects this exact DGUS UI/resource package.
- Reflash the complete `DWIN_SET` folder, not just selected BMP files.
- Confirm the display resolution is 480 x 272.

If the screen looks wrong or distorted:

- Stop using this package on that display until the panel type and resolution are confirmed.
- Flash the matching resource set for your display and Marlin build.

## Notes

DWIN's DGUS documentation describes SD/SDHC updates using a FAT32 card with a root-level `DWIN_SET` folder. The display detects the folder during startup, downloads the matching files, and uses numeric filename prefixes to decide where resources are stored in display flash.

Reference: [DWIN DGUS Development Guide V4.3](https://forums.dwin-global.com/wp-content/uploads/2024/07/DWIN-DGUS-DEV-GUIDE_V43_2015.pdf)
