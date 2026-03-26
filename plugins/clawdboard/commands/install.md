---
name: install
description: Install Clawdboard via Homebrew (or build from source) and optionally set up IDE integration.
---

# /clawdboard:install

Install Clawdboard, a macOS menu bar app that monitors Claude Code sessions.
Hooks are registered automatically by the plugin — no manual configuration needed.

Arguments passed: `$ARGUMENTS`

## Tool guidance

Minimize approval prompts. Use the Read and Write tools for file operations. Only use Bash when shell execution is truly required. Combine multiple shell checks into a single Bash call.

---

## Step 1 — Install Clawdboard

Check if the app is already installed:

```bash
echo "app:$(ls -d /Applications/Clawdboard.app ~/Applications/Clawdboard.app 2>/dev/null | head -1)"
```

If the app is already installed, tell the user and ask if they want to upgrade/reinstall. If they decline, skip to Step 2.

If the app is not installed (or the user wants to upgrade), detect the install method:

```bash
echo "source_repo:$(grep -q 'name: "Clawdboard"' ./Package.swift 2>/dev/null && echo yes || echo no)" && echo "brew:$(which brew 2>/dev/null)" && echo "mise:$(which mise 2>/dev/null)"
```

### Source repo detected

If `source_repo` is `yes`, ask the user whether they want to **build from source** or **install via Homebrew**.

**Build from source:**

1. If `mise` is available, run: `mise run setup`
2. Run: `./scripts/bundle.sh`
3. Open the built app.

Then skip the Homebrew path below.

### Homebrew (default)

If not in the source repo, or the user chose Homebrew:

1. If `brew` is not available, tell the user to install Homebrew from https://brew.sh and stop.
2. Run: `brew install --cask apocohq/clawdboard/clawdboard && open /Applications/Clawdboard.app`

## Step 2 — IDE integration (optional)

Ask the user if they want to set up IDE integration (for "Focus" buttons that jump to the right terminal/editor window). If they say no, skip to the summary.

If yes, run a single Bash command to detect what's available:

```bash
echo "iterm:$(ls -d /Applications/iTerm.app 2>/dev/null)"
echo "iterm_api:$(defaults read com.googlecode.iterm2 EnableAPIServer 2>/dev/null)"
echo "iterm_script:$(ls ~/.config/iterm2/AppSupport/Scripts/AutoLaunch/clawdboard.py 2>/dev/null)"
echo "iterm_focus:$(ls ~/.clawdboard/iterm2-focus.py 2>/dev/null)"
echo "vscode:$(ls -d '/Applications/Visual Studio Code.app' 2>/dev/null)"
echo "vscode_cli:$(which code 2>/dev/null)"
```

Ask which integration(s) to set up. Only offer IDEs that are installed.

### iTerm2

- If Python API is not enabled, tell the user: **iTerm2 → Settings → General → Magic → Enable Python API**.
- If integration scripts are missing, suggest: `brew reinstall --cask apocohq/clawdboard/clawdboard`.
- Suggest restarting iTerm2 if it's running.

### VS Code

- If `code` CLI is missing, tell the user: **Cmd+Shift+P → "Shell Command: Install 'code' command in PATH"**.
- Read `~/Library/Application Support/Code/User/settings.json` with the Read tool. If `"window.nativeTabs"` is not `true`, merge it in using the Write tool. Tell user to restart VS Code.

## Step 3 — Summary

Print what was done and any manual steps still needed. Note that existing Claude Code sessions must be restarted to pick up the new hooks.
