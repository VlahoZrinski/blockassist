# BlockAssist

<img width="680" height="333" alt="image" src="https://github.com/user-attachments/assets/7f42b743-9bcd-4e75-9f09-c81af474bd10" />


 **BlockAssist** is an AI bot that learns from you in Minecraft. It starts dumb but gets smarter as you play.

Steps:
1. Follow setup instructions below
2. Play Minecraft episodes. Complete building progress bar.
3. Track your participation on the leaderboard.

## Hardware Requirements
* 8 GB RAM minimum (16 GB recommended)
* Multi‑core CPU
* 100 GB disk space

## Enviorement
### Local systems (Recommended, best game experience)
**Linux or Mac**: This guide is for **Linux** users, **Mac** users check [official guide](https://github.com/gensyn-ai/blockassist/tree/main) for Mac commands)

**WSL**: NOT supported for now (Will update this for WSL users)

**Ubuntu on Windows via VirtualBox**: Detailed guide to setup [Ubuntu on Windows via VirtualBox](./ubuntu-virtualbox.md)

### Cloud GPUs (Desktop-gui enabled, VNC Desktop)
**Important Note: This method is actually running Minecraft inside a **VNC Desktop** which provides a slow game, but a more straight forward installation. I will update the guide with better solutions very soon**

**Vast Linux Desktop Template**: Rent a GPU with my custom [Moei Linux Desktop Container](https://cloud.vast.ai/?ref_id=228875&creator_id=228875&name=Moei%20Linux%20Desktop%20Container) template, then go to *instances* page and wait for your gpu to be deployed.

**To open your gpu desktop, you have two options:**
1. Vast preserved desktops (Recommended):
Click on **Open** (Blue button) to head to the gpu dashboard, and Access to the desktop via:
- Selkies Low Latency Desktop**(Recommended, most performant)
- **Apache Guacamole Desktop (Low performance)**

3. Local VNC client:
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

pip install -e . --no-cache-dir
pip install "mbag-gensyn[malmo]" --no-cache-dir

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

## Troubleshooting
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


