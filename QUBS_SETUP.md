# Setup guide for Qubs123 (level design)

Hi! This gets you from zero to building levels. Follow the steps in order.
Every command goes into a terminal: **Git Bash** or **PowerShell** on Windows, **Terminal** on macOS.

## 1. Install Git and Git LFS (once)

- **Windows:** download Git from https://git-scm.com and install with the default options
  (Git LFS is included).
- **macOS:** install Homebrew (https://brew.sh), then run `brew install git git-lfs`.

Then run:
```
git lfs install
```
You should see `Git LFS initialized.`

## 2. Tell Git who you are (once)

```
git config --global user.name "Qubs123"
git config --global user.email "the-email-on-your-github-account"
```
Nothing appears after these two commands. That's normal: no message means it worked.
Check with `git config --global --list`.

## 3. Accept the invite and clone the repo (once)

1. Accept the collaborator invite from **v1l3** (check your email or https://github.com/notifications).
2. Pick a short folder with no spaces, e.g. `C:\Dev` (Windows) or `~/Dev` (macOS), and run:
   ```
   cd C:\Dev
   git clone https://github.com/v1l3/sound-stealth.git
   cd sound-stealth
   git config lfs.locksverify true
   ```
3. The first time, a browser window asks you to sign in to GitHub. Approve it.

You now have your own copy at `C:\Dev\sound-stealth`.

## 4. Install Unreal Engine 5.8

Install the **latest 5.8 hotfix** (5.8.4 as of Oct 2026) from the Epic Games Launcher. You and Jasiu should stay on the same 5.8 hotfix.
Assets saved in 5.9 would not open in 5.8, so never upgrade to a new minor version without agreeing first.

## 5. Open the project and connect source control

Wait until Jasiu has pushed the Unreal project, then:

1. In the `sound-stealth` folder run `git pull` to get it.
2. Double-click the `.uproject` file to open it.
3. Bottom right of the editor: **Source Control → Connect to Source Control**.
4. Provider: **Git LFS 2** (only once Jasiu has added the Git plugin to the project; until then, lock from the terminal with the commands in step 7).
   Tick **Uses Git LFS 2 File Locking workflow**, enter **Qubs123** as the user name,
   and click **Accept Settings**.

## 6. Your folders

You own **`SoundStealth/Content/Levels/`** (your maps: one per ship deck/area, e.g. `Levels/Ship/Deck_Lobby`)
and **`Content/LevelPrototyping/`** (blockout meshes). Use `Content/Developers/<your name>/` for experiments.

Two rules for working together:
- **Place Jasiu's Blueprints** (doors, pickups, enemies) in your levels freely, but **don't edit the Blueprints
  themselves**. Ask Jasiu if one needs to behave differently.
- **Build to the agreed metrics** (player height, corridor/door widths, stair height) so gameplay fits.
- The project uses World Partition with One File Per Actor, so each object you place is its own file and
  you can both work in the same level. Lock individual actors, not the whole map.

## 7. Every time you work

1. **Close Unreal and get the latest:** `git pull`
2. **Open Unreal and lock what you'll edit:** right-click the asset → **Source Control → Check Out**.
   (A locked asset has a red tick for the other person, so they know not to touch it.)
3. **Make your changes and save.**
4. **Send them:** **Source Control → Submit Content**, write a short message ("Lobby blockout v2"),
   and submit. This commits, pushes and unlocks in one step.

Terminal equivalents if you ever need them:
```
git lfs lock "SoundStealth/Content/Levels/Ship/Deck_Lobby.umap"
git add -A
git commit -m "Lobby blockout"
git push
git lfs unlock "SoundStealth/Content/Levels/Ship/Deck_Lobby.umap"
git lfs locks     # who has what locked
```

If an asset you need is locked by Jasiu, message them and don't edit it.
