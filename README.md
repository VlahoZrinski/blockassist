# BlockAssist

<img width="680" height="333" alt="image" src="https://github.com/user-attachments/assets/7f42b743-9bcd-4e75-9f09-c81af474bd10" />


 **BlockAssist** is an AI bot that learns from you in Minecraft. It starts dumb but gets smarter as you play.

Steps:
1. Follow setup instructions below
2. Play Minecraft episodes. Complete building progress bar.
3. Track your participation on the leaderboard.

## Hardware Requirements
### Local systems (Recommended, Best game experience)
**Linux or Mac**: This guide is for **Linux** users, **Mac** users check [official guide](https://github.com/gensyn-ai/blockassist/tree/main) for Mac commands)

**WSL**: NOT supported for now (Will update this for WSL users)

**Ubuntu on Windows via VirtualBox**
* Download VirtualBox from Oracle website
* Install VirtualBox on Windows
* Enable virtualization in BIOS if needed
* Download Ubuntu Desktop 22.04 LTS ISO file
* Click on **New** to Create new VM in VirtualBox:
  * Name and Operating system: Choose ISO file
  * Unattended Instal: Choose a username and password for ubuntu user login
  * Hardware: 8GB minimum, 16GB recommended for RAM, add multiple CPU cores
  * Hard Disk: 100GB minimum
* Make `your-user` an admin (sudo) user
  * Power off the VM
  * Start the VM and hold Right-Shift (or press Esc repeatedly) to show GRUB.
  * Select “Advanced options for Ubuntu” → choose the entry with “(recovery mode)”.
  * In the Recovery Menu, choose “root – Drop to root shell”. → Insert a password if prompted.
  * Remount root as read-write:
    ```bash
    mount -o remount,rw /
    ```
  * Add your user to sudo group and set password (replace your-user):
    ```bash
    adduser your-user sudo
    passwd your-user
    ```
  * Reboot:
    ```bash
    reboot
    ```
  * Log back in as `your-user`, then verify sudo works:
    ```bash
    sudo whoami    # should print: root
    ```
* Optimize your VM settings:
  * Poweroff the VM and go to Virtualbox app. → Settings
  * Display → Video Memory: 128 MB (max)
  * Display → Graphics Controller: VBoxSVGA
  * Display → Enable 3D Acceleration: OFF (keep OFF until system is stable)
  * General → Advanced → Shared Clipboard: Bidirectional
  * General → Advanced → Drag’n’Drop: Bidirectional
 * Enable Clipboard (install Guest Additions):
   * Inside Ubuntu:
    ```
    sudo apt update
    sudo apt install -y build-essential dkms linux-headers-$(uname -r)
    ```
   * VM window top bar → Devices → “Insert Guest Additions CD Image…”
   * Then run in terminal:
    ```
    sudo mkdir -p /mnt/cdrom
    sudo mount /dev/cdrom /mnt/cdrom
    cd /mnt/cdrom
    sudo ./VBoxLinuxAdditions.run
    sudo reboot
    ```
* Verify integration:
  * Clipboard: copy text in Windows → paste in Ubuntu Terminal. Ctrl+Shift+V or middle-click (paste), Ctrl+Shift+C (copy), or right‑click → Paste.
  * Resize VM window; display should auto-resize.
  * If clipboard still doesn’t work after reboot, start helpers:
    ```
    VBoxClient --clipboard &
    VBoxClient --draganddrop &
    VBoxClient --checkhostversion &

    mkdir -p ~/.config/autostart
    cat > ~/.config/autostart/vboxclient-clipboard.desktop << 'EOF'
    [Desktop Entry]
    Type=Application
    Name=VBoxClient Clipboard
    Exec=/usr/bin/VBoxClient --clipboard
    X-GNOME-Autostart-enabled=true
    EOF
    ```

### Cloud GPUs (Desktop-gui enabled, VNC Desktop)
**Important Note: This method is actually running Minecraft inside a *VNC Desktop* which provides a very slow game. I will update it with better methods very soon**

**[Vast](https://cloud.vast.ai/?ref_id=62897&creator_id=62897&name=Linux%20Desktop%20Container)**: Rent a GPU with [Linux Desktop Container](https://cloud.vast.ai/?ref_id=62897&creator_id=62897&name=Linux%20Desktop%20Container) template, then go to *instances* page and wait for your gpu to be deployed.

**To open your gpu desktop, you have two options:**
* Vast preserved desktops (Recommended):
  * Click on **Open** (Blue button) to head to the gpu dashboard
  * Access to the desktop via **Selkies Low Latency Desktop** or **Apache Guacamole Desktop (VNC)**

* Local VNC client:
  * Click on **Open** (Blue button) to head to the gpu dashboard
  * Access to the terminal via **Jupyter Terminal**
  * Find your Dekstop password by executing: `echo $OPEN_BUTTON_TOKEN`
  * In instances page, click on gpu IP and find the mapped host:port to `5900/tcp`, for example it can be: `70.45.225.15:18483`
  * Install [RealVNC](https://www.realvnc.com/en/connect/download/viewer/)
  * Enter the mapped host:port and then it prompts for the password to access to your desktop.
  * To install a Chrome browser on it, visit [VNC (Linux desktop)](https://github.com/0xmoei/Install-VNC-Linux-Desktop/) Full Guide  


## Installation
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
pip install -e .
pip install psutil readchar
```

---

## Run BlockAssist

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
* Go to the first Minecraft window, the game will start.
* **Note for VNC users**: To enable in-game keys, Press `ENTER` inside the game window.
* Build the building to increase your progress. (More progress = Better trained AI).
  * Left-click pickaxe to remove, Left-click *Dirt* on red tiles, Right-click stone, glasses, plonks on place holders.
* Then return to your terminal and press `ENTER` to end the session.

<img width="1754" height="838" alt="Screenshot_825" src="https://github.com/user-attachments/assets/5acf6764-4040-4615-a5e7-5ea4e0b0fe6c" />

**Note: Even if you don't finish the eposide till the end and close it, you'll earn your participation in the leaderboard**

### Training
A model will be started training now and be submitted to Hugging Face and to Gensyn’s smart contract.

### Verify
If you reach this stage in the logging window and can see a transaction in the block explorer, your submission has succeeded.

A successful training logging:

<img width="861" height="421" alt="Untitl111ed" src="https://github.com/user-attachments/assets/3327ca8d-4e50-4d0d-b77e-e2a787c09e20" />


### Leaderboard
Login to [BlockAssist leaderboard](https://dashboard.gensyn.ai) to check your participation points

### Optional: Check logs
If needed, you can check logs by opening a *new* terminal and running this:
```bash
blockassist && tail -f logs/malmo.log
```

### Configuration
You can modify settings in the `src/blockassist/config.yaml` file or override them via command-line arguments.

- `episode_count` — Controls the number of episodes. If `episode_count` is greater than 1, a new episode will start each time you press `ENTER` during session recording.

- `num_training_iters` — Controls the number of training iterations across all recorded episodes.

---

## Troubleshooting
**Increase Malmo startup timeout**
```
cd blockassist
sed -i 's#python -m malmo.minecraft launch#python -m malmo.minecraft launch --timeout 300#' scripts/run_malmo.sh
```
