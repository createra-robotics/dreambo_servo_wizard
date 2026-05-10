# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build / Run

```bash
cargo run            # dev (fast compile)
cargo run --release  # production binary
cargo build          # type-check + compile (no tests in this crate)
```

Linux requires `pkg-config` and `libudev-dev` for the `serialport` crate.

There are no unit tests, no lint config beyond default `cargo clippy`, and no integration test harness — this is a single-binary TUI.

## Architecture

Four-module Rust binary using Ratatui + Crossterm for the TUI and `servocom` (a Dynamixel/Feetech protocol handler) over `serialport` for the bus.

- **`main.rs`** — terminal setup, event loop, key/mouse dispatch. Picks a poll interval based on whether a scan is running, the current `Mode`, and whether an edit is open.
- **`app.rs`** — all application state (`App`) and most behavior. Two modes: `Setup` (pick brand/port/baud/protocol/scan range) and `Main` (motor list + register table). Holds a `RefCell<Vec<HitZone>>` rebuilt each frame so mouse clicks can hit-test against rendered widgets.
- **`ui.rs`** — pure rendering. Each `draw_*` function calls `add_zone(app, rect, hit)` to register click targets back onto `App.hits` while drawing.
- **`comm.rs`** — thin `Bus` wrapper around `servocom::DynamixelProtocolHandler` exposing `ping` / `read` / `write`, plus a Linux-flavored `list_ports()` filter (ttyACM/ttyUSB/ttyAMA/ttyS).
- **`registers.rs`** — per-model register tables (`Reg { addr, name, ty, access }`) and the `MODELS` slice mapping `(Brand, model_number) → Model`. Encodes/decodes typed register values to/from raw little-endian bytes.

### Register tables

Each `Model` points at a `&'static [Reg]` slice. Adding a new servo means either reusing an existing table or defining a new const `*_REGS` and adding a `Model` entry to `MODELS`. The first read after discovery uses `model_number_addr` (0 for Dynamixel, 3 for Feetech) to identify which table applies; if the model number isn't in `MODELS`, `default_regs(brand)` is shown instead.

`MotorControl::from_regs` resolves "capability" registers (Torque Enable, Goal Position, Present Position, etc.) by name with a small alias list — that's how UI features like the goal slider and torque toggle work across brands without hard-coding addresses.

### EEPROM-safe writes (important)

`App::write_register_eeprom_safe` is the only path register edits go through. It detects EEPROM-region writes (addresses below the model's "Torque Enable" register) and:

- **Feetech**: clears `Lock` (addr 55) → write → restore Lock.
- **Dynamixel**: if torque is on, disables it → write → restore torque.

If the model's register table has no `Torque Enable`, the write goes through unchanged. Don't bypass this helper for register writes — silent EEPROM failures are the most common bug class here.

### Scan loop

`tick_scan` pings IDs 0..=`scan_max` one per call; `main.rs` pumps it 4× per frame so scans are brisk while the UI still updates per discovery. `tick_live` polls a small set of registers (`MotorControl::live_regs`) at ~5 Hz when in `Main` mode and not editing.

---

## Release flow

Releases are tag-driven via `cargo-dist` (`.github/workflows/release.yml`, configured in `dist-workspace.toml`). To cut a release:

1. Bump `version` in `Cargo.toml` to match the tag.
2. Commit (`release vX.Y.Z`) and `git push`.
3. `git tag vX.Y.Z && git push origin vX.Y.Z` — pushing the tag is what triggers the workflow.

The workflow builds for 5 targets (linux x86_64/aarch64, macOS x86_64/arm64, windows x86_64) and publishes a GitHub Release with shell + PowerShell installers. See `RELEASE.md` for the full procedure.