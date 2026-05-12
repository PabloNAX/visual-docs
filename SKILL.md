---
name: visual-docs
description: Use when asked to explain, document, visualize, map, teach, onboard, or make an HTML explainer for a repo, feature, module, agent system, prompt stack, provider route, business flow, code pipeline, or code changes/diff. Produces evidence-first graph.json plus a standalone Visual Atlas HTML page that opens from file://.
---

# Visual Docs

Create a clear visual atlas of a codebase, feature, workflow, agent system, or change set.

Default output:

```text
docs/{slug}/
  graph.json
  index.html
```

`index.html` must be standalone, embed the exact `graph.json`, work from `file://`, and open in the default browser at the end.

The goal is not a pretty report. The goal is that a reader can look at the page and immediately know:

- what this thing is
- where the code starts
- what folders matter
- what arrows connect inputs, code, agents, data, and outputs
- one or more real examples that prove the flow
- what to inspect next for common tasks

## Design Basis

Use these ideas quietly; do not name them in the generated HTML unless useful:

- C4-style zoom: context -> containers/packages -> components/modules -> code.
- Runtime scenarios: static structure is not enough; show at least one input-to-output path.
- Code tours: teach through ordered, evidence-backed steps anchored to files.
- Diataxis separation: keep explanation, navigation, reference, and tasks visually distinct.
- Program comprehension is both top-down and bottom-up: start with the mental model, then show exact files and evidence.

## Visual Atlas Standard

Every output must follow this structure. If a section cannot be proven, keep it visible and mark it `No source found` with searched places.

1. **Start Here**
   - One sentence: `{name} is a {category} for {audience}: it {main job}.`
   - Show source root, mode, branch/commit, and confidence.
   - Show the main path in one line with arrows and verbs.
   - Show 3-5 "start reading here" files.

2. **System Shape**
   - Full searchable/collapsible file tree from `sourceRoot`.
   - Top-level folder table with: role, why open, key files, example task, evidence.
   - Mark generated, vendored, binary, deleted, or skipped areas.

3. **Maps**
   - Context map: actors/external systems -> this repo.
   - Container/package map: apps/packages/modules and ownership boundaries.
   - Runtime/change map: input -> entry -> core -> data/config/provider/tools -> output.
   - Subsystem maps when detected: agents, prompts, tools, MCP, providers, routes, workflows, plugins.
   - Every edge has a visible verb and evidence.

4. **Guided Tours**
   - 2-4 ordered examples from README, tests, fixtures, demos, commands, or diff hunks.
   - Each tour has 3-7 steps.
   - Each step has: action, file/path, what happens, why it matters, evidence.
   - At least one tour must be a normal runtime/user path for repo/feature mode.
   - For change mode, include a before -> after tour.

5. **Deep Lenses**
   - Agent lens when agents/prompts/tools/providers/workers exist.
   - Domain lens when business rules matter.
   - Data/API lens when schemas, routes, migrations, or storage matter.
   - UI lens when components/screens/state matter.

6. **Next Actions**
   - Task-oriented navigation: "to change X, start at Y, then check Z."
   - Tests/examples to run or inspect.
   - Risks, unknowns, disabled/dead paths, missing tests.

The first screen must show the answer and the start of the main path. It must not be a cover page.

## Hard Rules

- No unlabeled lines. A line without a verb is noise.
- No giant radial mind map as the primary view.
- No cards replacing the file tree or flow maps.
- No vague claims without evidence.
- No fake examples. Derived examples must be labeled `derived from code`.
- No language mixing in UI labels. Keep code identifiers unchanged.
- No CDN, external assets, build step, dev server, or runtime JSON fetch.
- No generated UI label `Golden path`.
- Keep diagrams compact. Use details/search/drill-down for depth.

## Trigger Modes

Choose the narrowest mode that answers the user:

- `repo`: explain an unfamiliar repository.
- `feature`: explain a feature, module, command, component, API route, tool, prompt stack, or agent system.
- `changes`: explain a branch, PR, local changes, diff, or recent commits.
- `domain`: explain business/product logic behind the code.

Mode requirements:

- `repo`: full file tree, top-level folder atlas, context/container/runtime maps, normal runtime tour, deep lenses for detected subsystems.
- `feature`: boundary file tree, entry point, owner module, input/output, runtime map, examples/tests, nearby code that is not the live path.
- `changes`: base ref, changed-file tree, before/after map, affected contracts, changed examples/tests, blast radius, rollback/risk notes.
- `domain`: actors, objects, actions, outcomes, decisions, rules, domain flow, code locations, real examples.

## Scope Rules

1. Record current `pwd` as `sourceRoot`.
2. Start analysis from `sourceRoot`, not automatically from repository root.
3. Find nearest git root only for branch, commit, status, diff, and tracked path metadata.
4. If broader context is needed, state why in `graph.json.scope`.
5. Respect `.gitignore`; skip generated/build noise unless requested.
6. Use tracked files when possible:

```bash
git ls-files
```

If git is unavailable, walk the filesystem and mark that in `fileTree.generatedFrom`.

## Language

Match the user's language.

- Russian request -> Russian UI, headings, summaries, examples, risks, and notes.
- English request -> English output.
- Keep paths, code identifiers, commands, schema keys, API names, class/function names, and quoted source text unchanged.
- Do not mix English section labels into Russian output.

## Investigation Workflow

Read before drawing.

1. Identify mode, language, source root, git root, branch, commit, and user question.
2. Build inventory:
   - tracked file count and full file tree
   - top-level folders and package boundaries
   - manifests, README/docs, scripts, tests, examples, fixtures
   - entry points: CLI, routes, exports, commands, screens, SDK calls
   - agents/prompts/tools/MCP/providers/workflows/plugins if present
3. Build an evidence ledger:
   - claim
   - source path/line, command output, test, fixture, or diff hunk
   - confidence: `EXTRACTED`, `INFERRED`, or `AMBIGUOUS`
   - searched places when evidence is missing
4. Trace live paths:
   - imports/calls/routes
   - config/env/provider reads
   - data/schema/storage writes
   - spawned agents/workers/tools
   - prompt selection and model/provider routing
5. Extract tours:
   - README quick start or usage
   - tests/fixtures/demos
   - one real command or user action
   - diff hunks for change mode
6. Build `graph.json`.
7. Build standalone `index.html`.
8. Validate.
9. Open the page.

Never invent a relationship because it would make a nicer diagram.

## File Tree Contract

Repo and change docs need a real file tree.

`fileTree` must include:

- `root`
- `generatedFrom`
- `totalFiles`
- `truncated`
- `skipped`
- `nodes`

Each node should have:

- `path`
- `name`
- `type`: `file|dir`
- `role`: `entry|core|agent|prompt|tool|route|schema|config|test|docs|data|ui|generated|skipped|unknown`
- `importance`: `primary|supporting|reference|test|generated|skipped|unknown`
- `whyOpen`
- `evidence`
- `children`

For large repos, keep the tree complete in data and collapsed in UI. If the HTML would become unusable, set `truncated: true`, state the threshold, and show the exact inclusion rule.

## Map Contract

Every map is directional.

Use this edge shape:

```json
{
  "source": "node.id",
  "target": "node.id",
  "verb": "loads",
  "label": "loads config",
  "confidence": "EXTRACTED",
  "evidence": ["src/config.ts:12"]
}
```

Good:

```text
User request --starts--> CLI --loads--> local agents --runs--> agent runtime --calls--> tools --returns--> edits
```

Bad:

```text
CLI -> runtime -> tools
```

Use 4-8 nodes per map. Make multiple small maps instead of one huge graph.

## Guided Tour Contract

Every atlas needs tours. A tour is a real path through the code.

```json
{
  "title": "Run the CLI",
  "source": "README.md:31",
  "kind": "runtime|feature|change|agent|domain",
  "steps": [
    {
      "label": "Install command",
      "file": "README.md",
      "line": 31,
      "does": "documents npm install",
      "why": "this is the user-facing entry",
      "evidence": ["README.md:31"]
    }
  ],
  "proves": "the CLI entry path is documented"
}
```

If no real examples exist, show:

```text
No real example found.
Searched: README, tests, fixtures, demos, constants, package scripts.
```

## Agent Lens

If the repo contains agents, prompts, tools, MCP servers, providers, workers, skills, or handoffs, include an agent lens.

Detect:

- folders/files named `agents`, `.agents`, `skills`, `prompts`, `tools`, `mcp`, `providers`, `models`, `workflows`, `runtime`, `orchestrator`
- YAML/JSON/TOML manifests defining agents/tools/skills/permissions
- prompt templates and system/instruction prompts
- code that spawns workers, routes models/providers, grants tools, or runs tool calls

Show:

- agent definitions and where they live
- prompt sources and selection
- allowed tools and MCP/server connections
- input -> control -> tool call -> output lifecycle
- handoff/spawn routes between agents
- provider/model routing and fallback when visible
- tests/examples proving the path
- unknowns when a route cannot be proven

## graph.json Minimum Shape

Detailed schema and HTML patterns live in `references/atlas-contract.md`. Read it when implementing a full atlas or when schema details are unclear.

Minimum required keys:

```json
{
  "schemaVersion": "visual-docs.v2",
  "project": {
    "name": "string",
    "slug": "string",
    "mode": "repo|feature|changes|domain",
    "language": "string",
    "category": "string",
    "audience": "string",
    "summary": "string",
    "sourceRoot": "string",
    "gitRoot": "string|null",
    "gitCommit": "string|null",
    "branch": "string|null"
  },
  "scope": {
    "question": "string",
    "included": [],
    "skipped": [],
    "baseRef": "string|null"
  },
  "fileTree": {},
  "startHere": {
    "mainPath": [],
    "startFiles": []
  },
  "folders": [],
  "nodes": [],
  "maps": [],
  "tours": [],
  "lenses": [],
  "examples": [],
  "nextActions": [],
  "risks": [],
  "unknowns": [],
  "quality": { "checks": [] }
}
```

Use `schemaVersion: "visual-docs.v2"` for new outputs. If updating old v1 output, migrate it to v2.

## HTML Contract

One vertical page. No tabs by default.

Required order:

1. Sticky mini-nav and stats.
2. Start Here.
3. System Shape: file tree plus folder atlas.
4. Maps.
5. Guided Tours.
6. Deep Lenses.
7. Examples and evidence.
8. Next Actions.
9. Risks and Unknowns.
10. Node/edge/file explorer.

Design:

- Clean monochrome Paperwork style.
- Thin borders, flat sections, readable whitespace.
- Color only for status: green proven, amber inferred, red risk/fail.
- No glass, blur, gradients, glows, heavy shadows, pastel/rainbow decoration, or decorative blobs.
- Long paths wrap inside their own cells.
- Mobile has no horizontal page scroll. Wide maps/tables may scroll inside their own container only.
- Hover/click states do not move layout.

Interactions:

- Search/filter file tree.
- Click file/tree/map/tour step to show details and evidence.
- Map nodes link to file paths.
- Tour steps show file, line, what happens, why it matters.

## Validation

Before finishing:

- `graph.json` parses.
- `schemaVersion` is `visual-docs.v2`.
- `project.mode` is valid.
- Mode-required fields are present or visibly marked missing.
- `fileTree` exists for repo/change docs and includes count, source, skipped areas, and nodes.
- `maps` exist and every edge has source, target, verb, confidence, and evidence.
- `tours` exist with real source evidence or a visible "No real example found" note.
- Agent lens exists when agent/prompt/tool/provider/workflow files are detected, or absence is stated with searched places.
- `index.html` embeds the exact JSON:

```html
<script type="application/json" id="graph-data">
{...exact graph.json copy...}
</script>
```

- Embedded JSON equals `graph.json`.
- Inline JS syntax is valid.
- No browser-global DOM id usage like `title.textContent`.
- No CDN, external assets, build step, dev server, or runtime JSON fetch.
- Page opens from `file://`.
- Top navigation and section headings use the output language.
- First screen satisfies Start Here.
- Full file tree appears before dense graph/explorer sections.
- Maps have visible arrow verbs.
- Guided tours are visible before raw node explorer.
- No generated UI label `Golden path`.
- No horizontal page scroll on mobile.
- `quality.checks` records failures instead of hiding them.

Use a browser smoke test when browser tooling is available. At minimum, parse JSON, compare embedded JSON, and validate inline JS.

## Finish

Open the generated page:

```bash
open docs/{slug}/index.html
```

Final response should include:

- output path
- mode
- source root
- language
- what maps/tours/lenses were generated
- limitations/unknowns
- validation summary
- whether the page opened
