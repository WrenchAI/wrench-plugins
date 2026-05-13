# dossier-builder

> Contact intelligence dossier builder — Wrench.ai signals as a scannable HTML artifact

Builds a complete pre-meeting intelligence brief for any contact in your Wrench.ai workspace
and pushes it to a persistent Cowork sidebar artifact. One command → full dossier in the
sidebar, ready to scan before the call.

## What it produces

- Lead score, persona, model grade
- Recommended playbook + objection responses
- Ready-to-send email + LinkedIn drafts
- Sender-specific overlap (your communication style × their behavioral profile)
- Behavioral predictions: what they'll push back on, what lands, what drives action
- Supporting evidence (collapsed): Shapley drivers + meta-measure chart

## Install

```
/plugin install dossier-builder@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install dossier-builder@wrench-plugins`

## Usage

Trigger from chat:

```
dossier for Jane Smith at Acme Corp
build a dossier for john.doe@example.com
pre-meeting intel on Marcus Webb
```

## Requirements

- Wrench.ai workspace connected via MCP
- Cowork mode (for artifact push)

## Changelog

### v1.1.0
- Removed internal connector references and machine-specific paths
- Genericized sender badge (`Sender: [Your Name]`)
- Workspace guardrail rewritten for public use

### v1.0.0
- Initial release

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna) — Wrench.ai's standards and skills repository.
