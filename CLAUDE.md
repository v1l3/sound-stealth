# Instructions for Claude

Two people work on this repo and share one Claude account. Before changing anything in Unreal,
find out who you're working with. They start with "Jan here, want to work in Unreal" or
"Jakub here, want to work in Unreal". If it's unclear, ask "Jan or Jakub?" and change nothing until they answer.

| Person | GitHub | Role | May change |
|---|---|---|---|
| Jan | v1l3 | Gameplay | `SoundStealth/Content/Jasiu/` |
| Jakub | Qubs123 | Level design | `SoundStealth/Content/Qubs/` |

## "<name> here, want to work in Unreal"

1. **Check you're on that person's computer.** Jan's Mac Studio has the clone at `~/Developer/sound-stealth`.
   Jakub works on his own computer. Don't work as Jakub on Jan's machine, because commits and locks would be under v1l3.
2. **Get the latest:** make sure Unreal is closed, then run `git pull` in the repo root.
3. **Open the project:** `SoundStealth/SoundStealth.uproject` (macOS: `open SoundStealth/SoundStealth.uproject`).
4. **Connect to the editor:** the Unreal MCP server runs on `localhost:8000` once started
   (console `ModelContextProtocol.StartServer`, or Editor Preferences > Auto Start Server).
5. **Work only in that person's folder** (table above).
   - Never edit the other person's folder.
   - Never edit template folders (`FirstPerson/`, `Characters/`, `Input/`, `LevelPrototyping/`). Duplicate the asset
     into the person's folder and edit the copy.
   - Don't reference assets in the other person's folder until they agree to join the work.
   - `Config/` and `SoundStealth.uproject` (project settings, plugins): ask first, and say the other person will get the change.
6. **Finish:** Save All, then `git add` only the person's folder (plus anything they approved), commit with a short
   message, and `git push`. Run `git status` first, and don't sweep in unrelated changes.

Before any big or bulk change, make sure the current state is committed, so Git can undo it.
See `SETUP.md` for the full team workflow.
