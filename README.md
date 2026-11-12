# Portsari

The doorman of #pulina. It judges the channel against [the rules](https://www.pulina.fi/saannot), and when it decides a break calls for action, escalates per person: warning, kick, knockout (kick + 5 min ban), then a permanent ban. Strikes are kept by host in `strikes.json` and forgotten after 30 days. It also tells every op in a short private message what it did, when and why. It never answers anyone.

```
#pulina ──> Portsari (Sopel, botit.pulina.fi)
             ├─ slur word list ── hit ──> next step on the ladder + private message to every op + critical alert
             ├─ every 10 min, if there are new lines:
             │     rules page + last 300 lines ──> claude -p (no tools, empty home, fixed JSON answer)
             │     act or not + Finnish message ──> next step on the ladder, then private message to every op
             │     high ──> also a critical alert
             └─ private messages to Portsari: never read
```

Ops and half-ops are off limits: Portsari never judges, reports or kicks them. It never kicks bots either, and needs op itself to act. Claude's text reaches the channel only as the warning or kick reason: one line, at most 120 characters, email addresses removed.

## Install on IRC bot server

1. Install Claude Code for `rolle`: `curl -fsSL https://claude.ai/install.sh | bash`
2. On a machine logged in to the Claude subscription, run `claude setup-token` and put the token in `/home/rolle/portsari/.env` as `CLAUDE_CODE_OAUTH_TOKEN` (see `.env.example`).
3. Copy `portsari.cfg` and `plugins/` to `/home/rolle/portsari/`.
4. `sudo cp portsari.service /etc/systemd/system/ && sudo systemctl daemon-reload && sudo systemctl enable --now portsari`

Self-check: `python3 plugins/portsari.py` inside the Sopel virtualenv.
