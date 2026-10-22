# Team setup: GitHub + Git LFS + Unreal

Two people, one repo. Unreal assets (`.uasset`, `.umap`) are binary and **cannot be merged**,
so the golden rule is: **lock before you edit, unlock after you push.**

> Qubs123? Start with [QUBS_SETUP.md](QUBS_SETUP.md).

## Who owns what

The Unreal project lives in `SoundStealth/`, so all `Content/` paths below are inside `SoundStealth/Content/`.

| Folder / file | Owner | What lives there |
|---|---|---|
| `Content/FirstPerson/` (incl. `Lvl_FirstPerson`) | Jasiu | Template map = Jasiu's gameplay sandbox, player character, GameMode, controller |
| `Content/Gameplay/` | Jasiu | Gameplay Blueprints: interactables, items, inventory, health, enemy AI, UI |
| `Content/Levels/` | Qubs123 | Level design: ship blockouts, one map per deck/area (e.g. `Levels/Ship/Deck_Lobby`) |
| `Content/LevelPrototyping/` | Qubs123 | Template blockout meshes/materials for grey-boxing |
| `Content/Characters/`, `Content/Input/` | Shared, ask first | Mannequins stand in for every character for now |
| `Content/Developers/<name>/` | Each of you | Personal scratch space. Nothing outside may reference it |
| `Config/`, `*.uproject` | Jasiu | Project settings (text files, these *can* merge) |
| `Docs/` | Both | Story, characters, gameplay ideas as Markdown. Text merges fine, edit freely |

How the two halves connect without touching each other's files:
- **Qubs123 places Jasiu's Blueprints** (doors, pickups, enemies) into his levels. Placing one only changes his
  level, never the Blueprint. If a Blueprint needs to behave differently, he asks Jasiu. Jasiu marks
  variables **Instance Editable** so he can tweak them per placement himself.
- **Agree on metrics early:** player height, jump/crouch height, corridor and door widths, stair step
  height. Jasiu keeps a small metrics test area; levels are built to those numbers.
- **Big ship levels use World Partition with One File Per Actor** (already on in this project, see
  `__ExternalActors__`). Each placed object is its own file, so you can both work in the same level
  without locking the whole map. Lock the individual actors instead.
- **Work on `main` with locks.** Skip branches for now: Unreal assets can't be merged between branches.

## One-time setup (both of you)

1. **Install Git** — https://git-scm.com (Windows installer includes Git LFS).
   macOS: `brew install git git-lfs`.
2. **Install Git LFS hooks** — open a terminal and run:
   ```
   git lfs install
   ```
3. **Tell Git who you are:**
   ```
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```
4. **Clone the repo** (Jasiu shares the URL and adds the brother as a collaborator
   under GitHub → repo → Settings → Collaborators):
   ```
   git clone https://github.com/v1l3/sound-stealth.git
   ```
   Clone it to a short path with no spaces, e.g. `C:\Dev\sound-stealth`.
5. **Make locks enforce read-only files** (run inside the cloned folder):
   ```
   git config lfs.locksverify true
   ```
   Files marked `lockable` will now be read-only until you lock them, which stops
   accidental edits.

## Creating the Unreal project (Jasiu, once)

1. Create the project in **Unreal Engine 5.8** (latest hotfix, 5.8.4 as of Oct 2026) (both of you on the same 5.8 hotfix; never jump to 5.9 without agreeing), saving it **inside** the cloned folder
   so the `.uproject` sits next to `.gitignore`.
2. Close Unreal, then:
   ```
   git add .
   git commit -m "Initial Unreal project"
   git push
   ```

## Connecting Unreal's source control

Recommended: the **Project Borealis Git plugin** (handles LFS locking properly).
1. Download it from https://github.com/ProjectBorealis/UEGitPlugin (Releases page, pick the build for UE 5.8; if none exists yet, use the built-in provider below)
   and put it in `<project>/Plugins/UEGitPlugin/`. Commit it so the
   other person gets it too.
2. Open the project → bottom right **Source Control → Connect to Source Control**.
3. Provider: **Git LFS 2**. Tick **Uses Git LFS 2 File Locking workflow** and enter your
   **GitHub username** as the lock user. Click *Accept Settings*.

(Fallback: the built-in **Git (beta)** provider works for commits but its locking support is weaker.)

## Daily workflow

1. **Pull first:** close Unreal, `git pull` (or *Source Control → Sync* in Unreal).
2. **Lock what you'll edit:** in the Content Browser right-click the asset → *Source Control → Check Out*
   (that's the lock). Terminal equivalent:
   ```
   git lfs lock "SoundStealth/Content/Gameplay/BP_Door.uasset"
   ```
3. Edit, save.
4. **Commit and push:** *Source Control → Submit Content* in Unreal, or
   ```
   git add -A
   git commit -m "Door opens on interact"
   git push
   ```
5. **Unlock** after pushing (Unreal's submit can do this automatically), or:
   ```
   git lfs unlock "SoundStealth/Content/Gameplay/BP_Door.uasset"
   ```

Useful: `git lfs locks` lists every lock and who holds it.
If someone forgot to unlock and is unreachable: `git lfs unlock --force <file>` (owner only, use sparingly).

## Tips

- **Small, frequent commits.** Push at least once per working session.
- **Never edit a file you haven't locked.** If it's locked by the other person, message them.
- **Raw audio sessions (DAW projects, stems) stay out of the repo.** Commit only the exported WAVs
  the game uses. GitHub's free LFS storage/bandwidth quota is limited, and audio fills it fast:
  check the usage under GitHub → Settings → Billing.
- Renaming/moving assets leaves *redirectors*; run **Fix Up Redirectors** on the folder and commit
  that too.
