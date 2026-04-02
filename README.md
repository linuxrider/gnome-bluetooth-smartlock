# Bluetooth Smart Lock ![Smart Lock black icon](https://raw.githubusercontent.com/ba0f3/gnome-bluetooth-smartlock/main/icons/smartlock-black.svg)  ![Smart Lock white icon](https://raw.githubusercontent.com/ba0f3/gnome-bluetooth-smartlock/main/icons/smartlock-white.svg)

Dynamic lock for GNOME. Use your smart phone/watch/band to lock your desktop automatically when you step away.

When your device is out of bluetooth range or get disconnected, it will lock your desktop automatically.

** This extension use **smart lock** icon by Andi from [Noun Project](https://thenounproject.com/browse/icons/term/smart-lock)

## Usage

Click on *Smart Lock* icon to activate its menu, and select one of your paired devices as your smart lock

![Smart lock menu](https://raw.githubusercontent.com/ba0f3/gnome-bluetooth-smartlock/main/screenshots/screenshot1.png)


** This extension will try to connects to your device frequently (for some reason (idk yet), `bluez` wont reconnect to smart phone after disconnected)

### Settings

![Settings](https://raw.githubusercontent.com/ba0f3/gnome-bluetooth-smartlock/main/screenshots/screenshot2.png)

The extension comes with default settings, but you can tweak them as your need.

![Advanced settings](https://raw.githubusercontent.com/ba0f3/gnome-bluetooth-smartlock/main/screenshots/screenshot3.png)
## Installation

### Requirements

 * bluez (on debian based distro: `sudo apt install bluez`)

### Installation from extensions.gnome.org

https://extensions.gnome.org/extension/5359/bluetooth-smart-lock/

### Install from source

Requires:
* git
* make

```sh
git clone https://github.com/ba0f3/gnome-bluetooth-smartlock.git
cd gnome-bluetooth-smartlock
make install
```

### bt-rssi service (optional)

The **bt-rssi** service enables RSSI-based proximity locking. It reads signal strength from connected Bluetooth devices via the HCI management API in a more reliable way than scanning.

Without this service, the extension only locks/unlocks based on device connected/disconnected state. With it, you can lock the screen when signal strength drops below a configurable threshold. To adjust the threshold to your needs a live reading is displayed in the advanced settings dialog.

#### Build

Requires Rust toolchain:

```sh
cd services/bt-rssi
cargo build --release
```

#### Install

```sh
# Install the binary
sudo cp target/release/bt-rssi /usr/local/bin/

# Install the D-Bus policy (allows the service to own its bus name)
sudo cp ../org.gnome.BluetoothRSSI.conf /etc/dbus-1/system.d/

# Install and start the systemd service
sudo cp ../bt-rssi.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now bt-rssi
```

[Pixi](https://pixi.prefix.dev/latest/):
A pixi which manages the abovce installation process including rust toolchain setup is also provided.

```sh
cd services/bt-rssi
pixi run install
```

#### Verify

```sh
# Check the service is running
systemctl status bt-rssi

# Start RSSI monitoring: address, interval (seconds), hci adapter index
busctl --system call org.gnome.BluetoothRSSI /org/gnome/BluetoothRSSI \
  org.gnome.BluetoothRSSI StartMonitoring suq "<MAC>" 3 0
```

Once installed, the extension's preferences will enable the **Proximity lock (RSSI)** toggle and **RSSI threshold** setting.
