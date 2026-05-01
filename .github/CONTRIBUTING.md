# Contributing to wrench-plugins

Skills in this repo are maintained by the Wrench.ai team and synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna).

## Requesting a skill

Open an issue using the **Skill Request** template. Include:
- What the skill should do
- Who it's for (role, context)
- What triggers it
- What "done" looks like

## Reporting a bug

Open an issue with the skill name, what you expected, and what happened.

## The sync model

`SKILL.md` files in this repo are the canonical published versions. They are updated by the Wrench.ai team via `scripts/sync-plugins.sh` in wrench-dna. Do not edit SKILL.md files directly — raise a PR against wrench-dna instead.

---

## Releases and versioning

This repo uses [Semantic Versioning](https://semver.org/) at the marketplace level. Individual plugins have their own `version` field in `plugin.json` and increment independently.

| Change type | Version bump |
|---|---|
| Skill content update, doc fix | Patch (1.0.x) |
| New skill added, new docs | Minor (1.x.0) |
| Removed skill, renamed plugin, schema breaking change | Major (x.0.0) |

### Cutting a release

1. Update `CHANGELOG.md` — add a new `## [x.y.z] — YYYY-MM-DD` section at the top.
2. Bump `"version"` in `.claude-plugin/marketplace.json`.
3. Bump `version:` in `smithery.yaml`.
4. If any plugin's SKILL.md changed, bump its `"version"` in `plugins/<name>/.claude-plugin/plugin.json`.
5. Commit: `git add -A && git commit -m "Release vX.Y.Z"`
6. Tag: `git tag vX.Y.Z`
7. Push: `git push origin main --tags`
8. Create the GitHub Release:
   ```bash
   gh release create vX.Y.Z \
     --title "vX.Y.Z — [short description]" \
     --notes-file <(sed -n '/## \[X.Y.Z\]/,/## \[/p' CHANGELOG.md | head -n -1)
   ```

For patch releases that are only skill content updates, step 6–8 can be skipped — the CHANGELOG entry and version bump are sufficient.
