# Ready-to-copy skill add-on

This is the no-thinking version.

If you use Codex, copy the packaged folder `ai-ready-bootstrap-installer/` into your local skills directory and use it from there.

## Install it in Codex

From the root of this repository:

```bash
mkdir -p "$HOME/.codex/skills"
rm -rf "$HOME/.codex/skills/ai-ready-bootstrap-installer"
cp -R assistant-installer/addon/ai-ready-bootstrap-installer "$HOME/.codex/skills/"
```

That should leave you with:

```text
~/.codex/skills/ai-ready-bootstrap-installer/
└── SKILL.md
```

## Use it

1. Restart Codex or open a new session.
2. Open the repository you want to bootstrap.
3. Ask:

```text
Use the ai-ready-bootstrap-installer skill in this repository.
```

If you already know the target runtimes, say for example:

```text
Use the ai-ready-bootstrap-installer skill in this repository. Install Codex + Claude + generic adapters.
```

## What this add-on does

- audits the repository first
- decides whether this is a fresh install or a standardization pass
- creates the canonical `.ai/` layer
- fills `.ai/context*` from the real repository instead of leaving generic placeholders
- creates only the runtime adapters you ask for

## If it does not work

- Make sure the folder ended up under `~/.codex/skills/ai-ready-bootstrap-installer/`.
- Make sure the file is named exactly `SKILL.md`.
- Start a new Codex session after copying it.
- If your AI does not support installed skills, use [../PROMPT.md](../PROMPT.md) instead.
