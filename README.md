# Milton

A fork of [serge-rgb/milton](https://github.com/serge-rgb/milton), the
infinite-canvas paint program, maintained to fix bugs and improve the
day-to-day experience.

> **What Milton is:** an open-source application that lets you *Just Paint*.
> There are no pixels — you can paint with (almost) infinite detail. It feels
> raster-based but works with vectors. It's not an image editor and it's not a
> vector graphics editor; it's a program for drawing, sketching and painting.
> There is no save button — your work is persistent with unlimited undo.

---

## Why this fork exists

The upstream project is largely unmaintained. This fork exists to:

- **Fix bugs** that affect real users (see [Fixed](#fixed) below).
- **Introduce quality-of-life improvements** over time.
- Keep the project buildable and usable on current toolchains.

This is *not* a rewrite — the goal is a stable, dependable drop-in Milton.

## Fixed

- **Data loss from failed or corrupted saves.** Milton autosaves with no user
  control, and an interrupted or denied write previously replaced your only
  copy of a drawing with an empty/corrupt file, producing
  `MLT file could not be loaded. Magic number mismatch.` on the next open. This
  release:
  - backs up the previous file to `<name>.mlt.bak` before every save,
  - aborts a save if the temporary write isn't a valid Milton file (so a bad
    write can never clobber the good one),
  - retries `MoveFileExW` on transient `ERROR_ACCESS_DENIED` / sharing / lock
    errors (real-time antivirus briefly holds the freshly-written temp file),
  - serializes saves so the background save thread and manual saves can't
    collide on the same temp file.

## Roadmap

Quality-of-life and bug-fixing work is planned and tracked here incrementally.

---

## User controls

- **Pan:** drag (or use the pen) while holding `Space`.
- **Brush / eraser:** `B` / `E`.
- **Brush size:** `[` / `]`.
- **Transparency:** number keys `1`–`0`.

## Compiling

### Windows

Requires Visual Studio (the upstream readme targets 2019; this fork is verified
to build on **VS 2026**). Open an **x64 Native Tools command prompt** in the
repo root and run:

```
build.bat
```

The binary is produced at `build\Milton.exe`.

### Linux / macOS

Not officially supported upstream, but the CMake path is:

```
cd third_party/SDL2-2.0.8
mkdir build && cd build
cmake -DVIDEO_WAYLAND=OFF -DCMAKE_INSTALL_PREFIX=linux64 -DCMAKE_BUILD_TYPE=Debug ..
make && make install
cd ../../..
mkdir build && cd build
cmake ..
make
```

---

## Versioning

Upstream's scheme is kept: `MAJOR` tracks UI-overhaul releases, `MINOR` tracks
binary-file-format compatibility (a version can read files saved by any *older*
minor version, not any newer one), and `PATCH` is for non-breaking releases.

## License

GNU General Public License v3.0 — see [LICENSE.txt](LICENSE.txt). This fork is
derived from [serge-rgb/milton](https://github.com/serge-rgb/milton), made
possible by Sergio Gonzalez and the [contributors](CREDITS.md).
