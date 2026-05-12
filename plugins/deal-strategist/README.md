# deal-strategist

> B2B deal strategy skill — selects the right playbook, recommends the next step, surfaces proof assets, and drafts personalized outreach calibrated to contact archetype. Works from pasted notes or live Wrench.ai data.

## Install

```
/plugin install deal-strategist@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install deal-strategist@wrench-plugins`

## Usage

Trigger with any of:
- "what's the next step for [deal]"
- "what should I send [contact]"
- "prep for my [company] call"
- "draft a follow-up"
- "which playbook should I use"
- "what's the play here"
- "how do I move this deal forward"

Paste deal notes, a CRM export snippet, or just describe the situation. The skill selects a playbook, recommends the next action with timing and angle, surfaces proof assets to reference, and drafts the message — pending your approval.

With Wrench.ai connected, pulls live lead score, persona, and behavioral signals to calibrate the recommendation.

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins). Methodology from [wrench-dna](https://github.com/WrenchAI/wrench-dna).
