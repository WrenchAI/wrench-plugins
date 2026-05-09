# wrench-call-log

> Post-call CRM logging and follow-up writer — turns rough notes or a voice transcript into structured CRM fields, key signals, next action, and a ready-to-send follow-up message

## Install

```
/plugin install wrench-call-log@wrench-plugins
```

Or add to your project `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    { "name": "wrench-plugins", "source": { "source": "github", "repo": "WrenchAI/wrench-plugins" } }
  ]
}
```

Then: `/plugin install wrench-call-log@wrench-plugins`

## Usage

Trigger with any of:
- "log this call"
- "call summary"
- "update CRM"
- "post-call notes"
- "I just finished a call with"
- "write up this call"

Accepts stream-of-consciousness notes, bullet points, voice transcript paste, or a verbal description. Works standalone. With Wrench.ai connected, pushes the outcome back to the contact record and pulls meta-measure alignment to calibrate the follow-up draft.

## Category

**Sales** — part of the [Wrench.ai plugin marketplace](https://github.com/WrenchAI/wrench-plugins)

## Source

Synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna) — Wrench.ai's standards and skills repository.
