# Void Player (Beta)

> **Status: Beta Release**
> Void Player is currently in its beta stage. While the core state-machine architecture, VLC playback engine, and headless Bluetooth integrations are stable, users may encounter edge cases depending on their specific hardware, rogue Bluetooth peripherals, or unsupported USB DACs. Bug reports and contributions are highly encouraged.

Void Player is a lightweight, high-performance headless audio player designed specifically for the Raspberry Pi. Built entirely in Python, it bypasses standard desktop environments to deliver a pure, physical-button-driven music experience with a dynamic OLED interface.

At its core, Void Player utilizes a decoupled, non-blocking state-machine architecture. It handles hardware interrupts, dynamic audio routing, multithreaded display rendering, and headless Bluetooth management without relying on standard `time.sleep()` UI loops, preventing thread starvation and ensuring a highly responsive tactile experience.

## Core Features

* **Decoupled State-Machine Architecture:** Uses a centralized event queue and an active button manager (`btn_mgr`) to safely bind and unbind GPIO interrupts across different menu states.
* **Headless Bluetooth Management:** Interacts directly with the Linux BlueZ stack via `bluetoothctl` subprocesses. Handles device scanning, MAC address filtering, pairing, trusting, and connecting entirely through the 128x64 OLED UI.
* **Dynamic Audio Routing:** Hot-swaps active audio streams between Bluetooth sinks, external USB DACs, HDMI, and built-in audio jacks. Built on PulseAudio/PipeWire and managed via `pactl`.
* **VLC-Powered Playback Engine:** Supports FLAC, WAV, and MP3 formats with dynamic ID3 tag extraction via `tinytag`. Includes an isolated background thread for scrolling long track titles and rendering playback states smoothly.
* **Hardware Debouncing:** Custom wrapper for `gpiozero` implementing a 50ms re-entrancy lock and debounce window to prevent ghost inputs and overlapping handler execution.

## Hardware Requirements

* **SBC:** Raspberry Pi (Zero 2 W, 3, or 4 recommended for optimal VLC and PipeWire performance)
* **Display:** 128x64 I2C OLED Display (e.g., SSD1306)
* **Input:** 6x Tactile Push Buttons
* **Audio Output:** USB DAC (recommended for high-fidelity output) or a paired Bluetooth device.

## Hardware Setup & Wiring

Void Player relies on the Raspberry Pi's internal pull-up resistors. Each tactile button must be wired directly between its designated GPIO pin and any available Ground (GND) pin on the Pi. No external pull-up or pull-down resistors are required.

### GPIO Button Configuration

* **Menu / Back:** GPIO 24
* **Center / Select:** GPIO 18
* **Next Track / Down:** GPIO 22
* **Previous Track / Up:** GPIO 27
* **Volume Up:** GPIO 17
* **Volume Down:** GPIO 23

### OLED Display (I2C)

The SSD1306 display connects via the standard hardware I2C pins:

* **VCC:** 3.3V (Pin 1)
* **GND:** Ground (Pin 6 or Pin 9)
* **SDA:** GPIO 2 (Pin 3)
* **SCL:** GPIO 3 (Pin 5)

## Software Dependencies

Void Player requires the standard Linux audio and Bluetooth stacks to function correctly.

**System Packages:**
Ensure your Raspberry Pi has the following backend utilities installed:

```bash
sudo apt-get update
sudo apt-get install vlc pulseaudio-utils pulseaudio-module-bluetooth bluez

```

**Python Requirements:**
Install the necessary Python libraries using pip:

```bash
pip install -r requirements.txt

```

## Installation & Deployment

1. **Clone the repository:**

```bash
git clone https://github.com/YOUR_USERNAME/void-player.git
cd void-player

```

2. **Prepare the Music Directory:**
By default, the player scans for media in `/home/kash/Music`. Ensure your FLAC, WAV, or MP3 files are placed in this directory, or update `configs.MUSIC_DIR` to point to your desired path.
3. **Configure Fonts (Optional):**
The UI uses `DejaVuSans-Bold.ttf` for crisp rendering. If this font is not available on your system, the `configs.py` file will automatically fall back to the default Pillow font.
4. **Run the Application:**

```bash
python3 main.py

```

*Deployment Note: For a true headless appliance experience, it is highly recommended to configure `main.py` to run as a `systemd` background service on boot.*


## License

This project is open-source and available under the MIT License. See the LICENSE file for further details.
