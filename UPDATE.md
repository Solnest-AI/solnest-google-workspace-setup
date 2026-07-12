# UPDATE — Google Workspace MCP Setup (Claude reads this)

**You are Claude. The user asked you to update this guide. This one is easy — a guide is just a document, so there's no install to migrate and nothing of theirs to lose.**

## Step 1 — Check the version

Compare `VERSION` in this repo to the copy the user has. Same? Tell them they're current and stop.

## Step 2 — Get the latest

```bash
git clone --depth 1 https://github.com/Solnest-AI/solnest-google-workspace-setup.git /tmp/solnest-google-workspace-setup-update
```

No `git`? Download `https://github.com/Solnest-AI/solnest-google-workspace-setup/archive/refs/heads/main.zip`.

## Step 3 — Replace their copy

Overwrite their `GUIDE.md`, `README.md`, and `VERSION` with the fresh ones.

**Do not undo anything they already set up.** This guide walks through a one-time setup — if they've already done it, a newer guide does **not** mean they have to redo it. Read `CHANGELOG.md`, tell them what actually changed, and only walk them through a step again if that specific step changed.

## Step 4 — Tell them what changed

Two or three plain bullets from `CHANGELOG.md`, new version only.

> Updated to `X.Y.Z`. Nothing you already set up was touched — here's just what's new.
