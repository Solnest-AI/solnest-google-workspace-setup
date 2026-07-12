# Set Up the Google Workspace MCP

> **How to use this file:** Drag it into Claude (Claude Code or Claude Desktop) and say **"Set this up."**
> Claude will read the instructions below and walk you through the whole thing — installing the
> server, getting your Google API keys, and connecting your account. You do the browser clicks;
> Claude does the technical parts.
>
> Total time: **~15 minutes.** You need a Google account (personal Gmail or Google Workspace) and
> permission to create a free Google Cloud project (no billing, no charges).

---

## 🤖 INSTRUCTIONS FOR CLAUDE (the AI reading this file)

You are setting up the **Google Workspace MCP** (`taylorwilsdon/google_workspace_mcp`, run via
`uvx workspace-mcp`) for the user. This connects Gmail, Drive, Docs, Sheets, Slides, Calendar,
Forms, Tasks, Chat, and Contacts to Claude.

**Your job:** run every machine step yourself; for browser-only steps (Google Cloud Console), give
the user short, exact, click-by-click instructions and wait for them to finish before moving on.

**Ground rules:**
- Go **one phase at a time.** After each phase, confirm it worked before continuing.
- **Never ask the user to paste their Client Secret into the chat.** Have them save it to a file or
  paste it directly into the config you tell them to edit. Treat it like a password.
- Detect the user's setup first: **Claude Code vs Claude Desktop**, and **macOS vs Windows vs Linux.**
  Give only the commands for their setup.
- If a step fails, check the **Troubleshooting** section at the bottom before improvising.

Work through Phases 0–6 in order.

---

## Phase 0 — Detect environment & install `uv`

1. Ask the user (if you don't already know):
   - **"Are you using Claude Code (the terminal/CLI) or Claude Desktop (the app)?"**
   - Their operating system (macOS / Windows / Linux).
2. `uvx` (from Astral's `uv`) runs the server. Check if it's installed:
   ```bash
   uvx --version
   ```
3. If it's missing, install `uv`:
   - **macOS (Homebrew):** `brew install uv`
   - **macOS / Linux (no Homebrew):** `curl -LsSf https://astral.sh/uv/install.sh | sh`
   - **Windows (PowerShell):** `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"`
   - Then re-open the terminal and confirm with `uvx --version`.

✅ **Done when:** `uvx --version` prints a version number.

---

## Phase 1 — Create a Google Cloud project

> **TELL THE USER (they do this in a browser):**

1. Go to **https://console.cloud.google.com/projectcreate**
2. Name it something like `workspace-mcp` → **Create**.
3. Make sure that new project is selected in the top bar (the dropdown next to "Google Cloud").

> This is free. You will **not** be asked for a credit card and nothing bills.

✅ **Done when:** the user confirms the project is created and selected.

---

## Phase 2 — Turn on the Google APIs

> **TELL THE USER:** For each API below, this is the fastest way — open the link, make sure your new
> project is selected at the top, and click **Enable**. (Or go to **APIs & Services → Library** and
> search each name.)

Enable all **12**:

| API | Direct enable link |
|-----|--------------------|
| Gmail | https://console.cloud.google.com/apis/library/gmail.googleapis.com |
| Google Drive | https://console.cloud.google.com/apis/library/drive.googleapis.com |
| Google Docs | https://console.cloud.google.com/apis/library/docs.googleapis.com |
| Google Sheets | https://console.cloud.google.com/apis/library/sheets.googleapis.com |
| Google Slides | https://console.cloud.google.com/apis/library/slides.googleapis.com |
| Google Calendar | https://console.cloud.google.com/apis/library/calendar-json.googleapis.com |
| Google Forms | https://console.cloud.google.com/apis/library/forms.googleapis.com |
| Google Tasks | https://console.cloud.google.com/apis/library/tasks.googleapis.com |
| Google Chat | https://console.cloud.google.com/apis/library/chat.googleapis.com |
| People (Contacts) | https://console.cloud.google.com/apis/library/people.googleapis.com |
| Custom Search | https://console.cloud.google.com/apis/library/customsearch.googleapis.com |
| Apps Script | https://console.cloud.google.com/apis/library/script.googleapis.com |

> **Note:** You can skip any tools you don't want (e.g. Custom Search, Apps Script, Chat) — but
> enabling all 12 gives you the full workspace and avoids "API not enabled" errors later.

✅ **Done when:** the user confirms the APIs they want are enabled.

---

## Phase 3 — Configure the OAuth consent screen

> This is the screen Google shows when your account authorizes the app. You only fill it out once.

> **TELL THE USER:**

1. Go to **https://console.cloud.google.com/auth/overview** (APIs & Services → OAuth consent screen).
2. **User type:**
   - Choose **Internal** *only if* you have Google Workspace **and** you're okay with only accounts in
     your own organization using it. Internal skips the review warnings and token expiry issue below.
   - Otherwise choose **External** (this is normal for personal Gmail).
3. Fill in **App name** (e.g. `Workspace MCP`), your email as **User support email**, and your email
   again under **Developer contact**. Skip logo/domains. Save through the steps.
4. **If you chose External:** on the **Audience** / OAuth consent screen page you'll see a
   **Publishing status**. Do **one** of these:
   - Add yourself as a **Test user** (Audience → Test users → add your email), **or**
   - Click **Publish app** → confirm.

> ⚠️ **IMPORTANT — the #1 thing that breaks this later:** If your app stays in **"Testing"** status,
> Google **expires your login every 7 days** and you'll have to re-authenticate weekly. To avoid that,
> click **"Publish app"** (Publishing status → In production). Since it's your own app used only by
> you, you can ignore the "unverified app" warning — when authorizing, click **Advanced → Go to
> {app name} (unsafe)**. That warning is expected and safe *for your own app*.

✅ **Done when:** the consent screen is configured and (recommended) published to production, or your
email is added as a test user.

---

## Phase 4 — Create the OAuth Client ID (get the keys)

> **TELL THE USER:**

1. Go to **https://console.cloud.google.com/auth/clients** (APIs & Services → Credentials).
2. **+ Create Credentials → OAuth client ID.**
3. **Application type: `Desktop app`.** (This is the simplest — no redirect URLs to configure.)
4. Name it `Workspace MCP` → **Create.**
5. A popup shows your **Client ID** and **Client Secret**. Click **Download JSON** (or copy both).

> **CLAUDE:** Do **not** ask the user to paste the secret into the chat. In the next phase you'll have
> them put it directly into the config file / command. Ask only for the **Client ID** in chat if you
> need it (the Client ID is not sensitive; the Client Secret is).

✅ **Done when:** the user has their **Client ID** and **Client Secret** in hand.

---

## Phase 5 — Connect the server to Claude

Use the path that matches the user's setup. Both run the exact same server:
`uvx workspace-mcp --single-user --tool-tier complete`.

> **About `--tool-tier`:** `complete` = the full workspace (recommended, matches "everything").
> Use `core` instead for a lighter footprint / lower API quota if the user prefers.

### Path A — Claude Code (CLI)

Run this in the terminal, substituting the user's real values. Keep the secret out of the chat — have
the user run it themselves, or read the values from the JSON they downloaded:

```bash
claude mcp add google-workspace \
  -e GOOGLE_OAUTH_CLIENT_ID="THEIR_CLIENT_ID" \
  -e GOOGLE_OAUTH_CLIENT_SECRET="THEIR_CLIENT_SECRET" \
  -e USER_GOOGLE_EMAIL="them@example.com" \
  -e OAUTHLIB_INSECURE_TRANSPORT="1" \
  -e WORKSPACE_MCP_PORT="8000" \
  -- uvx workspace-mcp --single-user --tool-tier complete
```

Then verify it registered:
```bash
claude mcp list
```
You should see `google-workspace`. Restart Claude Code (or `/mcp` reconnect) so it loads.

> **Scope tip:** add `-s user` to the `claude mcp add` command to make it available in every project,
> or `-s project` to share it via a committed `.mcp.json` (⚠️ never commit the secret — project scope
> writes env values into `.mcp.json`, so use user scope unless you templatize the secret).

### Path B — Claude Desktop (app)

1. Open the config file (create it if missing):
   - **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
   - **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`
2. Add this block (merge into `mcpServers` if the file already has one):

```json
{
  "mcpServers": {
    "google-workspace": {
      "command": "uvx",
      "args": ["workspace-mcp", "--single-user", "--tool-tier", "complete"],
      "env": {
        "GOOGLE_OAUTH_CLIENT_ID": "THEIR_CLIENT_ID",
        "GOOGLE_OAUTH_CLIENT_SECRET": "THEIR_CLIENT_SECRET",
        "USER_GOOGLE_EMAIL": "them@example.com",
        "OAUTHLIB_INSECURE_TRANSPORT": "1",
        "WORKSPACE_MCP_PORT": "8000"
      }
    }
  }
}
```

3. **Fully quit and reopen Claude Desktop** (not just close the window).

> **Where do the login tokens get stored?** By default in `~/.google_workspace_mcp/credentials/`.
> These files grant access to the user's Google account — keep them private, never commit them,
> never share them.

✅ **Done when:** Claude shows the `google-workspace` server connected (Claude Code: `claude mcp list`
shows it ✔; Desktop: it appears in the tools/connectors list).

---

## Phase 6 — Authenticate & verify

1. In Claude, tell it: **"Authenticate my Google account for the workspace MCP."**
   - **CLAUDE:** call the `start_google_auth` tool with the user's `user_google_email`. A browser
     window will open to Google's sign-in/consent page. (If the popup lists the app as unverified,
     that's the expected "your own app" warning — **Advanced → Go to … (unsafe)** → continue.)
   - The user picks their account and clicks **Allow** on the permission screens.
2. When the browser says it succeeded, come back to Claude and **run a harmless read to confirm**, e.g.:
   - *"List my Gmail labels"* or *"List my Google calendars."*
   - **CLAUDE:** call a read-only tool (e.g. `list_gmail_labels` or `list_calendars`) and report the result.

✅ **Setup complete when:** a read-only Google tool returns real data. 🎉

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| **`uvx: command not found`** | `uv` isn't installed or not on PATH. Redo Phase 0 and re-open the terminal. |
| **Login works but breaks every ~7 days** | Your OAuth consent screen is in **Testing** mode. Fix in Phase 3: **Publish app** (Publishing status → In production). |
| **`Error 403: access_denied` during sign-in** | Your email isn't a Test user and the app isn't published. Add yourself as a Test user *or* publish the app (Phase 3). |
| **`Error 400: redirect_uri_mismatch`** | You created a **Web application** client instead of **Desktop app**. Delete it and make a **Desktop app** client (Phase 4). |
| **"This app isn't verified" warning** | Expected for your own unpublished app. Click **Advanced → Go to {app} (unsafe)** → continue. |
| **`API has not been enabled` / 403 on a specific tool** | That API isn't turned on. Enable it (Phase 2). |
| **Port already in use / callback fails** | Change `WORKSPACE_MCP_PORT` to another value (e.g. `8010`) in the config and restart. |
| **Server won't connect in Claude Desktop** | You edited the JSON wrong (trailing comma / bad merge) or didn't fully quit the app. Validate the JSON and restart. |
| **Google Chat tools error** | Chat needs its extra **Configuration** tab filled in (Chat API → Configuration: app name, avatar URL, description). Optional unless you use Chat. |

---

## Security notes (read once)

- Your **Client Secret** and the **token files** in `~/.google_workspace_mcp/credentials/` are the
  keys to your Google account. **Never** commit them to git, paste them in public chats, or share them.
- This server runs **locally on your machine**. Your data goes between your computer and Google's
  official APIs — nothing routes through a third party.
- To revoke access at any time: **https://myaccount.google.com/permissions** → remove the app; and/or
  delete the OAuth client in Google Cloud Console.

---

*Built on the open-source [`taylorwilsdon/google_workspace_mcp`](https://github.com/taylorwilsdon/google_workspace_mcp). Verify commands there if the tool changes.*
