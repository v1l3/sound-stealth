# Team setup: GitHub + Git LFS + Unreal

Two people, one repo. Unreal assets (`.uasset`, `.umap`) are binary and **cannot be merged**,
so the golden rule is: **lock before you edit, unlock after you push.**

## Who owns what

| Folder | Owner | What lives there |
|---|---|---|
| `Content/Game/` | Jasiu | Maps, Blueprints, AI (creatures), player, UI, meshes, materials |
| `Content/Audio/` | Brother | Sound waves, MetaSounds, Sound Cues, attenuation & concurrency settings, submixes |
| `Content/Game/Maps/` | Jasiu (shared on request) | Levels. Ask before touching; lock always |
| `Source/`, `Config/` | Jasiu | C++ (if any) and project settings. Text files, these *can* merge |

Hand-off points between the two of you:
- **Gameplay calls audio** through a small set of assets in `Content/Audio/Interfaces/`
  (e.g. one MetaSound or Sound Cue per game event: `SFX_Footstep`, `SFX_DoorOpen`).
  Jasiu wires them into Blueprints once; the brother can then reshape the sound freely
  without touching Jasiu's files.
- **Audio reports noise to the AI** through a shared Blueprint (e.g. `BP_NoiseEmitter`
  using `Report Noise Event`) owned by Jasiu. The brother tells Jasiu the loudness values he wants.
- Placing sounds in a level means editing the `.umap`, so either ask Jasiu to unlock it,
  or put ambient sound actors in a **sub-level** `Content/Game/Maps/<Level>_Audio.umap`
  that the brother owns. (Recommended: World Partition "Data Layers" or a streamed sub-level.)

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
   git clone https://github.com/v1l3/<repo>.git
   ```
   Clone it to a short path with no spaces, e.g. `C:\Dev\<repo>`.
5. **Make locks enforce read-only files** (run inside the cloned folder):
   ```
   git config lfs.locksverify true
   ```
   Files marked `lockable` will now be read-only until you lock them, which stops
   accidental edits.

## Creating the Unreal project (Jasiu, once)

1. Create the project in **Unreal Engine 5.8.1** (both of you must use exactly this version), saving it **inside** the cloned folder
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
   git lfs lock Content/Audio/SFX_Footstep.uasset
   ```
3. Edit, save.
4. **Commit and push:** *Source Control → Submit Content* in Unreal, or
   ```
   git add -A
   git commit -m "Footstep variations"
   git push
   ```
5. **Unlock** after pushing (Unreal's submit can do this automatically), or:
   ```
   git lfs unlock Content/Audio/SFX_Footstep.uasset
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
