# Raspberry Pi Setup — PiCar-X

## Hardware

- **Board:** Raspberry Pi 4 (4 GB RAM)
- **Kit:** SunFounder PiCar-X V2 (robot_hat, NOT fusion-hat)
- **SD Card:** 32 GB

## Connection

```bash
ssh picar          # passwordless, from Mac
# → peter@picar.local (192.168.188.54 via WiFi)
```

Mac SSH config (`~/.ssh/config`):
```
Host picar
    HostName picar.local
    User peter
    ServerAliveInterval 30
```

## Current OS (as of 2026-05-15)

| Item | Value |
|------|-------|
| OS | Debian 13 Trixie (Raspberry Pi OS) |
| Kernel | 6.12.75, aarch64 (64-bit) |
| Python | 3.13.5 |
| Network | WiFi — FRITZ!Box 7582 WH |

## Installed Dependencies

| Package | Status | Install path |
|---------|--------|--------------|
| robot_hat v2.5.x | installed 2026-05-15 | `~/robot-hat/` |
| vilib | pending | `~/vilib/` |
| picar-x (Peter's fork, v2.1.x) | pending | `~/picar-x/` |
| Node.js | not yet | for pi.dev framework |

## Install Commands

Run on the Pi in this order if reinstalling from scratch:

```bash
# 1. robot_hat
git clone --depth 1 -b 2.5.x https://github.com/sunfounder/robot-hat.git ~/robot-hat
cd ~/robot-hat && sudo python3 install.py

# 2. vilib (camera)
git clone --depth 1 https://github.com/sunfounder/vilib.git ~/vilib
cd ~/vilib && sudo python3 install.py

# 3. picar-x (Peter's fork, v2.1.x)
git clone -b v2.1.x https://github.com/pepe8040/picar-x.git ~/picar-x
cd ~/picar-x && sudo pip3 install . --break-system-packages
```

## GitHub Repository

- **Peter's fork:** https://github.com/pepe8040/picar-x
- **Active branch:** `v2.1.x` (merged upstream 2.1.x bug fixes + Peter's GPT/voice additions)
- **Upstream:** https://github.com/sunfounder/picar-x

## Known Issues & Solutions

| Issue | Solution |
|-------|----------|
| WiFi country code not set → radio disabled | `sudo raspi-config nonint do_wifi_country DE` |
| SSID case mismatch in NetworkManager | `sudo nmcli con modify '...' 802-11-wireless.ssid 'FRITZ!Box 7582 WH'` |
| SSH install commands timing out | Use `nohup bash -c '... > /tmp/install.log 2>&1' &disown` |
| sudo requires password interactively | `/etc/sudoers.d/peter` has `peter ALL=(ALL) NOPASSWD:ALL` |

## Next Steps

- [ ] Install vilib
- [ ] Deploy picar-x v2.1.x fork
- [ ] Install Node.js → explore pi.dev as agentic layer
- [ ] Set up VS Code Remote SSH (code CLI on Pi)
