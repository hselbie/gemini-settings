# Gemini CLI Settings

Opinionated configuration for a great [Gemini CLI](https://github.com/google-gemini/gemini-cli) experience.

## What's Included

### `GEMINI.md` — Agent Behavior Instructions

System prompt that shapes how Gemini behaves:

- **Ask, don't guess** — When something is ambiguous, the agent asks a focused question instead of presenting a menu of options
- **Discuss problems, not solutions** — Describes what's tricky and engages in dialogue before jumping to fixes
- **Self-reflection** — Reviews its own work, catches circular reasoning, and acknowledges mistakes
- **Minimal changes** — Makes the smallest correct change without refactoring unrelated code
- **Honesty** — Says "I don't know" when uncertain

### `.gemini/settings.json` — CLI Configuration

- Checkpointing enabled for session recovery
- Plan mode with model routing (Pro for planning, Flash for implementation)
- Inline thinking visible (see the model's reasoning)
- Context percentage shown in footer
- Usage statistics disabled

### `.gemini/policies/` — Tool Approval Rules

**`auto-approve-readonly.toml`** — Auto-approves read-only operations:
- File reading tools: `read_file`, `glob`, `grep`, `list_directory`, `read_many_files`
- Web tools: `web_search`, `web_fetch`
- Read-only shell commands: `cat`, `ls`, `find`, `head`, `tail`, `grep`, `rg`, `tree`, etc.
- Read-only git commands: `status`, `log`, `diff`, `show`, `branch`, `blame`, etc.

**`safety-guardrails.toml`** — Blocks dangerous operations:
- Denies `rm -rf /`, `rm -rf ~`, `rm -rf *`
- Denies permission changes on system directories
- Requires confirmation for: `git push`, force flags, `sudo`, `rm`, piped curl/wget

**`build-and-test.toml`** — Auto-approves build/test commands:
- `npm test/run`, `go test/build/run`, `cargo test/build/run`, `make`, `pytest`, `tsc`, `eslint`, `prettier`
- Requires confirmation for `npm install` and `npx` (they can modify the system)

### `.gemini/commands/` — Custom Commands

| Command | Description |
|---------|-------------|
| `/review` | Self-review recent changes for bugs, edge cases, style issues |
| `/reflect` | Session retrospective — what was done, decisions made, open questions |
| `/explain <target>` | Deep explanation of a file or code section |
| `/plan <task>` | Create a detailed implementation plan before writing code |

### `.gemini/skills/` — Agent Skills

On-demand expertise that Gemini activates when it matches a task. Skills are not loaded into context until needed, keeping the context window clean.

**General-Purpose Skills:**

| Skill | Persona | When It Activates |
|-------|---------|-------------------|
| `amelia-tdd` | Amelia — TDD Mentor | Writing tests first, RED→GREEN→REFACTOR cycles, adding test coverage |
| `barry-quickdev` | Barry — Rapid Developer | MVPs, quick features, bug fixes, shipping fast |
| `winston-arch` | Winston — System Architect | Architecture decisions, trade-off analysis, ADRs, system design |

**ADK Skills** (for Google Agent Development Kit projects):

| Skill | Purpose |
|-------|---------|
| `adk-cheatsheet` | Python API reference for ADK agents, tools, orchestration |
| `adk-dev-guide` | Development lifecycle, operational guidelines, coding discipline |
| `adk-eval-guide` | Evaluation methodology, eval config schemas, common gotchas |
| `adk-deploy-guide` | Production deployment, CI/CD, Terraform, Agent Engine |
| `adk-scaffold` | Project scaffolding with Agent Starter Pack CLI |

### `.geminiignore` — Context Filtering

Excludes noise from the model's context: dependencies, build outputs, binaries, lock files, logs.

## Usage

### As a project template

Copy the files into your project:

```bash
# Copy everything
cp -r GEMINI.md .gemini/ .geminiignore /path/to/your/project/
```

Or link skills into Gemini CLI directly:

```bash
gemini skills link /path/to/gemini-settings/.gemini/skills
```

### As global user settings

Copy to your home directory for all projects:

```bash
# Global instructions
cp GEMINI.md ~/.gemini/GEMINI.md

# Global settings (merge with existing if you have one)
cp .gemini/settings.json ~/.gemini/settings.json

# Global policies
cp -r .gemini/policies/ ~/.gemini/policies/

# Global commands
cp -r .gemini/commands/ ~/.gemini/commands/

# Global skills
cp -r .gemini/skills/ ~/.gemini/skills/
# Or use: gemini skills link /path/to/gemini-settings/.gemini/skills
```

### Mix and match

Every file is independent. Take what you want, leave what you don't.

## Customization

Edit any file to match your preferences. Key things you might want to change:

- **`GEMINI.md`** — Adjust the communication style, add project-specific conventions
- **`settings.json`** — Change the model, adjust thinking visibility, tweak UI
- **Policies** — Add/remove auto-approved commands for your stack
- **Commands** — Add shortcuts for your common workflows
- **Skills** — Remove ADK skills if you don't use ADK, add your own expertise skills

## Skills Attribution

The skills in `.gemini/skills/` are adapted from [hselbie/agent-skills](https://github.com/hselbie/agent-skills) with the following edits:
- Improved skill descriptions for better auto-activation matching
- Fixed model name contradictions (`gemini-2.0-flash` → `gemini-3-flash-preview` in adk-cheatsheet)
- Fixed phantom persona references (Murat, Paige, Sam → actual skill names)
- Added "execute tests, don't simulate" principle to amelia-tdd
- Extracted operational guidelines from adk-dev-guide into GEMINI.md
- Removed `mcp-server: adk-mcp` metadata (not bundled)

## Reference

- [Gemini CLI Configuration Docs](https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/configuration.md)
- [Policy Engine Docs](https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/policy-engine.md)
- [GEMINI.md Context Files](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/gemini-md.md)
- [Custom Commands](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/custom-commands.md)
- [Agent Skills](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/skills.md)
- [Agent Skills Spec](https://agentskills.io)
