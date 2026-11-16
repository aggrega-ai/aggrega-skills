---
name: aggrega
description: Work with Aggrega, the ahead-of-time compiler that turns declarative Aggregate, Component, Recipe, Manifest and Release files into provider-native artifacts for targets such as Helm, Docker Compose and Terraform. Use this skill when the user asks to install Aggrega, connect the aggrega MCP server, start or work in an Aggrega workspace, write or edit an Aggrega entity file, validate or build a Release, find a catalog entity, or understand an Aggrega error. It carries no schema of its own. It sends every structural question to the aggrega binary, through its MCP tools when they are connected and through the aggrega command line otherwise, and every conceptual question to the Aggrega documentation.
---
<!-- Generated from the skill built into the aggrega binary, whose source is github.com/aggrega-ai/aggrega-cli. "aggrega skills install" replaces a copy carrying this line, so keep your own changes in a skill of another name. -->

# Aggrega

Aggrega compiles what you declare into the files your infrastructure tools read. A developer
describes an application as an **Aggregate**; the platform catalog supplies **Components**
and **Features**; **Recipes** and **FeatureRecipes** realize them for a provider such as
Helm, Docker Compose or Terraform; a **Manifest** routes each Component to a Recipe for a
target; and a **Release** fulfills the Aggregate's parameters for one environment. `aggrega
build` turns a Release into provider-native artifacts. The compiler runs ahead of time: it
reads files, follows references and renders text, and it reaches no running cluster or
cloud account.

This skill is a guide to the tools, not a reference. Read it once, then work through the
binary and the documentation.

## Ask the binary, read the documentation

**Never answer a structural question from memory.** Which fields a kind takes, whether a
field is required, what a value may be, whether a file is valid, what a Release renders
to: the `aggrega` binary is the authority, and its answer matches the version the user has
installed. Ask it, and quote what it returns.

- **When the Aggrega MCP server is connected**, use its tools and resources. Call
  `describe-capabilities` with no arguments first: it lists every tool the server offers,
  what each is for and whether it writes. The tools you will use most are `get-schema`,
  `draft`, `validate`, `build`, `describe`, `search` and `explain-error`. The resources
  carry the reference material: `schema://<kind>` for a kind's JSON Schema,
  `langref://<path>` for a language-reference page such as `langref://entities/component`,
  `authoring://<topic>` for the authoring guide such as `authoring://getting-started`, and
  `cli://<command>` for a command's help.
- **Otherwise, run the `aggrega` command line.** `aggrega --help` lists the commands and
  `aggrega <command> --help` explains one. The commands in the loop below are the ones you
  need most.
- **If neither is available**, Aggrega is not installed or not on the `PATH`. Say so, and
  offer to install it as the next section describes. Do not reconstruct a schema from
  memory in the meantime.

**Read the documentation for what things mean** — what a kind is for, which role owns it,
how the pieces fit together. The published documentation is at
<https://aggrega.ai/docs/>. Start with:

- <https://aggrega.ai/docs/authoring/getting-started/> — the authoring loop, end to end.
- <https://aggrega.ai/docs/install/> — installing the binary and connecting a client.
- <https://aggrega.ai/docs/authoring/personas/> and
  <https://aggrega.ai/docs/authoring/entities-by-role/> — the four roles and which kinds each
  one writes.
- <https://aggrega.ai/docs/language-reference/> — every entity kind, the command line, and the
  error reference.

When the MCP server is connected, the same pages are served as the `authoring://` and
`langref://` resources, from the documentation bundle that matches the installed binary.

## Install Aggrega

Check first: `aggrega version` prints the installed version. If the command is not found,
install the binary. The installer downloads the latest release, checks it against the
release's published SHA-256 digests and installs it for the current user only, without
administrator rights. Ask the user before you run it.

On Linux and macOS:

```bash
curl -fsSL https://aggrega.ai/install.sh | sh
```

On Windows, in PowerShell:

```powershell
irm https://aggrega.ai/install.ps1 | iex
```

On Linux and macOS the binary lands in `~/.local/bin`. When that folder is not on the
`PATH`, the installer prints the line to add and names the file your shell reads it from;
a new terminal then finds `aggrega`. On Windows it lands in the `Programs\aggrega` folder
inside the directory the LOCALAPPDATA environment variable names, and the installer adds
that folder to the user `PATH` itself. Run `aggrega version` again to confirm. The manual
steps, the release page and the signature checks are on the setup page.

When this skill came with the Aggrega plugin, it is updated with the plugin. A copy written
by `aggrega skills install` is refreshed to match the installed binary by running
`aggrega skills install --agent <assistant>` again; `aggrega skills install --help` names the
assistants it knows.

## Connect the MCP server

The server is the `aggrega` command with the single argument `mcp`. The client launches it
and speaks the protocol over its standard input and output, so there is no port, account or
separate service.

**When the Aggrega plugin is installed in the assistant, the server is already connected:**
the plugin starts `aggrega mcp` itself. The plugin does not install the binary, so `aggrega`
must still be on the `PATH`; if it is not, install it as the previous section describes.
Otherwise, add the server the way your client adds one:

- **Claude Code:** `claude mcp add --transport stdio aggrega -- aggrega mcp`
- **Codex:** `codex mcp add aggrega -- aggrega mcp`, or an `[mcp_servers.aggrega]` table in
  `~/.codex/config.toml` with `command = "aggrega"` and `args = ["mcp"]`.
- **Gemini CLI:** `gemini mcp add aggrega aggrega mcp`
- **A client that reads a JSON server list** — Cursor's `.cursor/mcp.json`, for example — takes
  this entry:

  ```json
  {
    "mcpServers": {
      "aggrega": {
        "command": "aggrega",
        "args": ["mcp"]
      }
    }
  }
  ```

- **Visual Studio Code** names the list `servers` and each entry's transport:
  `"aggrega": { "type": "stdio", "command": "aggrega", "args": ["mcp"] }`.

The setup page lists where each client keeps its server list. Most clients load a new
server only after a restart or a reload. When the client reports that it cannot find
`aggrega`, put the absolute path to the binary in `command`. Once connected, call
`describe-capabilities` to confirm that the tools are there.

Some assistants start a plugin's server in the plugin's own folder rather than in the user's
project; Codex does. Before working in a project, check which workspace the server answers
in: `config-show` reports the workspace root in force, and an answer that found no workspace
names the folder it used instead. If that is not the project, pass the project's folder as
`workspace_root` on each call that takes it.

## Start a workspace

A workspace is a folder whose `.aggrega-config.yaml` carries a `workspace:` block; every
command run inside it finds that file and treats the folder as the workspace root. In the
folder that should become the project:

```bash
aggrega config init --workspace
```

This writes `.aggrega-config.yaml` with the `workspace:` block and registers the official
registry, `hub.aggrega.ai`, with its signature trust policy, so catalog entities can be
pulled and verified. Without `--workspace`, the same command writes the machine-wide
configuration file instead. It refuses to overwrite an existing file unless `--force` is
given. `aggrega config show` reports the configuration that applies and which file each
setting came from; over MCP, the `config-show` tool reports the same.

## The authoring loop

1. **Pick the kind your task needs.** A developer writes an Aggregate; the platform catalog
   is Components and Features; Recipes and FeatureRecipes realize them for a provider; a
   Manifest and a Release decide where and how the Aggregate is deployed. The split is
   guidance for keeping concerns apart, and the compiler accepts any authoring pattern that
   passes schema validation.
2. **Learn its shape from the schema.** `aggrega get-schema --list` names every kind and
   `aggrega get-schema <kind>` prints one schema; over MCP, use the `get-schema` tool or read
   `schema://<kind>`. Read the kind's reference page for what each field means.
3. **Start from a skeleton.** `aggrega draft <kind> [name]` prints a starter document
   carrying exactly the fields the kind requires, with a placeholder for each value still
   owed. It writes nothing, so redirect it into a file:
   `aggrega draft component my-service > my-service.yaml`. Over MCP, use the `draft`
   tool.
4. **Fill in the fields.** Take every field name and value shape from the schema. To reuse
   what a catalog already offers, `aggrega search <query>` finds entities in the local index
   of the configured registries, which `aggrega registry update` builds, and
   `aggrega inspect <name>` shows one as the compiler resolves it; over MCP, use `search` and
   `describe`.
5. **Validate.** `aggrega validate <file>` checks one Component, Feature, Recipe,
   FeatureRecipe, Workload or Plugin file; `aggrega validate --release <release.yaml>` checks
   everything a Release reaches; `aggrega validate` with no argument checks the whole local
   catalog. Over MCP, use the `validate` tool. Fix what it reports before you build.
6. **Build.** `aggrega pull --release <release.yaml>` resolves the Release's dependencies and
   writes its lockfile, and `aggrega build --release <release.yaml>` compiles it and writes the
   artifacts under `dist/` at the workspace root. `aggrega build --release <release.yaml>
   --auto-pull` does both in one step. Over MCP, use the `pull` and `build` tools. Read the
   rendered artifacts: a clean build says the input was valid, and the output is what tells
   you whether it does what you meant.

When a command halts, its message names a code. `aggrega explain <code>` — or the
`explain-error` tool — says what the condition means and what to change.

## Habits that keep the work correct

- Quote the binary. A field name, a required key or an allowed value you state comes from
  `get-schema`, `validate` or `draft`, not from recollection.
- Keep secrets out of the files. A password or token is supplied by environment variable or
  flag when it is needed, never written into an entity or a configuration file.
- Per-environment values reach a Release through the parameters its Aggregate exposes. When
  a value seems to belong in a Release, look for the parameter, and confirm the shape against
  the schema.
- Show the user what you wrote and what the binary said about it, and say which command or
  tool you ran.
