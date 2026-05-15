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

## Installed Software

| Package | Version | Install path |
|---------|---------|--------------|
| robot_hat | 2.5.2a1 | `~/robot-hat/` |
| vilib | 0.3.18 | `~/vilib/` |
| picar-x (Peter's fork) | 2.1.0a1 | `~/picar-x/` |
| Node.js | 24.15.0 | via NodeSource |
| npm | 11.12.1 | via NodeSource |
| pi (pi.dev agent) | 0.74.0 | `~/.local/bin/pi` |

## pi.dev Agent — Configuration

Pi is a terminal AI agent (pi.dev) that runs on the Pi and connects to AI providers.

**Auth file:** `~/.pi/agent/auth.json` (chmod 600)
```json
{
  "anthropic": { "type": "api_key", "key": "sk-ant-..." }
}
```

**API keys also set in** `~/.bashrc`:
```bash
export ANTHROPIC_API_KEY=sk-ant-...
export PATH=$HOME/.local/bin:$PATH
```

**Usage:**
```bash
ssh picar
pi                                              # interactive, default model
pi --model anthropic/claude-sonnet-4-6         # Claude Sonnet (most capable)
pi --model anthropic/claude-haiku-4-5-20251001 # Claude Haiku (fast/cheap)
pi --model openai/gpt-4o-mini                  # OpenAI (once key fixed)
pi -p "your prompt"                            # one-shot print mode
```

**Status (2026-05-15):**
- Anthropic/Claude: ✓ working
- OpenAI: removed (key revoked, not in use)

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

# 4. Node.js
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
npm config set prefix '~/.local'
echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bashrc

# 5. pi.dev agent
npm install -g @earendil-works/pi-coding-agent
```

## GitHub Repository

- **Peter's fork:** https://github.com/pepe8040/picar-x
- **Active branch:** `v2.1.x` (upstream 2.1.x bug fixes + Peter's GPT/voice additions)
- **Upstream:** https://github.com/sunfounder/picar-x

## Known Issues & Solutions

| Issue | Solution |
|-------|----------|
| WiFi country code not set → radio disabled | `sudo raspi-config nonint do_wifi_country DE` |
| SSID case mismatch in NetworkManager | `sudo nmcli con modify '...' 802-11-wireless.ssid 'FRITZ!Box 7582 WH'` |
| SSH install commands timing out | Use `nohup bash -c '... > /tmp/install.log 2>&1' &disown` |
| sudo requires password interactively | `/etc/sudoers.d/peter` has `peter ALL=(ALL) NOPASSWD:ALL` |
| npm global install permission error | Set `npm config set prefix '~/.local'` (no sudo needed) |
| pi.dev auth.json wrong format | Must be `{"openai":{"type":"api_key","key":"sk-..."}}` not plain string |

## Next Session Plan

### Priority 1 — Verify connectivity
- [ ] Run a live test of Claude via pi on the Pi (`pi -p "hello"`)
- [ ] OpenAI not configured — add key if needed in future

### Priority 2 — First hardware test
- [ ] Run `example/1.move.py` on the Pi to verify PiCar-X hardware responds
- [ ] Test camera: `example/7.computer_vision.py`
- [ ] Test voice/TTS: `example/13.sound_background_music.py`

### Priority 3 — Agentic layer design
- [ ] Design how pi.dev connects to PiCar-X commands
  - pi receives voice/text instruction
  - Python bridge translates to picarx API calls (move, turn, camera, TTS)
- [ ] Prototype: pi -p "drive forward 2 seconds" → picarx.forward(2)
- [ ] Explore new v2.1.x examples: `16.voice_controlled_car.py`, `18.online_llm_test.py`

### Priority 4 — VS Code Remote SSH
- [ ] Set up VS Code Remote SSH so Pi files can be edited from Mac directly in VS Code
