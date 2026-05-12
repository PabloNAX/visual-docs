# Visual Docs

[![GitHub stars](https://img.shields.io/github/stars/PabloNAX/visual-docs?style=social&cacheSeconds=60&v=2)](https://github.com/PabloNAX/visual-docs/stargazers)
[![License: MIT](https://img.shields.io/badge/license-MIT-0a0a0a.svg)](LICENSE)
![Claude skill](https://img.shields.io/badge/Claude-skill-0a0a0a)
![Codex skill](https://img.shields.io/badge/Codex-skill-0a0a0a)
![No server](https://img.shields.io/badge/output-file%3A%2F%2F%20HTML-e8b94a)

Turn any repository into a Visual Atlas: full file tree, directed maps, guided tours, graph JSON, and standalone HTML.

Visual Docs is a portable AI skill for explaining repositories, features, modules, agent systems, prompt stacks, provider routing, business flows, code pipelines, and code changes. It starts from the current working directory, builds the real file tree, shows how the system is shaped, draws arrows from inputs to outputs, then walks through real examples anchored to files.

The intended outcome is simple: after five minutes, a new reader should know where the code starts, what folders matter, what calls what, which examples prove the flow, what agent/prompt/tool systems exist, and what to inspect next.

![Visual Docs Focus Map](assets/visual-docs-focus-map-en.png)

## Quick Use

```text
Install skill.
Ask: Use visual-docs to explain this repo.
The skill opens docs/{feature}/index.html automatically.
No server. No Python needed.
```

## What It Creates

Default output:

```text
docs/{feature}/
  graph.json
  index.html
```

`graph.json` is the audit/source data for agents. `index.html` embeds the same JSON, opens directly from disk with `file://`, and the skill runs `open docs/{feature}/index.html` when done. No local server, build tool, CDN, or external assets are required.

The JSON uses `schemaVersion: "visual-docs.v2"` and includes mode metadata, source scope, full `fileTree`, `startHere`, folder atlas, directional `maps`, guided `tours`, deep `lenses`, examples, next actions, risks, unknowns, and a `quality.checks` self-audit.

## Page Structure

```text
Start Here
  -> System Shape
  -> Full file tree
  -> Folder atlas
  -> Context / package / runtime maps
  -> Guided tours from real examples
  -> Agent / domain / data / UI lenses when present
  -> Next actions
  -> Risks and unknowns
  -> Explorer
```

The generated page includes:

- one plain sentence explaining what the repo does
- generated copy in the user's language
- the source root that was analyzed
- category, audience, similar tools, and key difference
- full searchable/collapsible file tree with skipped areas named
- start-here files and one-line main path
- context, package/container, runtime/change, and subsystem maps
- directional graphs, rails, tables, or trees with visible verbs on arrows
- guided tours with 3-7 source-backed steps
- subsystem maps for agents, prompts, tools, providers, routes, workflows, plugins, or packages when detected
- agent-system internals: definitions, prompts, tools, lifecycle, handoffs, provider routing, evidence, and unknowns
- folder structure with responsibilities
- "what to inspect next" task guidance
- before/after impact when documenting changes
- real examples from README, tests, fixtures, or source
- node explorer with why, callers, dependencies, risk, and evidence

Every major section includes an example. Every factual claim should point back to source evidence. If examples are not found, the output says that and lists where it searched.

## Quality Gate

A good generated page should pass these checks:

- first screen answers category, audience, main job, similar tools or closest category, difference, and source root
- repo overview includes a full file tree, folder atlas, context map, package/container map, runtime map, and guided tours
- full file tree includes source root, total file count, skipped areas, role badges, and search/collapse UI
- directional maps have visible arrow verbs, evidence, and links back to file paths
- guided tours use real README/test/fixture/demo/command/diff evidence
- agent/prompt/tool/provider/workflow lens appears when those systems exist, or the page states that none was found and where it searched
- feature docs identify entry point, owner module, input, output, main path, branches, examples, and config sources
- change docs identify base ref, changed files by responsibility, before/after behavior, affected contracts, tests, risks, and missing tests
- every important claim has source evidence or is marked `INFERRED`, `AMBIGUOUS`, unknown, or missing
- `index.html` embeds the exact `graph.json` inside `id="graph-data"`
- generated UI is Paperwork-style: monochrome, thin borders, compact diagrams, no pastel/rainbow decoration, no giant radial mind map
- `quality.checks` records validation status instead of hiding weak spots

## Skill Folder

The clean installable skill lives here:

```text
skills/visual-docs/
  SKILL.md
  agents/openai.yaml
```

Do not upload the repository root as the skill folder. Upload or install `skills/visual-docs/`.

The root `SKILL.md` is a working copy for local development. Keep it synced with `skills/visual-docs/SKILL.md` before publishing.

## Runtime Requirements

The skill itself requires no Python, Node, npm, local server, or package install.

Python scripts in skill-building guides are optional helpers for validation or heavy deterministic workflows. Visual Docs keeps the installable skill as plain Markdown plus metadata. If an environment has no Python, the skill still works because the agent can write `graph.json` and `index.html` directly.

## Install

Install for all local agent profiles:

```bash
./install.sh
```

Install for one target:

```bash
./install.sh codex
./install.sh claude
./install.sh agents
```

Preview without writing:

```bash
./install.sh --dry-run
```

Default install targets:

```text
Codex:  ${CODEX_HOME:-$HOME/.codex}/skills/visual-docs
Claude: $HOME/.claude/skills/visual-docs
Agents: ${AGENTS_HOME:-$HOME/.agents}/skills/visual-docs
```

Claude.ai manual install:

1. Zip the `skills/visual-docs/` folder.
2. Upload it in Claude settings where custom skills are managed.

Manual install for any editor or agent:

1. Copy `skills/visual-docs/`.
2. Put it in that tool's custom skills, agents, prompts, or rules directory.
3. Tell the tool: `Use visual-docs to explain this repo.`

If you publish under a different GitHub slug, update the stars badge at the top of this README.

## Use

Example prompts:

```text
Use visual-docs to explain this repo.
Use visual-docs to map the payment flow.
Use visual-docs to explain the latest changes.
Use visual-docs to explain how this CLI tool works.
Use visual-docs to document the agent routing system in Spanish.
Use visual-docs on this repository and make the output in Russian.
```

The skill opens the generated page automatically. You can also open it directly:

```text
docs/{feature}/index.html
```

## Design Contract

Visual Docs favors structure over decoration:

- Paperwork-style white/gray UI with thin borders
- direct human writing, not AI-marketing prose
- no emojis
- no local server
- no generated marketing page
- no giant loose mind map
- no unlabeled graph edges
- no cards replacing the required file tree or directional maps
- no `Golden path` UI labels
- no fake examples
- no external assets in generated output
- compact cards, rails, tables, fixed trees, before/after views, and detail drawers
- readable spacing and quiet source evidence

## Compatibility

| Surface | Status | Install path |
| --- | --- | --- |
| Codex | supported | `$HOME/.codex/skills/visual-docs` |
| Claude Code | supported | `$HOME/.claude/skills/visual-docs` |
| Claude.ai | supported | upload zipped `skills/visual-docs/` |
| Generic agents | supported | `$HOME/.agents/skills/visual-docs` |
| Other editors | manual | copy `skills/visual-docs/` into that editor's prompt/rules system |

## Validate

```bash
bash -n install.sh
./install.sh --dry-run
```

Optional, if you already have the Codex skill-creator tools installed:

```bash
python3 /path/to/skill-creator/scripts/quick_validate.py skills/visual-docs
```

## License

MIT. See [LICENSE](LICENSE).

## Star History

<a href="https://www.star-history.com/#PabloNAX/visual-docs&Date">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=PabloNAX/visual-docs&type=Date&theme=dark" />
    <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=PabloNAX/visual-docs&type=Date" />
    <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=PabloNAX/visual-docs&type=Date" />
  </picture>
</a>
