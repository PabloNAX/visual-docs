# Visual Atlas Contract

Use this when implementing `visual-docs` output.

## graph.json v2

Recommended shape:

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
    "generatedAt": "ISO string",
    "sourceRoot": "string",
    "gitRoot": "string|null",
    "gitCommit": "string|null",
    "branch": "string|null"
  },
  "scope": {
    "question": "string",
    "included": ["paths or areas analyzed"],
    "skipped": ["paths or areas intentionally skipped"],
    "baseRef": "string|null",
    "workingTreeStatus": []
  },
  "startHere": {
    "sentence": "plain-language identity",
    "mainPath": [
      { "label": "User request", "verb": "starts", "target": "CLI", "evidence": ["README.md:31"] }
    ],
    "startFiles": [
      { "path": "src/index.ts", "why": "entry point", "evidence": ["src/index.ts:1"] }
    ]
  },
  "fileTree": {
    "root": "sourceRoot",
    "generatedFrom": "git ls-files|filesystem walk",
    "totalFiles": 0,
    "truncated": false,
    "threshold": null,
    "skipped": [
      { "path": "dist/", "reason": "generated build output", "fileCount": 120 }
    ],
    "nodes": []
  },
  "folders": [
    {
      "path": "cli/",
      "role": "terminal app",
      "whyOpen": "change startup or commands",
      "keyFiles": ["cli/src/index.tsx"],
      "exampleTask": "add a CLI flag",
      "fileCount": 42,
      "evidence": ["cli/src/index.tsx:1"]
    }
  ],
  "nodes": [
    {
      "id": "cli.entry",
      "type": "entry|file|function|class|route|component|config|prompt|agent|schema|data|test|domain|change",
      "group": "CLI",
      "name": "CLI entry",
      "filePath": "cli/src/index.tsx",
      "line": 1,
      "summary": "short factual summary",
      "input": "what enters",
      "output": "what comes out",
      "risk": "low|medium|high",
      "confidence": "EXTRACTED|INFERRED|AMBIGUOUS",
      "evidence": ["cli/src/index.tsx:1"]
    }
  ],
  "maps": [
    {
      "id": "main-runtime",
      "title": "translated title",
      "kind": "context|container|runtime|changes|agent|provider|route|domain|data|ui",
      "direction": "left-to-right|top-to-bottom",
      "nodes": [
        { "id": "cli.entry", "label": "CLI", "path": "cli/src/index.tsx" }
      ],
      "edges": [
        {
          "source": "cli.entry",
          "target": "agent.runtime",
          "verb": "calls",
          "label": "calls runtime",
          "confidence": "EXTRACTED",
          "evidence": ["cli/src/index.tsx:20"]
        }
      ]
    }
  ],
  "tours": [
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
          "why": "user-facing start",
          "evidence": ["README.md:31"]
        }
      ],
      "proves": "CLI entry path is documented"
    }
  ],
  "lenses": [
    {
      "title": "Agent system",
      "kind": "agent|domain|data|api|ui|change",
      "detected": true,
      "entryPoints": ["agents/base.ts"],
      "maps": ["agent-runtime"],
      "facts": [
        { "label": "Allowed tools", "value": "read_files, write_file", "evidence": ["agents/base.ts:20"] }
      ],
      "unknowns": []
    }
  ],
  "examples": [
    {
      "name": "real scenario",
      "source": "README.md:31",
      "input": "actual input",
      "path": ["entry", "runtime", "output"],
      "output": "actual output",
      "proves": "what the example proves"
    }
  ],
  "nextActions": [
    {
      "task": "change startup",
      "startAt": "cli/src/index.tsx",
      "thenCheck": ["cli/src/__tests__/cli-args.test.ts"],
      "why": "entry owns argv parsing",
      "evidence": ["cli/src/index.tsx:1"]
    }
  ],
  "risks": [
    { "what": "unproven route", "why": "no test found", "evidence": ["searched tests"] }
  ],
  "unknowns": ["specific unknown"],
  "quality": {
    "checks": [
      { "name": "file_tree", "status": "pass|warning|fail", "evidence": "..." }
    ],
    "searched": []
  }
}
```

## HTML Layout Pattern

Use a single-page atlas:

1. Start Here
   - one-sentence identity
   - main path rail
   - start files
2. System Shape
   - full file tree on one side
   - selected file detail panel
   - top folders table
3. Maps
   - context map
   - container/package map
   - runtime/change map
   - subsystem maps
4. Guided Tours
   - cards or vertical steps
   - each step anchored to file/path/evidence
5. Lenses
   - agent/domain/data/API/UI/change details
6. Next Actions
7. Risks/Unknowns
8. Explorer

## Visual Rules

- Prefer 3-6 small maps over one large graph.
- A map node should fit in 1-2 lines.
- Edge labels are verbs, not nouns.
- Evidence is metadata, not headline text.
- Long paths use `overflow-wrap:anywhere`.
- File tree defaults collapsed to top-level folders.
- Search/filter expands matching branches.
- Every clickable map/tour/tree item opens a detail panel.

## CSS Base

```css
:root {
  --bg:#fff; --bg-alt:#f5f5f5; --border:#e5e5e5; --text:#0a0a0a; --muted:#737373;
  --green:#059669; --amber:#d97706; --red:#dc2626; --page:1120px; --radius:8px;
}
html, body { max-width:100%; overflow-x:hidden; }
body {
  margin:0; background:var(--bg); color:var(--text);
  font-family:system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;
  font-size:15px; line-height:1.6; font-weight:300;
}
.container { max-width:var(--page); margin:0 auto; padding:0 48px; }
section { padding:72px 0; border-top:1px solid var(--border); }
.card { background:#fff; border:1px solid var(--border); border-radius:var(--radius); padding:20px; }
.path, code, pre { font-family:ui-monospace,SFMono-Regular,Menlo,monospace; overflow-wrap:anywhere; }
@media (max-width: 720px) {
  .container { padding:0 20px; }
  section { padding:52px 0; }
}
```

## Anti-Patterns

Do not produce:

- a large hero with no useful path
- generic "architecture overview" cards with no file anchors
- radial concept cloud as the main explanation
- a graph where nodes are just folder names and arrows are unlabeled
- full file tree as a plain text dump with no search/detail
- examples that are invented from names rather than README/tests/fixtures/diff
- agent section that lists files but does not show prompt/tool/spawn lifecycle

## Validation Snippets

Use equivalent checks in any available runtime.

```js
const fs = require('fs')
const vm = require('vm')
const graphText = fs.readFileSync('docs/slug/graph.json', 'utf8')
const graph = JSON.parse(graphText)
if (graph.schemaVersion !== 'visual-docs.v2') throw new Error('bad schemaVersion')
if (!graph.fileTree || !Array.isArray(graph.maps) || !Array.isArray(graph.tours)) {
  throw new Error('missing atlas sections')
}
for (const map of graph.maps) {
  for (const edge of map.edges || []) {
    if (!edge.source || !edge.target || !edge.verb || !edge.evidence) throw new Error('bad edge')
  }
}
const html = fs.readFileSync('docs/slug/index.html', 'utf8')
const match = html.match(/<script type="application\/json" id="graph-data">([\s\S]*?)<\/script>/)
if (!match || match[1] !== graphText) throw new Error('embedded JSON mismatch')
for (const script of html.matchAll(/<script(?![^>]*application\/json)[^>]*>([\s\S]*?)<\/script>/g)) {
  new vm.Script(script[1])
}
```
