# Dreambo Servo Configuration Wizard

Console UI to detect, inspect and configure Dynamixel and Feetech servos
over a serial bus, built on top of [`rustypot`](https://crates.io/crates/rustypot)
and [Ratatui](https://ratatui.rs/).

## Hardware

### FEETECH

- [SM40BL](https://www.feetechrc.com/12v-40kg-rs485-serial-bus-steering-gear.html) (485) or [STS3025BL](https://www.feetechrc.com/12v-30kg-metal-tooth-core-motor-magnetic-coding-double-shaft-ttl-series-steering-gear.html) (TTL) servos
- [FE-URT-2](https://www.feetechrc.com/530962.html) USB to TTL, 485 bus programmer
- 12V power supply

## Install

### Linux / macOS

```sh
curl -LsSf https://github.com/createra-robotics/dreambo_servo_wizard/releases/latest/download/dreambo_servo_wizard-installer.sh | sh
```

### Windows (PowerShell)

```powershell
irm https://github.com/createra-robotics/dreambo_servo_wizard/releases/latest/download/dreambo_servo_wizard-installer.ps1 | iex
```

The installer drops the binary in `~/.cargo/bin` (or `%USERPROFILE%\.cargo\bin`)
and adds it to your `PATH` if needed. No Rust toolchain required.

### Pinning a version

Replace `latest` with a tag, e.g. `download/v0.1.0/`:

```sh
curl -LsSf https://github.com/createra-robotics/dreambo_servo_wizard/releases/download/v0.1.0/dreambo_servo_wizard-installer.sh | sh
```

### From source

```sh
cargo install --git https://github.com/createra-robotics/dreambo_servo_wizard
```

Or download a tarball/zip directly from the
[Releases page](https://github.com/createra-robotics/dreambo_servo_wizard/releases)
(builds: linux x86_64/aarch64, macOS x86_64/arm64, windows x86_64).

### Linux serial-port permissions

To talk to a USB-serial adapter without `sudo`, add yourself to the `dialout`
group (Debian/Ubuntu) or `uucp` (Arch), then log out/in:

```sh
sudo usermod -aG dialout "$USER"
```

## Usage

```sh
dreambo_servo_wizard
```

Pick the brand (Dynamixel / Feetech), serial port, baud rate and protocol,
then `Enter` to scan. Use the keyboard or the mouse: click motors and
registers, drag the goal-position slider, click the torque pill to toggle.
Esc returns to the connection screen, `q` quits.

## Local Development

1. Make sure Rust is installed

```shell
rustc --version || curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

2. Install the serial port build dependency (Linux needs libudev headers since serialport is used without default features but still requires udev on Linux):

```bash
sudo apt install pkg-config libudev-dev
```

3. Run it from the project root

```bash
cargo run --release
```

4. For development (faster compile, slower binary), drop --release:

```bash
cargo run
```

## Release

```bash
git push -u origin main
git tag v?.?.?
git push origin v?.?.?
```

## License

Apache-2.0