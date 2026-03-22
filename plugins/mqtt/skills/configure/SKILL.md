---
name: configure
description: Set up the MQTT channel — save the broker URL, credentials, and review connection status. Use when the user provides an MQTT broker URL, asks to configure MQTT, asks "how do I set this up", or wants to check channel status.
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Bash(ls *)
  - Bash(mkdir *)
  - Bash(chmod *)
---

# /mqtt:configure — MQTT Channel Setup

Writes connection settings to `~/.claude/channels/mqtt/.env` and orients the
user on subscription and access policy. The server reads `.env` at boot.

Arguments passed: `$ARGUMENTS`

---

## Dispatch on arguments

### No args — status and guidance

Read the state files and give the user a complete picture:

1. **Broker URL** — check `~/.claude/channels/mqtt/.env` for
   `MQTT_BROKER_URL`. Show set/not-set; if set, show the URL with password
   masked if embedded in the URL.

2. **Auth** — check for `MQTT_USERNAME` in `.env`. Show set/not-set.
   Never display the password.

3. **Client ID** — check for `MQTT_CLIENT_ID` in `.env`. Show if set,
   otherwise mention the default prefix `claude-mqtt`.

4. **Subscriptions** — read `~/.claude/channels/mqtt/config.json` if it
   exists. Show subscription count and list topics. Show allowlist state.

5. **What next** — end with a concrete next step based on state:
   - No broker URL → *"Run `/mqtt:configure <broker-url>` with your MQTT
     broker URL (e.g., `mqtt://localhost:1883`)."*
   - URL set, no subscriptions → *"Add subscriptions with
     `/mqtt:access subscribe add <topic>`."*
   - URL set, subscriptions present → *"Ready. Launch with
     `claude --channels plugin:mqtt@apoco-plugins` to start
     receiving messages."*

### `<broker-url>` — save it

Detect this case when `$ARGUMENTS` starts with `mqtt://` or `mqtts://`.

1. Treat `$ARGUMENTS` as the broker URL (trim whitespace). Validate it
   starts with `mqtt://` or `mqtts://`.
2. `mkdir -p ~/.claude/channels/mqtt`
3. Read existing `.env` if present; update/add the `MQTT_BROKER_URL=` line,
   preserve other keys. Write back, no quotes around the value.
4. `chmod 600 ~/.claude/channels/mqtt/.env` — the URL may contain credentials.
5. Confirm, then show the no-args status so the user sees where they stand.

### `auth <username> <password>` — save credentials

1. Parse username and password from `$ARGUMENTS` (after "auth").
2. `mkdir -p ~/.claude/channels/mqtt`
3. Read existing `.env` if present; update/add `MQTT_USERNAME=` and
   `MQTT_PASSWORD=` lines, preserve other keys. Write back.
4. `chmod 600 ~/.claude/channels/mqtt/.env`
5. Confirm credentials saved. Remind user to restart session.

### `client-id <prefix>` — set client ID prefix

1. Parse the prefix from `$ARGUMENTS` (after "client-id").
2. `mkdir -p ~/.claude/channels/mqtt`
3. Read existing `.env`; update/add `MQTT_CLIENT_ID=` line. Write back.
4. `chmod 600 ~/.claude/channels/mqtt/.env`
5. Confirm.

### `clear` — remove credentials

Delete `MQTT_BROKER_URL=`, `MQTT_USERNAME=`, `MQTT_PASSWORD=`, and
`MQTT_CLIENT_ID=` lines from `.env` (or the file if those are the only lines).

---

## Implementation notes

- The channels dir might not exist if the server hasn't run yet. Missing file
  = not configured, not an error.
- The server reads `.env` once at boot. Changes need a session restart
  or `/reload-plugins`. Say so after saving.
- `config.json` is re-read on every tool call — subscription/allowlist changes
  via `/mqtt:access` take effect immediately, no restart.
