# BlockAssist

<div align="center">

<img width="1280" height="640" alt="image" src="https://github.com/user-attachments/assets/453dad02-0cf6-4147-82f9-f968073bb681" />

</div>

**BlockAssist** is an AI assistant that learns from its user’s actions in Minecraft. The assistant appears in-game with you, starting with only basic knowledge of the game’s commands. As you play, it learns how to assist you in building, learning directly from your actions. It shows an early demo of _assistance learning_ - a new paradigm for aligning agents to human preferences across domains.

Steps:
1. Follow setup instructions below
2. Play Minecraft episodes and complete the building goal in the shortest time possible.  This will help train the best assistant models.
3. Share your progress with the community by posting your gameplay videos, stats, and Hugging Face uploads on Discord and X. Track your participation on the leaderboard.

**You do not need a copy of Minecraft to play! BlockAssist includes a free version.**


## Hardware Requirements
### Local systems
**Linux or Mac**: This guide is for **Linux** users, **Mac** users follow [official guide](https://github.com/gensyn-ai/blockassist/tree/main))

**WSL**: NOT supported

### Cloud GPUs
**[Vast](https://cloud.vast.ai/?ref_id=62897&creator_id=62897&name=Linux%20Desktop%20Container)**: Rent a GPU with [Linux Desktop Container](https://cloud.vast.ai/?ref_id=62897&creator_id=62897&name=Linux%20Desktop%20Container) template

password: `echo $OPEN_BUTTON_TOKEN`


## Installation

### Step 1: Install Dependecies
```bash
sudo apt update && \
sudo apt upgrade -y && \
sudo apt install -y \
make git build-essential gcc \
libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev curl \
libncursesw5-dev xz-utils tk-dev \
libxml2-dev libxmlsec1-dev \
libffi-dev liblzma-dev zip unzip
```

### Step 2: Clone the repo and enter the directory
```bash
git clone https://github.com/gensyn-ai/blockassist.git
cd blockassist
```

### Step 3: Install Java
```bash
./setup.sh
```
```
sudo apt update
sudo apt install openjdk-8-jdk
```
```
echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64' >> ~/.bashrc
source ~/.bashrc
```

### Step 4: Install `pyenv`
```bash
curl https://pyenv.run | bash

export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
```
* Restart your terminal and `cd blockassist` again.

### Step 5: Install Python 3.10

```bash
pyenv install 3.10
pyenv global 3.10
```

### Step 6: Install project dependecies
```bash
pip install --upgrade pip
pip install -e .
pip install psutil readchar

corepack enable
yarn install --frozen-lockfile
```
* If got any error in installing `readchar`, I fixed it by adding commands in Running BlockAssist in the next step



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
* Then return to your terminal and press `ENTER` to end the session.

<img width="1754" height="838" alt="Screenshot_825" src="https://github.com/user-attachments/assets/5acf6764-4040-4615-a5e7-5ea4e0b0fe6c" />


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




