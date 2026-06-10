# Contributing to over-engineering-plugins

This repository is a Claude Code plugin **marketplace index**. It contains no plugin code — each plugin lives in its own repository and is referenced from `.claude-plugin/marketplace.json` by a GitHub source. Plugin development happens in the plugin repos; this guide covers listing a plugin here and the conventions every listed plugin must follow.

## Listing a plugin

1. **Build the plugin in its own repo.** The only hard requirement is a `.claude-plugin/plugin.json` manifest at the repo root:

   ```json
   {
     "name": "<slug>",
     "version": "0.1.0",
     "description": "<one sentence, active voice, under 200 chars>",
     "author": { "name": "Alex Cristea" },
     "homepage": "https://github.com/alexcristea/<slug>-plugin",
     "repository": "https://github.com/alexcristea/<slug>-plugin",
     "license": "MIT",
     "keywords": ["..."]
   }
   ```

   Recommended layout (see [c4-plugin](https://github.com/alexcristea/c4-plugin) for a full example):

   ```
   <slug>-plugin/
   ├── .claude-plugin/plugin.json
   ├── .github/workflows/ci.yml     # the plugin tests itself
   ├── skills/<skill>/SKILL.md      # → /<slug>:<skill>
   ├── agents/, hooks/, commands/   # optional
   ├── scripts/, references/, templates/, tests/, examples/
   ├── README.md
   └── LICENSE
   ```

2. **Pick a slug.** Lowercase, no spaces, unique within this marketplace. It appears in every slash command (`/<slug>:<skill>`). Keep it short — `c4`, not `c4-architect-plugin`. The repo is conventionally named `<slug>-plugin`.

3. **Add an entry to `.claude-plugin/marketplace.json`:**

   ```json
   {
     "name": "<slug>",
     "source": { "source": "github", "repo": "alexcristea/<slug>-plugin" },
     "description": "<same sentence as plugin.json>",
     "category": "<architecture | development | docs | ...>",
     "keywords": ["..."],
     "homepage": "https://github.com/alexcristea/<slug>-plugin"
   }
   ```

   Entries deliberately omit `version` and `ref` — they track the plugin repo's default branch, so plugin releases need no marketplace PR.

4. **Update the plugin table in `README.md`.**

5. **Smoke-install** before opening the PR: in a Claude Code session,

   ```
   /plugin marketplace add alexcristea/claude-marketplace
   /plugin install <slug>@over-engineering-plugins
   ```

   (For a not-yet-merged branch, install the plugin repo directly: `/plugin install alexcristea/<slug>-plugin`.)

## Cross-plugin conventions

These apply to **every** plugin listed in this marketplace; they're enforced by each plugin repo's own CI.

### Manifest

- `name` is lowercase, no spaces, matches the marketplace entry.
- `version` follows SemVer (`MAJOR.MINOR.PATCH`).
- `description` is one sentence, active voice, under 200 characters.

### Skills

- `SKILL.md` frontmatter must include `name`, `description`, `allowed-tools`.
- `description` is active voice, under 200 characters. *"Edit a C4 workspace ..."*, not *"This skill is used to edit ..."*.
- References to bundled files go through `${CLAUDE_PLUGIN_ROOT}/...`, never hard-coded paths.
- For non-invocable skills triggered by file edits, set `paths:` glob(s) and omit user-facing instructions.

### Slash command namespace

Every skill becomes `/<plugin-name>:<skill-name>`. Use distinctive verbs (`init`, `update`) within a plugin's domain, not generic ones (`run`, `do`) that compete with other plugins in a user's palette.

### Shell scripts

- `#!/usr/bin/env bash` and `set -euo pipefail` at the top.
- Detect missing `docker` (or other external binaries) with a clear stderr message and exit code `127`.
- Detect a stopped Docker daemon and exit `1`. Missing input file: exit `2`.
- On Apple Silicon, pass `--platform linux/amd64` for any Java-based images that lack ARM builds (Structurizr, etc.).

### Tests

- Each plugin repo has CI that exercises its shell tooling and templates **without invoking Claude**.
- For things only Claude can do (LLM-driven skill behaviour), keep a "Manual LLM-behaviour checklist" in the plugin's CONTRIBUTING.md and run it before each release.

## Releasing a plugin

Releases happen entirely in the plugin's repo:

1. Bump `version` in its `.claude-plugin/plugin.json`.
2. Update its `README.md` / references if behaviour changed.
3. Run its manual checklist; CI must be green.
4. Tag: `git tag vX.Y.Z && git push --tags`.

Because marketplace entries track the default branch, users pick the release up with `/plugin marketplace update over-engineering-plugins` followed by `/plugin update <slug>@over-engineering-plugins` — no change to this repo.

## CI in this repo

`.github/workflows/ci.yml` runs **marketplace-lint** on every push and PR: `marketplace.json` parses, every entry has `name`/`source`/`description`, and every `source` is a well-formed `{"source": "github", "repo": "owner/repo"}` reference. Per-plugin lint, smoke, and e2e jobs live in the plugin repos.

## Filing issues

- Plugin bugs → the plugin's own repo (e.g. [c4-plugin issues](https://github.com/alexcristea/c4-plugin/issues)).
- Marketplace problems (bad entry, install resolution, docs) → this repo.

## License

Contributions are accepted under the [MIT License](./LICENSE) unless a plugin explicitly declares a different one in its `plugin.json`.
