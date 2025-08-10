# BlockAssist

<img width="680" height="333" alt="image" src="https://github.com/user-attachments/assets/7f42b743-9bcd-4e75-9f09-c81af474bd10" />

 **BlockAssist** is an AI bot that learns from you in Minecraft. It starts dumb but gets smarter as you play.

Steps:
1. Follow setup instructions below
2. Play Minecraft episodes. Complete building progress bar.
3. Track your participation on the leaderboard.

---

## ➡️ Hardware Requirements
* 8 GB RAM minimum (16 GB recommended)
* Multi‑core CPU
* 100 GB disk space

---

## ➡️ Enviorement
### Local systems (Recommended, best game experience)
→ **Linux or Mac**: This guide is for **Linux** users, **Mac** users check [official guide](https://github.com/gensyn-ai/blockassist/tree/main) for Mac commands)

→ **WSL** (Windows Subsystem for Linux — Light):
- Recommended if you are on Windows and want to run everything inside Ubuntu on WSL.
- Choose ONE of the two display options below. If you are on Windows 11, prefer WSLg.
- If you run into issues, see [WSL: Common issues and fixes](#wsl-common-issues-and-fixes).

1) WSLg (Windows 11, easiest — no external X server):
- In Windows PowerShell (Admin):
```powershell
wsl --update
wsl --shutdown
```
- In WSL Ubuntu, ensure no manual DISPLAY overrides exist:
```bash
# Remove old DISPLAY-related lines
sed -i '/export DISPLAY=/d' ~/.bashrc
sed -i '/LIBGL_ALWAYS_INDIRECT/d' ~/.bashrc
sed -i '/WAYLAND_DISPLAY/d' ~/.bashrc
```
- Install test tools:
```bash
sudo apt update && sudo apt install -y x11-apps mesa-utils
```
- Verify GUI apps open
```
xeyes  # a small test window should appear
```
- Note: With WSLg you typically do NOT set DISPLAY manually. If you previously added it, remove it as shown above.
- If `xeyes` does not open on Windows 11 (WSLg may be unavailable/disabled), install and run an external X server like MobaXterm or VcXsrv (the guide to configure is in next step) and keep it open while running GUI tests and BlockAssist. Then retry `xeyes`.

2) External X server (Windows 10 or if you prefer):
- Option A: MobaXterm (full-featured client)
  - Settings > Configuration > X11
    - X11 server display = Multiwindow mode
    - OpenGL = Hardware
    - X11 remote access = Full
    - Clipboard = Enabled
    - Display offset = 0
  - Ensure the X server button (top-right) is colored (running)
- Option B: VcXsrv
  - Install from `https://sourceforge.net/projects/vcxsrv/`
  - Launch XLaunch with:
    - Multiple windows, Display number: 0
    - Start no client
    - Extra settings: check “Disable access control”

- In WSL Ubuntu, install test tools:
```bash
sudo apt update && sudo apt install -y x11-apps mesa-utils
```
- Clean up any old DISPLAY settings and set DISPLAY to the Windows host IP (required for X server):
```bash
# Remove old lines first so you do not end up with duplicates
sed -i '/export DISPLAY=/d' ~/.bashrc
sed -i '/LIBGL_ALWAYS_INDIRECT/d' ~/.bashrc

# Set DISPLAY to WSL2 default gateway (Windows host) and optionally indirect GL
echo 'export DISPLAY=$(ip route show default | awk '\''/default/ {print $3}'\''):0.0' >> ~/.bashrc
# Optional: helps on some systems with OpenGL issues; try removing if performance suffers
echo 'export LIBGL_ALWAYS_INDIRECT=1' >> ~/.bashrc

# Load the changes
. ~/.bashrc
```
- Test your X server from WSL:
```bash
xeyes
```
  - If the eye window appears, your X server and DISPLAY are working. Close it with Ctrl + C in the terminal.

echo 'export DISPLAY=$(ip route show default | awk '\''/default/ {print $3}'\''):0.0' >> ~/.bashrc


→ **Ubuntu on Windows via VirtualBox**(Heavy): Detailed guide to setup [Ubuntu on Windows via VirtualBox](./ubuntu-virtualbox.md)

### Cloud GPUs (Desktop-gui enabled, VNC Desktop)
This method is running Minecraft inside a **VNC Desktop** which provides a slow game, but it's a straight-forward installation.

→ **Vast Linux Desktop Template**: Rent a GPU with my custom [Moei Linux Desktop Container](https://cloud.vast.ai/?ref_id=228875&creator_id=228875&name=Moei%20Linux%20Desktop%20Container) template (nvh264enc video encoder), then go to *instances* page and wait for your gpu to be deployed.

To open your gpu desktop, you have two options:

1. Vast preserved desktops (Recommended):
* Click on **Open** (Blue button) to head to the gpu dashboard, and Access to the desktop via:
  * **Selkies Low Latency Desktop (Recommended, most performant)**. Lower the video bitrate if you have low-bandwidth > By clicking the small circle in the right side of the desktop.
  * Apache Guacamole Desktop (Low performance)

2. Local VNC client (Low performance, complex):
  * Click on **Open** (Blue button) to head to the gpu dashboard
  * Access to the terminal via **Jupyter Terminal**
  * Find your Dekstop password by executing: `echo $OPEN_BUTTON_TOKEN`
  * In instances page, click on gpu IP and find the mapped host:port to `5900/tcp`, for example it can be: `70.45.225.15:18483`
  * Install [RealVNC](https://www.realvnc.com/en/connect/download/viewer/)
  * Enter the mapped host:port and then it prompts for the password to access to your desktop.
  * To install a Chrome browser on it, visit [VNC (Linux desktop)](https://github.com/0xmoei/Install-VNC-Linux-Desktop/) Full Guide

# 

**Note: The installation is very experimental and it's a little complicated for non-local linux and mac users. The team also Introduced [Sunshine + Moonlight for Remote Linux](https://docs.gensyn.ai/testnet/blockassist/running-blockassist-on-a-remote-linux-desktop) method that the training runs on VPS and the game runs on your local PC, but it's also challanging. Try it out only if you know what you are doing.**

---

## ➡️ Installation
### Step 1: Install Dependecies
```bash
sudo apt update
sudo apt install -y \
  make build-essential gcc \
  libssl-dev zlib1g-dev libbz2-dev libreadline-dev \
  libsqlite3-dev libncursesw5-dev xz-utils tk-dev \
  libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev \
  curl git unzip \
  libxi6 libxrender1 libxtst6 libxrandr2 libglu1-mesa libopenal1
```

### Step 2: Clone the repo and enter the directory
```bash
git clone https://github.com/gensyn-ai/blockassist.git
cd blockassist
```

### Step 3: Install Node.js
```
# Verify version
# Skip the whole step if version 20
node --version
npm --version

# Remove existing Node.js
sudo apt remove -y nodejs npm

# Install NodeSource repository for Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

# Install Node.js 20
sudo apt install -y nodejs
```

### Step 4: Install Yarn
```bash
curl -o- -L https://yarnpkg.com/install.sh | bash
export PATH="$HOME/.yarn/bin:$HOME/.config/yarn/global/node_modules/.bin:$PATH"
```

### Step 5: Install Java
```bash
./setup.sh
exec $SHELL
```

### Step 6: Install `pyenv`
```bash
curl https://pyenv.run | bash
```
```bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
```

### Step 7: Install Python
```bash
pyenv install 3.10
pyenv global 3.10
```

### Step 8: Install project dependecies
```bash
pip install --upgrade pip

pip install -e . --no-cache-dir
pip install "mbag-gensyn[malmo]" --no-cache-dir

pip install psutil readchar
```

---

## ➡️ Run BlockAssist

### Start Mincraft
```bash
cd blockassist

export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"

python run.py
```

### Hugging Face Token
It prompts you to enter a [Hugging Face](https://huggingface.co/) API token. Create an Access Token with `Write` permissions [here](https://huggingface.co/settings/tokens) and save it

### Gensyn Testnet login
You will be prompted to login via a browser
* Open **Web Browser**, visit `http://localhost:3000`, and login to your Gensyn account.
* If you have previously logged in, this step will be skipped

<img width="1234" height="588" alt="Screenshot_824" src="https://github.com/user-attachments/assets/20d46c20-490d-4646-b56f-182102f99630" />


### Play Minecraft
* Wait until two Minecraft windows have opened
* Press `ENTER` in terminal.
* Go to the first Minecraft window, the game and the map should start itself, must not create one yourself.
* **Note for VNC users**: To enable in-game keys, Press `ENTER` inside the game window.
* **Note for VirtualBOX users**: To enable in-vm mouse movement, VM window > Input > Mouse Integration, then toggle off/on with `right-ctrl`
* Build the building to increase your progress. (More progress = Better trained AI).
  * Left-click pickaxe to remove, Left-click *Dirt* on red tiles, Right-click stone, glasses, plonks on place holders.
* Then return to your terminal and press `ENTER` to end the session.

<img width="1754" height="838" alt="Screenshot_825" src="https://github.com/user-attachments/assets/5acf6764-4040-4615-a5e7-5ea4e0b0fe6c" />

**Note: Make sure to check [Troubleshooting](#troubleshooting) if you got any error**

**Note #2: Even if you don't finish the eposide till the end and close it, you'll earn your participation in the leaderboard**

### Training
A model will be started training now and be submitted to Hugging Face and to Gensyn’s smart contract.

### Verify
If you reach this stage in the logging window and can see a transaction in the block explorer, your submission has succeeded.

A successful training logging:

<img width="861" height="421" alt="Untitl111ed" src="https://github.com/user-attachments/assets/3327ca8d-4e50-4d0d-b77e-e2a787c09e20" />


### Leaderboard
Login to [BlockAssist leaderboard](https://dashboard.gensyn.ai) to check your participation points


### Configuration
You can modify settings in the `src/blockassist/config.yaml` file or override them via command-line arguments.

- `episode_count` — Controls the number of episodes. If `episode_count` is greater than 1, a new episode will start each time you press `ENTER` during session recording.

- `num_training_iters` — Controls the number of training iterations across all recorded episodes.

---

## ➡️ Troubleshooting
### CUDNN Error
```
wget https://developer.download.nvidia.com/compute/cudnn/9.11.0/local_installers/cudnn-local-repo-ubuntu2204-9.11.0_1.0-1_amd64.deb
sudo dpkg -i cudnn-local-repo-ubuntu2204-9.11.0_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2204-9.11.0/cudnn-local-4EC753EA-keyring.gpg /usr/share/keyrings/
echo "deb [signed-by=/usr/share/keyrings/cudnn-local-4EC753EA-keyring.gpg] file:///var/cudnn-local-repo-ubuntu2204-9.11.0 /" | sudo tee /etc/apt/sources.list.d/cudnn-local.list
sudo apt update
sudo apt install -y libcudnn9 libcudnn9-dev
```

### Increase Malmo startup timeout
Needed if you need more timeout delay between the first minecraft window and the second one
```
cd blockassist
sed -i 's#python -m malmo.minecraft launch#python -m malmo.minecraft launch --timeout 300#' scripts/run_malmo.sh
```

### Check logs and debug
Open a *new* terminal to run these:

* Minecraft logic logs:
```bash
cd blockassist
tail -f logs/malmo.log
```

* Minecraft maps startup logs:
```bash
cd blockassist
tail -n 200 logs/run.log
```

* Login page logs:
```bash
cd blockassist
tail -n 200 logs/yarn.log
```

### WSL: Common issues and fixes
- "Error: cannot open display":
  - Ensure your X server is running on Windows (MobaXterm X server button is colored, or VcXsrv XLaunch window was started and is in the system tray)
  - In WSL, verify DISPLAY and connectivity:
```bash
echo $DISPLAY
# For external X servers, the host should be your Windows IP (e.g. 172.22.224.1:0.0)
ping -c1 "${DISPLAY%:*}" || true
```
  - For WSLg (Windows 11): remove manual DISPLAY lines from `~/.bashrc` and try again (WSLg sets DISPLAY automatically)

- Black/blank Minecraft or GLX errors:
  - For VcXsrv, relaunch with “Disable Native OpenGL”; keep “Disable access control” enabled
  - In WSL Ubuntu, try:
```bash
export LIBGL_ALWAYS_INDIRECT=1
```
  - If performance is very slow with the above, remove it and retry under WSLg if possible

- `java.awt.HeadlessException` or AWT errors:
  - Ensure a GUI is available and `xeyes` works before starting BlockAssist
  - Confirm DISPLAY is set (for external X) and X server is running

- When running `run.py` on WSL, if Minecraft is stuck at "building" and Malmo logs show only one line:
```text
Starting a new Gradle Daemon for this build (subsequent builds will be faster).
```
  - Open a new terminal and check: `cd blockassist && tail -f logs/malmo.log`
  - If you only see the line above and nothing else, the X server is not connected; fix your X setup:
    - For WSLg: remove manual DISPLAY overrides and retry
    - For external X: start MobaXterm/VcXsrv, ensure DISPLAY points to Windows host, verify `xeyes` opens

- Input not captured inside Minecraft:
  - Click the game window and press ENTER once to focus the window (see Run section notes)

- Corporate VPN/Firewall blocking X traffic:
  - Use WSLg (Windows 11) instead of external X; or temporarily allow VcXsrv through Windows Firewall



## Get Block Discord Role
Visit [massage url](https://discord.com/channels/852932483691577395/1397909075685146804/1403436609583579146) and follow instructions 

<img width="1100" height="532" alt="Screenshot_833" src="https://github.com/user-attachments/assets/c01ef1a1-1f64-4352-ba7a-e51287cb02e8" />

