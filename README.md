# over-engineering-plugins

A curated [Claude Code](https://docs.claude.com/claude-code) plugin **marketplace**. This repo is a thin index: every plugin lives in its own repository and is referenced here by a GitHub source, so the same plugin can be listed by other marketplaces or installed directly from its repo.

## Plugins in this marketplace

| Plugin | Repo | Description |
|--------|------|-------------|
| **`c4`** | [alexcristea/c4-plugin](https://github.com/alexcristea/c4-plugin) | Author C4 architecture diagrams as Structurizr DSL, with ADRs and supplementary docs. Skills: `init`, `update`, `validate`, `export`, `preview`, `conventions`. |
| **`ddd`** | [alexcristea/ddd-plugin](https://github.com/alexcristea/ddd-plugin) | Design and unit-test DDD / Clean Architecture layers, driven by a per-project profile. Skills: `create-profile`, `entity-design`, `repository-design`, `usecase-tests`. |

Want to add yours? See [CONTRIBUTING.md](./CONTRIBUTING.md).

## Install a plugin from this marketplace

Add the marketplace once:

```
/plugin marketplace add alexcristea/over-engineering-plugins
```

Install any plugin by name:

```
/plugin install c4@over-engineering-plugins
```

Each plugin keeps its own slash-command namespace — installing `c4` exposes `/c4:init`, `/c4:update`, `/c4:validate`, `/c4:export`, `/c4:preview`. Installing additional plugins from this marketplace won't collide.

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json   # the index — one entry per plugin, each pointing at its own repo
├── .github/workflows/ci.yml   # validates marketplace.json
├── README.md              # ← you are here
├── CONTRIBUTING.md        # how to list a plugin here
└── LICENSE
```

Plugin entries reference external repos:

```json
{
  "name": "c4",
  "source": { "source": "github", "repo": "alexcristea/c4-plugin" }
}
```

Entries track each plugin repo's default branch — no version pinning. Users pick up new plugin commits with:

```
/plugin marketplace update over-engineering-plugins
/plugin update c4@over-engineering-plugins
```

## Releasing a plugin

Releases happen in the plugin's own repo: bump `version` in its `.claude-plugin/plugin.json` and tag (`vX.Y.Z`). Because marketplace entries track the default branch, no change is needed here — see each plugin repo's CONTRIBUTING for its release checklist.

## License

[MIT](./LICENSE). Each plugin declares its own license in its `plugin.json`.
