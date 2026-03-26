---
name: uninstall
description: Uninstall Clawdboard and optionally clean up IDE integrations.
---

# /clawdboard:uninstall

Remove Clawdboard and its configuration. Hooks are deregistered automatically when the plugin is removed.

Arguments passed: `$ARGUMENTS`

## Tool guidance

Minimize approval prompts. Use the Read and Write tools for file operations. Only use Bash when shell execution is truly required. Combine multiple shell checks into a single Bash call.

---

## Step 1 — Uninstall Clawdboard

Check how Clawdboard was installed:

```bash
echo "brew_cask:$(brew list --cask 2>/dev/null | grep -q clawdboard && echo yes || echo no)"
```

Quit the app first: `osascript -e 'quit app "Clawdboard"' 2>/dev/null`

If `brew_cask` is `yes`, run: `brew uninstall --cask clawdboard`

Otherwise, remove the app directly: `rm -rf /Applications/Clawdboard.app ~/Applications/Clawdboard.app`

Then remove the configuration directory: `rm -rf ~/.clawdboard/`

## Step 2 — IDE cleanup (optional)

Ask the user if they want to clean up IDE integrations. If no, skip to the summary.

If yes, run a single Bash command to check what's present:

```bash
echo "iterm_script:$(ls ~/.config/iterm2/AppSupport/Scripts/AutoLaunch/clawdboard.py 2>/dev/null)"
echo "vscode_tabs:$(grep -l nativeTabs ~/Library/Application\ Support/Code/User/settings.json 2>/dev/null)"
```

### iTerm2

- If `clawdboard.py` exists, delete it: `rm ~/.config/iterm2/AppSupport/Scripts/AutoLaunch/clawdboard.py`
- Suggest restarting iTerm2.

### VS Code

- If `"window.nativeTabs"` is set, tell the user they can remove it manually if they don't want it. Don't auto-remove — they may want it independently of Clawdboard.

## Step 3 — Summary

Print what was removed and any manual steps remaining.
