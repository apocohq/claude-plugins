# MQTT

Connect an MQTT broker to your Claude Code session via an MCP server.

The MCP server connects to an MQTT broker, subscribes to topics, and forwards incoming messages to Claude Code as channel notifications. Claude can publish back to topics using the `publish` tool.

## Prerequisites

- [Bun](https://bun.sh) — the MCP server runs on Bun. Install with `curl -fsSL https://bun.sh/install | bash`.
- An MQTT broker (e.g., [Mosquitto](https://mosquitto.org/), [EMQX](https://www.emqx.io/), or any cloud MQTT service).

## Quick Setup

**1. Install the plugin.**

These are Claude Code commands — run `claude` to start a session first.

```
/plugin install mqtt@apoco-plugins
```

**2. Configure the broker URL.**

```
/mqtt:configure mqtt://localhost:1883
```

Writes `MQTT_BROKER_URL=...` to `~/.claude/channels/mqtt/.env`. You can also write that file by hand, or set the variable in your shell environment — shell takes precedence.

For TLS connections use `mqtts://`:
```
/mqtt:configure mqtts://broker.example.com:8883
```

**3. (Optional) Set authentication.**

If your broker requires credentials:
```
/mqtt:configure auth myuser mypassword
```

**4. Relaunch with the channel flag.**

The server won't connect without this — exit your session and start a new one:

```sh
claude --channels plugin:mqtt@apoco-plugins
```

**5. Subscribe to topics.**

```
/mqtt:access subscribe add sensors/#
/mqtt:access subscribe add home/+/status
```

Messages on matching topics now arrive in your Claude Code session.

**6. Publish.**

Claude can use the `publish` tool to send messages to any topic allowed by the publish allowlist.

## Access Control

See **[ACCESS.md](./ACCESS.md)** for topic allowlists, subscription management, QoS settings, and the `config.json` schema.

Quick reference: by default, all topics are allowed for both publish and subscribe (`#` wildcard). Use `/mqtt:access` to restrict.

## Tools exposed to the assistant

| Tool | Purpose |
| --- | --- |
| `publish` | Publish a message to an MQTT topic. Takes `topic` + `payload`, optionally `qos` (0/1/2) and `retain` (boolean). Checks the publish allowlist before sending. |
| `subscribe` | Subscribe to a topic filter at runtime. Supports `+` and `#` wildcards. Checks the subscribe allowlist. |
| `unsubscribe` | Unsubscribe from a topic filter. |

## Inbound messages

Messages arrive as channel notifications:

```
<channel source="mqtt" topic="sensors/temp" qos="0" ts="2026-03-22T10:30:00Z">23.5</channel>
```

Retained messages include a `retained="true"` attribute.

## Environment variables

| Variable | Required | Description |
| --- | --- | --- |
| `MQTT_BROKER_URL` | Yes | Broker URL (`mqtt://` or `mqtts://`) |
| `MQTT_USERNAME` | No | Authentication username |
| `MQTT_PASSWORD` | No | Authentication password |
| `MQTT_CLIENT_ID` | No | Client ID prefix (default: `claude-mqtt`) |
| `MQTT_STATE_DIR` | No | Override state directory (default: `~/.claude/channels/mqtt/`) |

## Payloads

MQTT payloads are converted to UTF-8 strings. Binary payloads (e.g., Protobuf, CBOR) will appear garbled — decode them in your pipeline before publishing, or use a text format like JSON.
