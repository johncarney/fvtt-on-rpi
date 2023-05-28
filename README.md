# Foundry VTT: Raspberry Pi setup

Developed in part from [Helping Ninja][helping-ninja]'s YouTube series on [Running Foundry Virtual Tabletop on your Raspberry Pi][foundry-on-raspberry-pi].

1. [Install Raspberry Pi OS](#install-os) on fresh microSD card
2. [Install SSH key](#install-ssh-key) for easy, secure logins
3. [Upgrade OS](#upgrade-os)
4. [Install **git** & **python**](#install-git-and-python)
5. [Install **docker** & **docker-compose**](#install-docker-and-docker-compose)
6. [Setup GitHub SSH key](#setup-github-ssh-key)
7. [Setup GPG signing key (optional)](#setup-gpg-signing-key-optional)
8. [Install Foundry VTT](#install-foundry-vtt)
9. [Tighten security](#tighten-security)
   1. [Disable password authentication](#disable-password-authentication)

Note that the detailed instructions for steps 3 through 4 ask you to connect via SSH each time, but there is no reason you can't do them all in a single SSH session.

[helping-ninja]:           https://www.youtube.com/@helpingninja
[foundry-on-raspberry-pi]: https://youtube.com/playlist?list=PL4RF7atXITOwF2MCwby_gwtfILX6NpiII

## Install OS

Install 64-bit Raspberry Pi OS lite using Raspberry Pi imager.

Option          | Setting
----------------|-------------------------------
WiFi            | Do not enable
hostname        | foundryvtt
Enable SSH      | Allow public-key authentication only
Username        | foundryvtt
Password        | Get from password manager
Wireless LAN    | Configure
Locale settings | As appropriate for your region

## Install SSH key

Log into Raspberry Pi using password to check that it works, then log out immediately.

```
ssh foundryvtt@foundryvtt.local
exit
```

Use **ssh-copy-id** to install your SSH key. You will be prompted for the password.

```
ssh-copy-id foundryvtt@foundryvtt.local
```

Now try logging in using SSH authentication.

```
ssh foundryvtt@foundryvtt.local
```

## Upgrade OS

```
ssh foundryvtt@foundryvtt.local
```

```
sudo apt update
sudo apt full-upgrade --assume-yes
sudo apt autoclean
sudo apt autoremove --assume-yes
```

Once this is complete you should reboot your Raspberry Pi. This will close your SSH session.

```
sudo reboot
```

## Install **git** and **python**

```
sudo apt install git python3 python3-pip --assume-yes
```

## Install **docker** and **docker-compose**

```
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker ${USER}
sudo pip3 install docker-compose
sudo systemctl enable docker
```

Once this is complete you should reboot your Raspberry Pi. This will close your SSH session.

```
sudo reboot
```

### Testing your docker installation

```
docker run hello-world
```

## Setup GitHub SSH key

Create an SSH key and add it to your GitHub account as an authentication key.

```
ssh-keygen
cat ~/.ssh/id_rsa.pub
```

Set your name and email address for git.

```
git config --global user.name "Alice Beaufort"
git config --global user.email "alice.beaufort@example.com"
```

## Setup GPG signing key (optional)

To set up a GPG signing key for git commits, follow the instructions on GitHub under [Managing commit signature verification](https://docs.github.com/en/authentication/managing-commit-signature-verification).

## Install Foundry VTT

### Create folders

Log into your Raspberry Pi and create a data folder for Foundry VTT and another for docker. You can name these whatever you like.

```
ssh foundryvtt@foundryvtt.local
```

```
mkdir ~/foundry-data
mkdir ~/docker
```

### Create **docker-compose.yml**

```yaml
version: '3.9'

services:
  foundry:
     image:        felddy/foundryvtt:release
     hostname:     foundryvtt
     network_mode: host
     init: true
     restart: "unless-stopped"
     volumes:
       - type: bind
         source: ~/foundry-data
         target: /data
     environment:
       - FOUNDRY_USERNAME=
       - FOUNDRY_PASSWORD=
       - FOUNDRY_ADMIN_KEY=
```

### Connect to your Foundry VTT server

```
http://foundryvtt.local:30000
```

### Install game system

- Pathfinder 2E

### Install modules

#### Content modules

- Pathfinder Beginner Box

#### Recommended modules

Module                        | Description                              | Recommended by
------------------------------|------------------------------------------|--------------------------------------------------------------
[Ambient Doors]               | Sound effects for doors                  | [The Rules Lawyer]
[Combat Enhancements]         | Combat tracker enhancements              | [The Rules Lawyer]
[Dice So Nice!]               | 3D animated dice                         | [Recall Knowledge], [The Rules Lawyer]
[Dice Tray]                   |                                          | [Recall Knowledge], [The Rules Lawyer]
[Foundry VTT Arms Reach]      | Limit player interactions with doors etc | [Copper Dragon Games]
[Health Estimate]             |                                          | [Recall Knowledge], [The Rules Lawyer]
[Herolab Online PF2e Import]  | Import Herolab characters                | [The Rules Lawyer]
[Initiative Quick Change]     | Double-click initiative to edit          | [Recall Knowledge]
[Koboldworks - Ready Up!]     |                                          | [Copper Dragon Games]
[Minimal UI]                  |                                          | [Copper Dragon Games]
[PDF to Foundry (PF2e)]       | Import Adventure Path modules            | [Recall Knowledge], [The Rules Lawyer]
[Foundry VTT Arms Reach]      | Limit player interactions with doors etc | [Copper Dragon Games] Lawyer]
[PF2e Dorako UI]              | UI enhancements                          |
[PF2e Drag Ruler Integration] | Drag ruler                               | [Recall Knowledge], [The Rules Lawyer]
[PF2e Target Damage]          |                                          |
[Pings]                       | Ping the map                             | [Recall Knowledge], [The Rules Lawyer]
[PopOut!]                     |                                          | [Copper Dragon Games], [Recall Knowledge], [The Rules Lawyer]
[Sync Token Names]            |                                          | [Copper Dragon Games]
[Tidy UI]                     | Collapse checkboxes                      | [The Rules Lawyer]
[Token Action HUD]            |                                          | [Recall Knowledge], [The Rules Lawyer]
[Torch]                       |                                          | [Recall Knowledge], [The Rules Lawyer]

#### Things to evaluate

Module                        | Description                              | Recommended by
------------------------------|------------------------------------------|------------------------------------------
[DF Architect]                | Map building tools                       | [Copper Dragon Games]
[Multiface Tiles]             |                                          | [Copper Dragon Games]
[Multilevel Tokens]           | Teleport tokens                          | [Recall Knowledge]
[PF2e Rule Element Generator] | Automations(?)                           | [Recall Knowledge]
[pf2E Keybind Menagerie]      | PF2E keyboard shortcuts                  | [Recall Knowledge]
[Popout Resizer]              | Resize popouts                           | [Copper Dragon Games], [The Rules Lawyer]
[Simple Dice Roller]          |                                          | [Recall Knowledge]
[Smart Target]                | Targeting helpers                        | [Recall Knowledge]
[Stairways (Teleporter)]      | Teleport tokens                          | [Copper Dragon Games]
[Token Mold]                  | Token enhancements                       | [Recall Knowledge]

[Copper Dragon Games]: https://youtu.be/45-0rnEIZfM
[Recall Knowledge]:    https://youtu.be/pcp4AaaxhGs
[The Rules Lawyer]:    https://youtu.be/e9O8ClXhjxY

[Ambient Doors]:               https://foundryvtt.com/packages/ambientdoors
[Combat Enhancements]:         https://foundryvtt.com/packages/combat-enhancements
[DF Architect]:                https://foundryvtt.com/packages/df-architect
[Dice So Nice!]:               https://foundryvtt.com/packages/dice-so-nice
[Dice Tray]:                   https://foundryvtt.com/packages/dice-calculator
[Foundry VTT Arms Reach]:      https://foundryvtt.com/packages/foundryvtt-arms-reach
[Health Estimate]:             https://foundryvtt.com/packages/healthEstimate
[Herolab Online PF2e Import]:  https://foundryvtt.com/packages/hlo-importer
[Initiative Quick Change]:     https://foundryvtt.com/packages/fvtt-initiative-quick-change
[Initiative Quick Change]:     https://foundryvtt.com/packages/fvtt-initiative-quick-change
[Koboldworks - Ready up!]:     https://foundryvtt.com/packages/koboldworks-ready-up
[Minimal UI]:                  https://foundryvtt.com/packages/minimal-ui
[Multiface Tiles]:             https://foundryvtt.com/packages/multiface-tiles
[Multilevel Tokens]:           https://foundryvtt.com/packages/multilevel-tokens
[PDF to Foundry (PF2e)]:       https://foundryvtt.com/packages/pdftofoundry
[Perfect Vision]:              https://foundryvtt.com/packages/perfect-vision
[Pf2e Dorako UI]:              https://foundryvtt.com/packages/pf2e-dorako-ui
[PF2e Drag Ruler Integration]: https://foundryvtt.com/packages/pf2e-dragruler
[pf2E Keybind Menagerie]:      https://foundryvtt.com/packages/pf2e-f-is-for-flatfooted
[PF2e Rule Element Generator]: https://foundryvtt.com/packages/rule-element-generator
[PF2e Target Damage]:          https://foundryvtt.com/packages/pf2e-target-damage
[Pings]:                       https://foundryvtt.com/packages/pings
[Popout Resizer]:              https://foundryvtt.com/packages/popout-resizer
[PopOut!]:                     https://foundryvtt.com/packages/popout
[Simple Dice Roller]:          https://foundryvtt.com/packages/simple-dice-roller
[Smart Target]:                https://foundryvtt.com/packages/smarttarget
[Stairways (Teleporter)]:      https://foundryvtt.com/packages/stairways
[Sync Token Names]:            https://foundryvtt.com/packages/sync-token-names
[Tidy UI]:                     https://foundryvtt.com/packages/tidy-ui_game-settings
[Token Action HUD]:            https://foundryvtt.com/packages/token-action-hud
[Token Mold]:                  https://foundryvtt.com/packages/token-mold
[Torch]:                       https://foundryvtt.com/packages/torch

## Connect to the outside world

### **ngrok**



## Tighten security

### Disable password authentication

In order to tighten up security on your server, you should disable SSH password authentication and use SSH key authentication only.

## Settings

### Configure Settings

- Core
  - Left-Click to Release Objects: Enabled
- Pathfinder 2nd Edition
  - Automation
    - *Rules-based vision*:                    Enabled
    - *Immunities, weaknesses, & resistances*: Enabled
    - *Effects auto-expire*:                   Enabled
    - *Remove expired effects*:                Enabled
    - *Flanking detection*:                    Enabled
    - *NPCs are lootable*:                     Enabled?
  - Metagame information
    - *Shared party vision*: Enabled?
  - *Campaign Feats*: Enabled
- Follew Me!
  - *Snap to grid*:             Enabled
  - *Stop following in combat*: Enabled
- Token Action HUD
  - Direction: Down
- PF2e Target Damage
  - *Hide non-Player Token Targets*:   Checked?
  - Hide Collapsible Buttons:          Checked
  - Hide Original PF2e Damage Buttons: Checked
- Walled Templates:
  - Enable autotargetting: Display toggle button; default on

### Configure Controls

- DF Architect
  - *Toggle Wall Chaining Lock*: Alt+W
- Follow Me!
  - FollowMe: Shift+F
