# Waker 1.0.0

The first public build: a native macOS front end that runs **The Legend of Zelda: The Wind Waker** (GameCube, USA) on an embedded Dolphin core. No emulator UI, no setup wizard — pick your disc, press **Play**.

## Install

1. Download **Waker.dmg** from the assets below.
2. Open it and drag **Waker** into **Applications**.
3. First launch: right-click the app → **Open** → **Open**. The build is signed ad-hoc, not notarised, so Gatekeeper asks once.

Requires macOS 13 Ventura or newer on Apple Silicon. You supply your own `GZLE01` disc image as a plain `.iso` or `.gcm`.

## What's in it

- Boots straight from a validated GZLE01 image into a native game window, with the disc name and game ID checked before launch.
- ARM64 JIT with separate CPU and GPU threads — full speed, not the interpreter.
- Save states: `F1`–`F8` save, `Shift`+`F1`–`F8` load, plus a **Load Save State** menu that boots directly into a slot.
- Keyboard controller with a remapping screen under **Controls**.
- Settings for fullscreen, window size, widescreen, internal resolution and hi-res texture packs.
- Header mark and app icon with transparent backgrounds that sit cleanly on the app's palette.

## Fixed in this build

- **Boot crash**: the app bridge and the Dolphin libraries were each creating their own `Core::System`, so the game started against an empty machine and died at the entry point (`Unknown instruction 00000000 at PC = 80003140`). There is now a single shared system instance; the game boots and streams from the disc.
- **System beep on every key press**: the game view now accepts key events and swallows them, so macOS no longer plays the alert sound while you play. (The core reads the keyboard itself, so input is unaffected.)
- The emulator config no longer forces single-core mode; it runs dual-core with the JIT, which is the configuration that actually boots.
- Removed the stale `Fastmem` and `DisableICache` overrides left over from earlier debugging.

## Known limitations

- Apple Silicon only — this build will not run on Intel Macs.
- USA `GZLE01` only. Other regions and games are rejected by the disc check.
- Compressed images (`.rvz`, `.gcz`, `.wbfs`, `.cia`) must be converted to `.iso` first.
- Ad-hoc signature, so the first launch needs right-click → Open.
- Keyboard only for now; gamepads are not mapped yet.

## Credits

Built on the [Dolphin Emulator](https://github.com/dolphin-emu/dolphin) core (GPL-2.0-or-later). Waker is GPL-2.0-or-later and ships with no game data; see `LICENSE` and `NOTICE.md` in the repository.
