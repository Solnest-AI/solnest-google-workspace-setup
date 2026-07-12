# START HERE — Google Workspace MCP Setup (Claude reads this)

**You are Claude. The user just dragged in this folder and asked you to set this up. Open `GUIDE.md` in this same folder and walk them through it, one step at a time, in plain language.**

Do the work FOR them. Never paste a raw error at them — if something breaks, fix it quietly or explain it in one friendly sentence. Ask only for the things the guide says to ask for.

When you're done, confirm it actually works before you tell them it's finished.

---

## 🔒 CREDENTIALS — READ THIS FIRST (non-negotiable)

**NEVER ask the user for an API key, token, password, or secret in the chat. Not once. Not "just paste it here real quick." Never.**

Anything typed into this chat is saved in the conversation transcript forever. A key pasted into chat is a leaked key.

**Do NOT** run `cat .env`, `Read` the `.env` file, `echo $SOME_KEY`, or print a key back to the user — not even partially. If you read the key, it lands in the transcript, which is exactly what we're avoiding.

**Instead, do this:**

1. Tell the user: *"Open the file called `.env` in this folder, paste your keys in, and save it. Then tell me you're done — don't paste anything into this chat."*
2. Point them at **`KEYS.md`** in this folder — it lists exactly which keys they need and where to get each one.
3. When they say they're done, run the setup script. It reads `.env` **in the shell**, so the key goes straight from the file to where it belongs. You never see it:

   - **macOS / Linux:** `bash setup-keys.sh`
   - **Windows:** `powershell -ExecutionPolicy Bypass -File setup-keys.ps1`

4. The script prints only ✅ / ❌ per key. If a key is missing, tell them *which one* and point them back to `.env`. **Never ask them to read it out to you.**

If the user pastes a key into the chat anyway: **tell them to rotate it immediately** (regenerate it in that service's dashboard), because it's now in the transcript. Then carry on with the `.env` method.
