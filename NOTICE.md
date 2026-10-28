# Third-party notices

## Dolphin Emulator

Waker embeds the [Dolphin Emulator](https://github.com/dolphin-emu/dolphin) core libraries, which are licensed
GPL-2.0-or-later. Copyright (C) 2003–2026 Dolphin Emulator Project.

Release builds link Dolphin's static libraries and include its `Sys` data folder. Waker's only change to Dolphin
is the shared-`System` fix in [`patches/dolphin-shared-system.patch`](patches/dolphin-shared-system.patch);
the corresponding Dolphin source is available from the project linked above.

## Bundled libraries

Dolphin's core depends on FFmpeg, fmt, lz4, lzo, zstd, mbedTLS and a number of other libraries. Release builds
copy the Homebrew-provided dylibs into `Waker.app/Contents/Frameworks` so the app runs on Macs without Homebrew
installed. Each of those libraries keeps its own licence (LGPL, GPL, MIT, BSD or similar); see the respective
upstream projects for their terms.

## Nintendo

Waker is not affiliated with, endorsed by, or sponsored by Nintendo. *The Legend of Zelda* and
*The Wind Waker* are trademarks of Nintendo. No game data is included or distributed with this project —
you must supply your own disc image.
