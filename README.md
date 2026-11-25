# Portsari

The doorman of #pulina. It judges the channel against [the rules](https://www.pulina.fi/saannot), and when it decides a break calls for action, escalates per person: warning, kick, knockout (kick + 5 min ban), then a permanent ban. Words with no innocent meaning (slurs, Nazi slogans) are kicked in code the moment they are said, without asking Claude, and continue the same ladder. Ambiguous words and English (rule 3: Finnish only) skip the warning and start at the kick once Claude confirms, English only from someone who has never written Finnish on that nick or address (`finns.json`, built once from the logs since 2008) and is not on a .fi address; the warning is then said when they rejoin, with the next step named. Strikes are kept by host in `strikes.json` and forgotten after 30 days. It also tells every op in a short private message what it did, when and why, and sends private notes about harmful things and bad vibes that are not rule breaks to a short list of readers, each checked by nick and exact ident@host before every message. It never answers anyone.

```
#pulina ──> Portsari (Sopel, botit.pulina.fi)
             ├─ JOIN: Madeleine spam bot (nick, ident and address pattern, never spoken since 2008) ──> kick
             ├─ JOIN: girl photo spam bot (95.70.0.0/16, no identd, never spoken) ──> kick
             │        anyone else, no strike, channel not +m ──> voice
             ├─ the Madeleine bot's opening line from a nick that has never spoken ──> kick before the rest follows
             ├─ kick word (slur, Nazi slogan) ── hit ──> kicked at once, no Claude
             ├─ ambiguous slur word, or a line that looks English ── hit ──> an early Claude check of the context
             ├─ every minute, if anyone spoke: Haiku reads the new talk ── heating up ──> the full check now (at most every 2 min)
             ├─ the full check flagged people as heating up ──> the full check again in 2 min, told whom it was watching
             ├─ every 10 min, the lines of the day log not judged yet (position saved in state.json, so restarts lose nothing):
             │     rules page + new lines + its notes and reports on the speakers from the last 14 days ──> claude -p (reads only the channel logs since 2008, empty home, fixed JSON answer)
             │     act or not + Finnish message ──> next step on the ladder, one message naming everyone, then private message to every op
             │     calm ──> a gentle word to everyone involved, no strike, once per 30 min for the same people
             │     22-07 ──> told the ops are asleep, so it calms or acts itself instead of leaving a note
             │     high ──> also a critical alert with Claude's reason
             │     notes ──> private message to the verified readers + notes.log, no alert, no action, never about ops
             └─ private messages to Portsari: never read
```

The spam check matched 150 bot visits and no real person in the 39 777 joins since 2024. It needs the speaker list (`speakers.json`, built once from the monthly logs and kept up to date) and kicks a host only once, so a mistake mends itself on the next join.

Ops and half-ops are off limits: Portsari never judges, reports, notes or kicks them. It never acts on bots either, and needs op itself to act. Every report to the ops is also kept in `actions.log`. Claude's text reaches the channel only as the warning or kick reason: one line, at most 120 characters, email addresses removed.

## Install on IRC bot server

1. Install Claude Code for `rolle`: `curl -fsSL https://claude.ai/install.sh | bash`
2. On a machine logged in to the Claude subscription, run `claude setup-token` and put the token in `/home/rolle/portsari/.env` as `CLAUDE_CODE_OAUTH_TOKEN` (see `.env.example`).
3. Clone this repo to `/home/rolle/portsari/`. The channel logs must be readable at `~/pulina.fi/pulina-days` and `~/pulina.fi/pul` (on the bot server an sshfs mount of the website).
4. `sudo cp portsari.service /etc/systemd/system/ && sudo systemctl daemon-reload && sudo systemctl enable --now portsari`

Self-check: `python3 plugins/portsari.py` inside the Sopel virtualenv.
