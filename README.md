# Waker

A small, native macOS front end for **The Legend of Zelda: The Wind Waker** (GameCube, USA), built on an embedded [Dolphin](https://github.com/dolphin-emu/dolphin) core. Pick your disc image, press **Play**, and the game opens in its own window — no emulator UI, no configuration files to edit.

Waker links Dolphin's core libraries directly, so it boots the game itself rather than driving a Dolphin install. Settings, key bindings, save states and the emulator's user folder all live in Waker's own Application Support directory.

> Waker contains no game data. You need your own copy of the USA disc image, and it must be `GZLE01`.

## Requirements

- macOS 13 Ventura or newer
- Apple Silicon (the bundled core is arm64)
- The Wind Waker (USA) as a plain `.iso` or `.gcm` — compressed formats such as `.rvz`, `.gcz` or `.wbfs` are rejected; convert them first

## Install

1. Download **Waker.dmg** from [Releases](../../releases/latest).
2. Open the DMG and drag **Waker** into **Applications**.
3. First launch: the build is signed ad-hoc, not notarised, so macOS asks for confirmation. Right-click (or Control-click) the app and choose **Open**, then **Open** again. If the app is still refused, clear the quarantine flag once:
   ```sh
   xattr -dr com.apple.quarantine /Applications/Waker.app
   ```

## First run

1. Launch Waker.
2. Click **Choose Disc Image…** and pick your `.iso`.
3. The window shows `USA disc ready · GZLE01` when the image checks out (the name and game ID are read from the disc header).
4. Press **Play**.

The game runs in a separate window. Closing that window stops the game; the main window stays open so you can start again or load a save state.

## Controls

| GameCube | Key | | GameCube | Key |
| --- | --- | --- | --- | --- |
| A | `Space` | | D-Pad | `I` `K` `J` `L` |
| B | `X` | | Main stick | `W` `S` `A` `D` |
| X | `C` | | C stick | `T` `G` `F` `H` |
| Y | `V` | | L trigger | `Q` |
| Z | `Z` | | R trigger | `E` |
| Start | `Return` | | | |

Every binding can be changed under **Controls** — click a button, then press the key you want.

**Save states:** `F1`–`F8` save to slots 1–8; `Shift`+`F1`–`F8` load them. States also appear in the **Load Save State** menu, and opening one boots the game straight into it.

## Settings

**Settings** covers fullscreen, window size, widescreen, internal resolution (1×–4×), and hi-res texture packs. Dropped-in texture packs go in `~/Library/Application Support/Waker/DolphinUser/Load/Textures/GZLE01`.

## Where things live

```
~/Library/Application Support/Waker/
├── settings.json          # disc path, window, bindings
└── DolphinUser/           # emulator user folder
    ├── Config/            # generated before each launch
    ├── StateSaves/        # F1–F8 slots
    └── Logs/dolphin.log   # core log — first place to look if a boot fails
```

## Building from source

Waker is a Swift package that links **prebuilt Dolphin static libraries**, so you need a Dolphin checkout with its core libraries built.

1. Clone Dolphin and apply the patch in [`patches/`](patches/dolphin-shared-system.patch):
   ```sh
   git clone https://github.com/dolphin-emu/dolphin
   cd dolphin
   git apply /path/to/waker/patches/dolphin-shared-system.patch
   ```
   The patch gives `Core::System::GetInstance()` a single out-of-line definition. Without it, Waker's bridge and the Dolphin libraries each get their own `System`, the game boots against an empty machine, and you get a black window followed by `Unknown instruction 00000000 at PC = 80003140`.

2. Configure and build Dolphin's static libraries with CMake into `build-core/` (release). `Package.swift` lists the archives it links, all under `build-core/Source/Core/...` and `build-core/Externals/...`.

3. Build and package the app:
   ```sh
   export DOLPHIN_SRC=/path/to/dolphin              # checkout containing build-core/
   export DOLPHIN_SYS=/path/to/Dolphin.app/Contents/Resources/Sys
   ./scripts/build-app.sh                            # -> dist/Waker.app, dist/Waker.dmg
   ```
   `DOLPHIN_SYS` is the `Sys` data folder (font, IPL tables) the core expects at runtime; it is copied into the bundle. `scripts/build-app.sh` also copies the Homebrew dylibs the core needs into `Contents/Frameworks`, so the result runs without Homebrew.

## Troubleshooting

- **Black window, then the app closes** — check `~/Library/Application Support/Waker/DolphinUser/Logs/dolphin.log`. If it ends in `Unknown instruction 00000000`, the core was built without the patch above.
- **"The file could not be read"** — the disc image is inside iCloud Drive and hasn't been downloaded locally yet, or it is not a GameCube image.
- **Old icon in the Dock** — macOS caches icons; `killall Dock`, or remove and re-add the app.
- **Key presses make your Mac beep** — that was fixed in 1.0.0; update to the current build.

## Credits and licence

Waker is licensed **GPL-2.0-or-later** — see [LICENSE](LICENSE). It embeds the [Dolphin Emulator](https://github.com/dolphin-emu/dolphin) core, also GPL-2.0-or-later; see [NOTICE.md](NOTICE.md) for third-party details.

Waker is not affiliated with, endorsed by, or sponsored by Nintendo. *The Legend of Zelda* and *The Wind Waker* are trademarks of Nintendo. No game data is included.
