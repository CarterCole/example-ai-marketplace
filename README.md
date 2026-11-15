# CarterTech AI Marketplace

One repo of AI agent plugins that installs into **Claude Code**, **GitHub Copilot** (CLI and VS Code), **OpenAI Codex** and **Cursor**. Skills also work in **Gemini CLI**.

Everything is written once, in Claude Code's plugin format:

- **Copilot and Codex** read Claude's files as they are.
- **Cursor** gets symlinks to them.
- **Small real copies** exist only where a tool's format differs, mostly hook files. A check script keeps those in sync.

See [how it works](docs/how-it-works.md).

## Plugins

| Plugin | What it is |
|--------|------------|
| [commit-craft](plugins/commit-craft) | Conventional Commits skill, `/commit`, a diff-reviewer agent, a hook that blocks force-pushes to `main`, a git MCP server |
| [changelog-writer](plugins/changelog-writer) | one skill that writes Keep a Changelog entries; the smallest plugin shape, and the one to copy |
| [kitchen-sink](plugins/kitchen-sink) | reference: one of every component, a logger on every hook event for every tool, and an MCP server shipped inside the plugin |

[`examples/project-setup`](examples/project-setup) shows the same approach for a project repo: instructions, rules, skills, agents, commands, hooks and MCP servers for every tool, from one set of Claude files.

## Install

**Claude Code**

```
/plugin marketplace add CarterCole/cartertech-ai-marketplace
/plugin install commit-craft@cartertech
```

**Copilot CLI**

```bash
copilot plugin marketplace add CarterCole/cartertech-ai-marketplace
```

```bash
copilot plugin install commit-craft@cartertech
```

**VS Code**: set `"chat.plugins.enabled": true`, and add `CarterCole/cartertech-ai-marketplace` to `chat.plugins.marketplaces`. Keep the two GitHub defaults, because setting the list replaces them. Then install from the Customizations editor's **Browse** page. See [Copilot](docs/vendors/copilot.md#install-from-this-repo) for details.

**Codex**

```bash
codex plugin marketplace add CarterCole/cartertech-ai-marketplace
```

```bash
codex plugin add commit-craft@cartertech
```

**Cursor**: `/plugin marketplace add https://github.com/CarterCole/cartertech-ai-marketplace` in the Cursor CLI, or Dashboard → Plugins & MCPs → Import from Repo.

**Gemini CLI** (skills only):

```bash
gemini skills install https://github.com/CarterCole/cartertech-ai-marketplace --path plugins/commit-craft/skills/conventional-commits
```

## What each tool reads

| | Claude Code | Copilot / VS Code | Codex | Cursor |
|---|:-:|:-:|:-:|:-:|
| `.claude-plugin/marketplace.json` | ✓ | ✓ | ✓ | symlink |
| `.claude-plugin/plugin.json` | ✓ | ✓ | ✓ | symlink, or a real copy if the plugin has hooks |
| Skills | ✓ | ✓ | ✓ | ✓ |
| Commands | ✓ | ✓ | as skills | ✓ |
| Agents | ✓ | ✓ | ✗ | ✓ |
| Hooks | ✓ | ✓ | ✓ | own file |
| MCP | ✓ | ✓ | ✓ | ✓ |

The full matrix, project-level files, and what was actually tested are in [compatibility](docs/compatibility.md).

## Layout

```
.claude-plugin/marketplace.json     the marketplace (source of truth)
.cursor-plugin/marketplace.json  →  symlink to it
plugins/<name>/
  .claude-plugin/plugin.json        source of truth
  .cursor-plugin/plugin.json        symlink, or a real copy when hooks differ
  skills/ commands/ agents/ hooks/ .mcp.json README.md LICENSE
examples/project-setup/             project-level setup for every tool
docs/                               formats, lookup orders, test results
scripts/check.mjs                   validates links, manifests, skills and docs
```

## Contributing

To add a plugin:

1. Copy `plugins/changelog-writer`.
2. Rename it.
3. List it in `.claude-plugin/marketplace.json`.
4. Run the check:

   ```bash
   node scripts/check.mjs
   ```

The [authoring guide](docs/authoring.md) covers the rules, versioning, releases and testing in each tool. All docs are indexed in [docs/](docs/README.md).

## License

[MIT](LICENSE)
