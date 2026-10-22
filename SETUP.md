# Team setup: GitHub + Git LFS + Unreal

Two people, one repo. Unreal assets (`.uasset`, `.umap`) are binary and **cannot be merged**,
so the golden rule is: **lock before you edit, unlock after you push.**

> Qubs123? Start with [QUBS_SETUP.md](QUBS_SETUP.md).

## Who owns what

The Unreal project lives in `SoundStealth/`, so all `Content/` paths below are inside `SoundStealth/Content/`.

**For now, each of you works only in your own folder.** You never touch the same files, so there's
almost nothing to lock.

| Folder | Owner | What lives there |
|---|---|---|
| `Content/Jasiu/` | Jasiu | Gameplay: player, GameMode, interactables, items, AI, UI, test maps |
| `Content/Qubs/` | Qubs123 | Level design: ship blockouts, one map per deck/area |
| `Content/FirstPerson/`, `Characters/`, `Input/`, `LevelPrototyping/` | Nobody (template) | Use as-is. To change something, **Duplicate** it into your own folder and edit the copy |
| `Config/`, `*.uproject` | Shared | Project Settings. Text files that Git can usually merge, but tell each other when you change them |
| `Docs/` | Both | Story, gameplay ideas, metrics as Markdown. Text merges fine, edit freely |

Rules:
- **Pull before you start, push when you finish**, every session.
- **Don't reference the other person's folder** until you decide to join the work. Joining is easy later:
  Qubs123 drags Jasiu's Blueprints into his levels.
- **Agree on metrics early** (`Docs/Metrics.md`): player height, jump/crouch, corridor and door widths,
  stair height, so the levels fit the gameplay.
- **Work on `main`.** Skip branches for now: Unreal assets can't be merged between branches.
- The rare shared file (e.g. a template asset you both really must change): lock it from the terminal first
  (see *Daily workflow*).

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
   git lfs lock "SoundStealth/Content/Jasiu/BP_Door.uasset"
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
   git lfs unlock "SoundStealth/Content/Jasiu/BP_Door.uasset"
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
