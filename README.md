<!-- Generated from the skill built into the aggrega binary, whose source is github.com/aggrega-ai/aggrega-cli. "aggrega skills install" replaces a copy carrying this line, so keep your own changes in a skill of another name. -->

# Aggrega skills

Skills that teach an AI assistant to install and use [Aggrega](https://aggrega.ai/docs/), the
ahead-of-time compiler that turns declarative Aggregate, Component, Recipe, Manifest and Release
files into provider-native artifacts. Each skill follows the open
[Agent Skills](https://agentskills.io/specification) format, so any assistant that reads that
format can load it.

| Skill | What it does |
| --- | --- |
| [`aggrega`](skills/aggrega/SKILL.md) | Work with Aggrega, the ahead-of-time compiler that turns declarative Aggregate, Component, Recipe, Manifest and Release files into provider-native artifacts for targets such as Helm, Docker Compose and Terraform. Use this skill when the user asks to install Aggrega, connect the aggrega MCP server, start or work in an Aggrega workspace, write or edit an Aggrega entity file, validate or build a Release, find a catalog entity, or understand an Aggrega error. It carries no schema of its own. It sends every structural question to the aggrega binary, through its MCP tools when they are connected and through the aggrega command line otherwise, and every conceptual question to the Aggrega documentation. |

## Install the plugin

This repository is also a plugin for the assistants that install plugins. Installing it adds the
`aggrega` skill and connects the assistant to the `aggrega` MCP server, which the assistant starts
as `aggrega mcp`. The plugin does not install the `aggrega` binary: install it first, as
[the install page](https://aggrega.ai/docs/install/) describes, so the assistant finds
`aggrega` on the `PATH`.

Claude Code:

```bash
claude plugin install aggrega --marketplace aggrega-ai/aggrega-skills
```

Inside Claude Code, the same is `/plugin install aggrega --marketplace aggrega-ai/aggrega-skills`.

Codex:

```bash
codex plugin marketplace add aggrega-ai/aggrega-skills
codex plugin add aggrega@aggrega
```

GitHub Copilot CLI:

```bash
copilot plugin marketplace add aggrega-ai/aggrega-skills
copilot plugin install aggrega@aggrega
```

Gemini CLI:

```bash
gemini extensions install https://github.com/aggrega-ai/aggrega-skills
```

Antigravity:

```bash
agy plugin install https://github.com/aggrega-ai/aggrega-skills
```

Cursor loads a plugin from a folder beneath `~/.cursor/plugins/local/`. Put the repository there,
then run **Developer: Reload Window** from the command palette:

```bash
git clone https://github.com/aggrega-ai/aggrega-skills ~/.cursor/plugins/local/aggrega
```

## Install a skill

To install the skill alone and connect the MCP server yourself, with the open skills
command-line tool, from your project:

```bash
npx skills add aggrega-ai/aggrega-skills
```

With Aggrega itself, which writes the copy built into the binary you have installed, so the
skill matches that version:

```bash
aggrega skills install --agent <assistant>
```

`--agent` takes an assistant's identifier and may be repeated; `--global` installs into the
assistant's per-user skill folder instead of the current project's. The assistants it knows are:

| `--agent` | Assistant | Project folder | Per-user folder |
| --- | --- | --- | --- |
| `amp` | Amp | `.agents/skills` | `~/.config/amp/skills` |
| `antigravity` | Antigravity | `.agents/skills` | `~/.gemini/config/skills` |
| `antigravity-cli` | Antigravity CLI | `.agents/skills` | `~/.gemini/antigravity-cli/skills` |
| `claude-code` | Claude Code | `.claude/skills` | `~/.claude/skills` |
| `cline` | Cline | `.cline/skills` | `~/.cline/skills` |
| `codex` | Codex | `.agents/skills` | `~/.agents/skills` |
| `cursor` | Cursor | `.cursor/skills` | `~/.cursor/skills` |
| `gemini-cli` | Gemini CLI | `.gemini/skills` | `~/.gemini/skills` |
| `github-copilot` | GitHub Copilot | `.github/skills` | `~/.copilot/skills` |
| `goose` | Goose | `.agents/skills` | `~/.agents/skills` |
| `opencode` | OpenCode | `.opencode/skills` | `~/.config/opencode/skills` |
| `windsurf` | Windsurf | `.devin/skills` | `~/.codeium/windsurf/skills` |
| `zed` | Zed | `.agents/skills` | `~/.agents/skills` |

An assistant that is not listed can still use Aggrega through its MCP server: connect it to
`aggrega mcp` as [the install page](https://aggrega.ai/docs/install/) describes.

## Where these files come from

Every file in this repository — the skills and the plugin manifests beside them — is generated
from the source of the `aggrega` binary,
[aggrega-ai/aggrega-cli](https://github.com/aggrega-ai/aggrega-cli), and a tag here names the
Aggrega release the files were generated from. A change to a skill or a manifest is made there;
an edit made here is replaced the next time the files are generated.

## License

The Aggrega skills and plugin are released under the Apache License, Version 2.0; the full text
is in the [`LICENSE`](LICENSE) file.
