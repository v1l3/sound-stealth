# Instructions for Claude

Two brothers work on this repo and share one Claude account. Before changing anything in Unreal,
find out who you're working with. They start with "Jan here, want to work in Unreal" or
"Jakub here, want to work in Unreal". If it's unclear, ask "Jan or Jakub?" and change nothing until
they answer. Saying the name is the only identification: it's a courtesy system, not security.

| Person | GitHub | Role | May change |
|---|---|---|---|
| Jan (Jasiu) | v1l3 | Gameplay | `SoundStealth/Content/Jasiu/**`, plus his gameplay sandbox map `SoundStealth/Content/FirstPerson/Lvl_FirstPerson.umap` and its `__ExternalActors__/FirstPerson/Lvl_FirstPerson/**` / `__ExternalObjects__/FirstPerson/Lvl_FirstPerson/**` files |
| Jakub | Qubs123 | Level design | `SoundStealth/Content/Qubs/**`, plus the `__ExternalActors__/Qubs/**` / `__ExternalObjects__/Qubs/**` files of maps saved there |

Both may edit `Docs/**` (story, ideas, metrics). Everything else is shared: ask first.

**Never close, quit, restart or kill Unreal**, and never discard unsaved work. Someone may be working in
it. If Unreal needs closing (e.g. to pull), ask the person at that computer to save and close it themselves.

## "<name> here, want to work in Unreal"

1. **Check you're on that person's computer.** Jan's Mac Studio has the clone at `~/Developer/sound-stealth`.
   Jakub works on his own computer. Don't work as Jakub on Jan's machine, because commits and locks
   would be under v1l3. If "Jakub here" arrives on Jan's Mac, don't touch Jan's Unreal or files at all:
   just tell Jakub to start Claude on his own computer.
2. **Get the latest:** if Unreal already has the project open, don't pull (it would overwrite files
   Unreal has loaded); skip to step 4. Otherwise run `git pull origin main` in the repo root, and make
   sure `git config lfs.locksverify` is `true`.
3. **Open the project with the MCP server on:**
   - macOS: `open -a "/Users/Shared/Epic Games/UE_5.8/Engine/Binaries/Mac/UnrealEditor.app" --args "$PWD/SoundStealth/SoundStealth.uproject" -ModelContextProtocolStartServer`
   - Windows: `"C:\Program Files\Epic Games\UE_5.8\Engine\Binaries\Win64\UnrealEditor.exe" "%CD%\SoundStealth\SoundStealth.uproject" -ModelContextProtocolStartServer`
     (adjust the path if Unreal is installed elsewhere)
4. **Connect to the editor:** the Unreal MCP server runs on `http://127.0.0.1:8000/mcp` (the `unreal`
   server in `.mcp.json`; auto-start is on in `Config/DefaultEditorPerProjectUserSettings.ini`). The
   editor can take a few minutes to load. If the `unreal` tools aren't loaded in this session, talk to the
   endpoint directly over HTTP (JSON-RPC `initialize`, then `tools/call` with `list_toolsets` /
   `describe_toolset` / `call_tool`, sending the `Mcp-Session-Id` header).
5. **Tell them it's open,** which areas you'll change for them, and show `git lfs locks` so they see
   whether the other brother holds anything.
6. **Work only in that person's areas** (table above).
   - Before any Unreal tool call that changes something, check the target asset or level path.
     Placing actors changes the *level* you place them in, so only do it in a level that person owns.
   - Never edit the other person's folder, or a file the other brother has locked.
   - Never edit template folders (`FirstPerson/` apart from Jan's map, `Characters/`, `Input/`,
     `LevelPrototyping/`). Duplicate the asset into the person's folder and edit the copy.
   - Don't reference assets in the other person's folder until they agree to join the work.
   - `Config/` and `SoundStealth.uproject` (project settings, plugins): ask first, and say the other
     person will get the change.
7. **Finish:** Save All, then `git add` only the person's paths (plus anything they approved), commit with
   a short message, and `git push origin main`. Run `git status` first, and don't sweep in unrelated changes.

Before any big or bulk change, make sure the current state is committed, so Git can undo it.
Never pull while Unreal has the project open. See `SETUP.md` for the full team workflow.
