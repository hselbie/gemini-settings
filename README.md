# ~/.gemini

Git-controlled [Gemini CLI](https://github.com/google-gemini/gemini-cli) user settings. Clone this repo as `~/.gemini` and everything just works.

## Setup

```bash
# Back up existing settings if you have any
mv ~/.gemini ~/.gemini.bak

# Clone as your ~/.gemini directory
git clone https://github.com/hselbie/gemini-settings.git ~/.gemini
```

That's it. Gemini CLI reads `~/.gemini/` automatically for user-level settings, policies, commands, skills, and context.

### Per-Project Setup

Copy the project-level files into any repo you work on:

```bash
cp ~/.gemini/project-template/GEMINI.md /path/to/project/GEMINI.md
cp ~/.gemini/project-template/.geminiignore /path/to/project/.geminiignore
```

## What's Here

```
~/.gemini/
├── GEMINI.md                ─ Global agent instructions (always active)
├── settings.json            ─ CLI configuration
├── policies/                ─ Tool approval rules
├── commands/                ─ Custom slash commands
├── skills/                  ─ On-demand agent expertise
└── project-template/        ─ Files to copy into individual projects
    ├── GEMINI.md            ─ Per-project agent instructions
    └── .geminiignore        ─ Context filtering
```

### `GEMINI.md` — Agent Behavior

Global instructions shaping how Gemini behaves across all projects:

- **Ask, don't guess** — Asks focused questions instead of presenting option menus
- **Discuss problems, not solutions** — Engages in dialogue before jumping to fixes
- **Self-reflection** — Reviews its own work, catches circular reasoning, acknowledges mistakes
- **Surgical precision** — Changes only what was asked, preserves everything else
- **Operational discipline** — Breaks infinite loops, fixes root causes not symptoms

### `settings.json` — CLI Config

- Checkpointing enabled for session recovery
- Plan mode with model routing (Pro for planning, Flash for implementation)
- Inline thinking visible (see the model's reasoning)
- Context percentage shown in footer
- Usage statistics disabled

### `policies/` — Tool Approval Rules

| File | What It Does |
|------|--------------|
| `auto-approve-readonly.toml` | Auto-approves read-only tools (`read_file`, `glob`, `grep`, `cat`, `ls`, `git status/log/diff/blame`, etc.) |
| `safety-guardrails.toml` | Blocks `rm -rf /`, system `chmod/chown`; requires confirmation for `git push`, `sudo`, `rm` |
| `build-and-test.toml` | Auto-approves `npm test/run`, `go test/build`, `cargo test/build`, `make`, `pytest`, etc. |

### `commands/` — Slash Commands

| Command | Description |
|---------|-------------|
| `/review` | Self-review recent changes for bugs, edge cases, style issues |
| `/reflect` | Session retrospective — what was done, decisions made, open questions |
| `/explain <target>` | Deep explanation of a file or code section |
| `/plan <task>` | Create an implementation plan before writing code |

### `skills/` — Agent Skills

On-demand expertise that Gemini activates when it matches a task. Not loaded until needed.

**General-Purpose:**

| Skill | Persona | When It Activates |
|-------|---------|-------------------|
| `amelia-tdd` | TDD Mentor | Tests first, RED→GREEN→REFACTOR cycles, adding coverage |
| `barry-quickdev` | Rapid Developer | MVPs, quick features, bug fixes, shipping fast |
| `winston-arch` | System Architect | Architecture decisions, trade-off analysis, ADRs, diagrams |

**ADK** (Google Agent Development Kit):

| Skill | Purpose |
|-------|---------|
| `adk-cheatsheet` | Python API reference for ADK |
| `adk-dev-guide` | Development lifecycle and coding guidelines |
| `adk-eval-guide` | Evaluation methodology and common gotchas |
| `adk-deploy-guide` | Production deployment, CI/CD, Terraform |
| `adk-scaffold` | Project scaffolding with Agent Starter Pack |

## Keeping Settings in Sync

```bash
# After making changes in ~/.gemini
cd ~/.gemini
git add -p
git commit -m "describe your change"
git push

# On another machine
cd ~/.gemini
git pull
```

## Customization

- **`GEMINI.md`** — Adjust communication style, add personal conventions
- **`settings.json`** — Change model, thinking visibility, UI preferences
- **`policies/`** — Add/remove auto-approved commands for your stack
- **`commands/`** — Add shortcuts for your workflows
- **`skills/`** — Add your own, remove ADK skills if you don't use ADK

## Skills Attribution

Skills adapted from [hselbie/agent-skills](https://github.com/hselbie/agent-skills) with edits:
- Improved descriptions for better auto-activation matching
- Fixed model name contradictions (`gemini-2.0-flash` → `gemini-3-flash-preview`)
- Fixed phantom persona references (Murat, Paige, Sam → actual skill names)
- Added "execute tests, don't simulate" to amelia-tdd
- Extracted operational guidelines from adk-dev-guide into GEMINI.md
- Removed `mcp-server: adk-mcp` metadata

## Reference

- [Gemini CLI Configuration](https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/configuration.md)
- [Policy Engine](https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/policy-engine.md)
- [GEMINI.md Context Files](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/gemini-md.md)
- [Custom Commands](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/custom-commands.md)
- [Agent Skills](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/skills.md)
- [Agent Skills Spec](https://agentskills.io)
