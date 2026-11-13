# Portsari

The doorman of #pulina. It judges the channel against [the rules](https://www.pulina.fi/saannot), and when it decides a break calls for action, escalates per person: warning, kick, knockout (kick + 5 min ban), then a permanent ban. Strikes are kept by host in `strikes.json` and forgotten after 30 days. It also tells every op in a short private message what it did, when and why, and sends private notes about harmful things and bad vibes that are not rule breaks to a short list of readers, each checked by nick and exact ident@host before every message. It never answers anyone.

```
#pulina ──> Portsari (Sopel, botit.pulina.fi)
             ├─ slur and hate-slogan list ── hit ──> an early Claude check of the context (the word alone never decides)
             ├─ every 10 min, the lines of the day log not judged yet (position saved in state.json, so restarts lose nothing):
             │     rules page + new lines ──> claude -p (reads only the channel logs since 2008, empty home, fixed JSON answer)
             │     act or not + Finnish message ──> next step on the ladder, then private message to every op
             │     high ──> also a critical alert
             │     notes ──> private message to the verified readers + notes.log, no alert, no action, never about ops
             └─ private messages to Portsari: never read
```

Ops and half-ops are off limits: Portsari never judges, reports, notes or kicks them. It never acts on bots either, and needs op itself to act. Every report to the ops is also kept in `actions.log`. Claude's text reaches the channel only as the warning or kick reason: one line, at most 120 characters, email addresses removed.

## Install on IRC bot server

1. Install Claude Code for `rolle`: `curl -fsSL https://claude.ai/install.sh | bash`
2. On a machine logged in to the Claude subscription, run `claude setup-token` and put the token in `/home/rolle/portsari/.env` as `CLAUDE_CODE_OAUTH_TOKEN` (see `.env.example`).
3. Clone this repo to `/home/rolle/portsari/`. The channel logs must be readable at `~/pulina.fi/pulina-days` and `~/pulina.fi/pul` (on the bot server an sshfs mount of the website).
4. `sudo cp portsari.service /etc/systemd/system/ && sudo systemctl daemon-reload && sudo systemctl enable --now portsari`

Self-check: `python3 plugins/portsari.py` inside the Sopel virtualenv.
