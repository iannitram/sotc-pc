# sotc-pc code map

- **Repo:** iannitram/sotc-pc (private mirror). **Kind:** Windows wrapper around a game-specific shadPS4 build: batch, PowerShell, Bash and Python scripts, data files and documents. The C++ runtime itself is a submodule, not in this tree.
- **Upstream:** SotC-project/sotc-pc (`upstream/main`; the runtime lives on its `runtime` branch). **Integration branch:** `main`.
- **Describes commit:** `338246c` (2026-10-27).

Starts one game, *Shadow of the Colossus* (PS4, CUSA08809), from a single program on Windows, with keyboard, mouse and controller support, PC graphics settings, uncapped frame rate, a free play mode and an achievements panel. The game's own PS4 program runs unchanged on the runtime (a custom shadPS4 build, GPL-2.0-or-later, kept on the upstream `runtime` branch and checked out into `runtime/`). This repo holds what surrounds it: build and install scripts, settings, key pictures, test and measurement scripts, documents. No game files are in the repo.

## Layout

| Path | What it owns |
|---|---|
| `runtime` | Git submodule (gitlink `a50e820`) pointing to SotC-project/sotc-pc, branch `runtime`: the shadPS4-derived emulator source. Not expanded here |
| `build.bat`, `build-lto.bat`, `build-release.bat`, `build-pgo.bat` | Configure and build the runtime with CMake and the LLVM/Clang toolchain they point to (Visual Studio 2022) |
| `dist/` | What gets installed: `Install.ps1` and `Install.bat`, `Settings.ps1` (writes graphics and display settings), `FreePlay.ps1`, `Play.bat`, `PlayColossus.bat`, `ini_update.py`, `glyphs.py` and `glyphs/` (keyboard and Xbox button pictures drawn by the script), `user/` (default `config.json` and input bindings), `cursor_preview.png` |
| `lang/` | Translation files for the in-game text added by this build (`keys.txt` and one `.txt` per language: ar, da, de, es, fi, fr, he, it, nl, no, pl, pt, ru, and more) |
| `upstream/` | Two small general patches (`address-space-split-protections.patch`, `wave64-small-workgroup.patch`) kept apart from the game-specific work |
| `docs/` | `TUTORIAL.md` (build, install, first start, controls) and `img/` (screenshots and benchmark stills) |
| `showcase/` | `BENCHMARK.md`, `PERFORMANCE.md`, `NOTES.txt`: measurements and how they were made |
| Root scripts (about 50) | Test and measurement tooling on a separate test profile: `test.sh`, `bench*.sh`, `bossarena.sh`, `bosstest.sh`, `soakloop.sh`, `loadloop.sh`, `padtest.sh`, `audiotest.sh`, `*_video.sh`, `memwatch.ps1`, `keytest.ps1`, `screencap.ps1`, `bench_report.py`, `video_marks.py`, `seek.py` |
| `cursor_art.py`, `glyphs.py`, `ini_update.py`, `publish_filter.py` | Generators and tools: menu pointer art written into the runtime source, button pictures, bindings-file updater, the filter applied before the runtime is published |
| `.github/ISSUE_TEMPLATE/` | Issue forms (hardware, problem, question) |
| `README.md`, `CONTRIBUTING.md`, `LICENSE` | Upstream's readme, contribution guide, license |
| `.gitignore`, `ROADMAP.md`, `AGENTS.fork.md`, `CHANGELOG.fork.md`, `PIPELINE.md` | Ours: ignore rules, placeholder roadmap, rules for this mirror, change history, stage record |

## Where our changes live

Upstream owns everything. Against `upstream/main` the only differences are five added files (`.gitignore`, `ROADMAP.md`, `AGENTS.fork.md`, `CHANGELOG.fork.md`, `PIPELINE.md`), and `git log upstream/main..HEAD` is three commits, 66 added lines. The runtime source is not in this repo, so none of it is changed here. The submodule URL points at upstream, not at this fork.

## Build and test

```bat
git submodule update --init runtime
git -C runtime submodule update --init --recursive
build-lto.bat
cd dist
pwsh -File Install.ps1 -GameDir "D:\Games\CUSA08809"
```

- Needs Windows 10 or 11, a Vulkan 1.3 GPU, Visual Studio 2022 with CMake, Git, PowerShell 7 and the LLVM/Clang toolchain the `build*.bat` files expect (they hard-code local tool paths, so adjust them first).
- Tests are the root scripts, run in Git Bash on Windows (`./test.sh <seconds> [VAR=value ...]` runs the game headless on a test profile). They assume local paths and a built runtime; there is no CI workflow.

## Mac / PC / Linux

- Windows only (PIPELINE.md: platforms windows). Tested upstream on one PC (Ryzen 7 5700X3D, Radeon RX 9060 XT); Intel and NVIDIA untested.
- Not usable on macOS or Linux: the scripts are `.bat`, `.ps1` and Windows-path Bash. A Mac session can read and edit files but cannot build or run anything here (R04).
