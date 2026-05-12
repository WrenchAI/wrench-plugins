---
name: passive-deal-updater
description: >
  Passive deal intelligence automation — builds an n8n workflow that nightly pulls signals
  from your CRM, email, calendar, and meeting transcripts, synthesizes them with AI, and
  writes structured deal notes + next steps back to your CRM automatically. Trigger when
  someone says "build the deal updater", "update my deals automatically", "passive CRM
  update", "nightly deal intelligence", "build the deal enrichment workflow", or
  "why hasn't [deal] been updated." Works with Notion, HubSpot, or any CRM with an n8n connector.
compatibility:
  tools:
    - n8n
    - Notion
    - HubSpot
    - Gmail
    - Google Calendar
    - Slack
---

# Passive Deal Updater

Builds an n8n automation that runs nightly, pulls signals from multiple sources, and writes
AI-synthesized deal notes + next steps back to your CRM — so your pipeline reflects reality
without manual data entry.

---

## When to Trigger

**Keyword signals:**
- "build the deal updater", "passive deal intelligence", "nightly deal update"
- "update my deals automatically", "set up the CRM automation"
- "build the deal enrichment n8n workflow"
- "why hasn't [deal] been updated", "what happened with [deal] recently"
- "check playbook completeness on my pipeline"

**Context signals:**
- User has a deal pipeline but CRM notes are stale
- User wants to eliminate manual post-call CRM updates
- User is setting up n8n for CRM automation

---

## System Overview

This skill configures an n8n workflow that:

1. **Queries** your CRM for deals that haven't been updated in 24+ hours
2. **Fetches** recent signals from email, calendar, meeting notes (Slack/Read.ai), and CRM activity
3. **Synthesizes** signals with AI into structured deal notes + next steps
4. **Writes back** to your CRM — notes, stage updates, and recommended next actions
5. **Sends** a Slack DM summarizing what was processed (optional)

**Token budget**: ~2,000 tokens per deal × 10 deals/run = ~20K tokens/run (very efficient)

---

## Before You Start

Answer these questions. The workflow design adapts based on your stack:

1. **CRM:** Notion, HubSpot, Salesforce, or other?
2. **Email:** Gmail or Outlook?
3. **Calendar:** Google Calendar or Outlook Calendar?
4. **Meeting notes:** Slack (with Read.ai or Fireflies posts)? Notion meeting pages? Manual paste?
5. **Notification:** Slack DM? Email? None?
6. **Schedule:** Nightly (recommended) or on-demand?

If unsure, default to: Notion CRM + Gmail + Google Calendar + Slack notification.

---

## CRM Schema Requirements

Your CRM needs these fields on each deal record. If they don't exist, add them before building the workflow.

### Required Fields
| Field | Type | Purpose |
|---|---|---|
| Deal Name | Text | Used to search email, calendar, Slack |
| Stage | Select | Prospecting → Qualified → Proposal → Negotiation → Closed |
| Priority | Select | High / Medium / Low — determines AI model used |
| Last Enriched | Date | Skip gate — deals updated < 24h ago are skipped |
| Enrichment Status | Select | Pending / Enriched / Needs Refresh |
| Next Step | Text | AI-generated recommended action, surfaced inline |

### Recommended Fields
| Field | Type | Purpose |
|---|---|---|
| Deal Value | Number | Helps prioritize processing order |
| Close Date | Date | Signals urgency |
| Primary Contact Email | Email | Used to search Gmail threads |
| Calendar Event Link | URL | Links to last/next meeting |

---

## Workflow Design

**Schedule**: Nightly (recommended: 10 PM local time — processes while you sleep, ready for morning)
**Batch size**: 10 deals per run (prevents token overload)
**Skip logic**: Deals updated < 24 hours ago are skipped automatically

### Node Sequence

```
1. Schedule Trigger (nightly cron)
2. Query CRM — get deals where Last Enriched > 24h or null
3. Sort + limit to 10 (Priority: High first, then oldest)
4. Loop over each deal:
   ├── 4a. Fetch CRM activity / notes (HubSpot timeline or Notion Notes DB)
   ├── 4b. Search Slack for deal name mentions (last 14 days)
   ├── 4c. Search Gmail for deal name (last 14 days)
   └── 4d. Search Calendar for deal name (last 14 days / next 7 days)
5. Aggregate signals into compact context (~1,500 tokens)
6. Send to Claude API — structured JSON response
7. Parse Claude response
8. Write note to CRM (linked to deal)
9. Update deal record (Last Enriched, Next Step, Stage if changed)
10. Send Slack DM summary (optional)
```

---

## Complete n8n Node Configurations

### Node 1: Schedule Trigger
```json
{
  "name": "Nightly Schedule",
  "type": "n8n-nodes-base.scheduleTrigger",
  "parameters": {
    "rule": {
      "interval": [{"field": "cronExpression", "expression": "0 4 * * *"}]
    }
  },
  "notes": "4 AM UTC = 10 PM ET / 9 PM CT / 7 PM PT. Adjust for your timezone."
}
```

### Node 2: Query CRM (Notion example — adapt for HubSpot)

**Notion:**
```json
{
  "name": "Get Stale Deals",
  "type": "n8n-nodes-base.notion",
  "parameters": {
    "resource": "databasePage",
    "operation": "getAll",
    "databaseId": "YOUR_DEALS_DATABASE_ID",
    "returnAll": true,
    "filterType": "manual",
    "filters": {
      "and": [
        {"property": "Stage", "select": {"does_not_equal": "Closed Won"}},
        {"property": "Stage", "select": {"does_not_equal": "Closed Lost"}},
        {"or": [
          {"property": "Last Enriched", "date": {"is_empty": true}},
          {"property": "Last Enriched", "date": {"before": "={{ $now.minus({hours: 24}).toISO() }}"}}
        ]}
      ]
    }
  }
}
```

**HubSpot alternative:**
Use `HubSpot → Get All Deals` with a filter on `hs_lastmodifieddate` older than 24 hours.

### Node 3: Sort + Limit to 10

```javascript
// Code node: prioritize High-priority deals, then oldest first
const BATCH_SIZE = 10;
const priorityOrder = { 'High': 0, 'Medium': 1, 'Low': 2 };

const deals = $input.all().map(item => {
  const props = item.json.properties || item.json;
  return {
    id: item.json.id,
    dealName: props['Deal Name']?.title?.[0]?.plain_text || props.dealname || 'Unknown',
    stage: props['Stage']?.select?.name || props.dealstage || 'Prospecting',
    priority: props['Priority']?.select?.name || 'Low',
    dealValue: props['Deal Value']?.number || props.amount || 0,
    lastEnriched: props['Last Enriched']?.date?.start || null,
    contactEmail: props['Primary Contact Email']?.email || null,
  };
});

deals.sort((a, b) => {
  const pA = priorityOrder[a.priority] ?? 3;
  const pB = priorityOrder[b.priority] ?? 3;
  if (pA !== pB) return pA - pB;
  // Oldest enrichment date first (null = never enriched = highest priority)
  if (!a.lastEnriched) return -1;
  if (!b.lastEnriched) return 1;
  return new Date(a.lastEnriched) - new Date(b.lastEnriched);
});

return deals.slice(0, BATCH_SIZE).map(d => ({ json: d }));
```

### Node 4a: Fetch CRM Activity
**Notion Notes DB:**
```json
{
  "name": "Notion: Get Deal Notes",
  "type": "n8n-nodes-base.notion",
  "parameters": {
    "resource": "databasePage",
    "operation": "getAll",
    "databaseId": "YOUR_NOTES_DATABASE_ID",
    "filterType": "manual",
    "filters": {"property": "Deal", "relation": {"contains": "={{ $json.id }}"}}
  }
}
```
**HubSpot alternative:** Use `HubSpot → Get Deal Activities` for the deal ID.

### Node 4b: Slack Search
```json
{
  "name": "Slack: Search for Deal Mentions",
  "type": "n8n-nodes-base.slack",
  "parameters": {
    "resource": "message",
    "operation": "search",
    "query": "={{ $json.dealName }}",
    "options": {"count": 5, "sort": "timestamp", "sortDir": "desc"}
  }
}
```

### Node 4c: Gmail Search
```json
{
  "name": "Gmail: Search Deal Emails",
  "type": "n8n-nodes-base.gmail",
  "parameters": {
    "resource": "message",
    "operation": "getAll",
    "returnAll": false,
    "limit": 5,
    "filters": {"q": "={{ $json.dealName + ' newer_than:14d' }}"}
  }
}
```

### Node 4d: Calendar Search
```json
{
  "name": "Calendar: Get Meetings",
  "type": "n8n-nodes-base.googleCalendar",
  "parameters": {
    "resource": "event",
    "operation": "getAll",
    "calendarId": "primary",
    "returnAll": false,
    "limit": 5,
    "options": {
      "timeMin": "={{ $now.minus({days: 14}).toISO() }}",
      "timeMax": "={{ $now.plus({days: 7}).toISO() }}",
      "query": "={{ $json.dealName }}"
    }
  }
}
```

### Node 5: Aggregate Signals
```javascript
// Build compact context for Claude (~1,500 tokens input)
const deal = $('Process Each Deal').first().json;

const crmNotes = ($('CRM: Get Deal Notes').all() || [])
  .slice(0, 3)
  .map(n => n.json?.properties?.Note?.rich_text?.[0]?.plain_text?.substring(0, 300) || n.json?.body?.substring(0, 300))
  .filter(Boolean)
  .join('\n') || 'No recent CRM notes';

const slackMentions = ($('Slack: Search').all() || [])
  .slice(0, 5)
  .map(m => m.json?.text?.substring(0, 150))
  .filter(Boolean)
  .join('\n') || 'No Slack mentions';

const emailSummary = ($('Gmail: Search').all() || [])
  .slice(0, 5)
  .map(m => {
    const subject = m.json?.payload?.headers?.find(h => h.name === 'Subject')?.value || 'Unknown';
    return `Subject: ${subject} | ${m.json?.snippet?.substring(0, 100)}`;
  })
  .filter(Boolean)
  .join('\n') || 'No recent emails';

const calSummary = ($('Calendar: Get Meetings').all() || [])
  .slice(0, 5)
  .map(e => `${e.json?.summary || 'Meeting'} — ${e.json?.start?.dateTime || e.json?.start?.date}`)
  .filter(Boolean)
  .join('\n') || 'No meetings found';

// Use a lighter model for standard deals, save heavier model for high-priority
const model = deal.priority === 'High' ? 'claude-sonnet-4-5' : 'claude-haiku-4-5';

return [{ json: { deal, model, context: { crmNotes, slack: slackMentions, email: emailSummary, calendar: calSummary } } }];
```

### Node 6: Claude API Call
```javascript
// HTTP Request: POST https://api.anthropic.com/v1/messages
// Headers: x-api-key: YOUR_ANTHROPIC_KEY, anthropic-version: 2023-06-01

const { deal, model, context } = $json;

const PLAYBOOK_CHECKS = [
  "Budget confirmed?",
  "Business goals / KPIs documented?",
  "Decision maker identified and engaged?",
  "Competitors known?",
  "Timeline / close date established?",
  "Champion or internal advocate identified?",
  "Demo scheduled or completed?",
  "Proposal sent?",
  "Objections surfaced and addressed?",
  "Next follow-up scheduled?"
];

const prompt = `You are a sales intelligence assistant. Analyze the signals below for this deal.

Return ONLY valid JSON — no preamble, no markdown:
{
  "note_bullets": ["3-5 concise bullet points on deal status / what happened"],
  "playbook_gaps": ["list of checklist items with NO evidence in the signals"],
  "next_step": "ONE specific actionable next step with a person and timeframe",
  "stage_change": null or one of ["Prospecting","Qualified","Proposal Sent","Negotiation","Closed Won","Closed Lost"],
  "priority_flag": null or one of ["High","Medium","Low"],
  "confidence": "high", "medium", or "low"
}

DEAL: ${deal.dealName}
STAGE: ${deal.stage} | VALUE: $${deal.dealValue?.toLocaleString() || 'TBD'} | PRIORITY: ${deal.priority || 'Unknown'}

CRM NOTES / ACTIVITY:
${context.crmNotes}

SLACK / MEETING TRANSCRIPT MENTIONS:
${context.slack}

EMAIL THREADS (last 14 days):
${context.email}

CALENDAR EVENTS (last 14 days / next 7 days):
${context.calendar}

SALES PLAYBOOK COMPLETENESS CHECK:
${PLAYBOOK_CHECKS.map((c, i) => `${i+1}. ${c}`).join('\n')}

Flag any items that have NO evidence in the signals above as playbook gaps.`;

return {
  json: {
    model,
    max_tokens: 800,
    messages: [{ role: "user", content: prompt }]
  }
};
```

### Node 7: Parse Claude Response
```javascript
const content = $json.content?.[0]?.text || '{}';
let parsed;
try {
  parsed = JSON.parse(content.replace(/```json\n?|\n?```/g, '').trim());
} catch(e) {
  parsed = {
    note_bullets: ["⚠️ Parse error — manual review needed"],
    playbook_gaps: [],
    next_step: "Review deal manually",
    stage_change: null,
    priority_flag: null,
    confidence: "low"
  };
}

const deal = $('Aggregate Signals').first().json.deal;
const today = new Date().toISOString().split('T')[0];
const noteTitle = `${deal.dealName} — AI Update ${today}`;

const bullets = (parsed.note_bullets || []).map(b => `• ${b}`).join('\n');
const gaps = (parsed.playbook_gaps || []).length > 0
  ? '\n\n⚠️ Playbook Gaps:\n' + parsed.playbook_gaps.map(g => `• ❌ ${g}`).join('\n')
  : '\n\n✅ No critical playbook gaps detected';

const noteBody = `AI Deal Update — ${today}\n\n${bullets}${gaps}\n\n🎯 Next Step:\n${parsed.next_step}\n\nConfidence: ${parsed.confidence} | Sources: CRM, Slack, Email, Calendar`;

return [{ json: { deal, noteTitle, noteBody, stageChange: parsed.stage_change, nextStep: parsed.next_step, playbookGaps: parsed.playbook_gaps, confidence: parsed.confidence } }];
```

### Node 8: Write Note to CRM

**Notion:**
```json
{
  "name": "Notion: Create Note",
  "type": "n8n-nodes-base.notion",
  "parameters": {
    "resource": "databasePage",
    "operation": "create",
    "databaseId": "YOUR_NOTES_DATABASE_ID",
    "title": "={{ $json.noteTitle }}",
    "propertiesUi": {
      "propertyValues": [
        {"key": "Deal", "type": "relation", "relationValue": [{"id": "={{ $json.deal.id }}"}]}
      ]
    },
    "blockUi": {
      "blockValues": [{"type": "paragraph", "textContent": "={{ $json.noteBody }}"}]
    }
  }
}
```

**HubSpot alternative:** Use `HubSpot → Create Note` associated with the deal ID.

### Node 9: Update Deal Record
```json
{
  "name": "CRM: Update Deal",
  "type": "n8n-nodes-base.notion",
  "parameters": {
    "resource": "databasePage",
    "operation": "update",
    "pageId": "={{ $json.deal.id }}",
    "propertiesUi": {
      "propertyValues": [
        {"key": "Last Enriched", "type": "date", "date": "={{ $now.toISO() }}"},
        {"key": "Enrichment Status", "type": "select", "selectValue": "Enriched"},
        {"key": "Next Step", "type": "rich_text", "textContent": "={{ $json.nextStep }}"}
      ]
    }
  }
}
```

### Node 10: Slack Summary (Optional)
```javascript
const processed = $input.all();
const gaps = processed.flatMap(p => p.json.playbookGaps || []).length;
const stageChanges = processed.filter(p => p.json.stageChange).length;

let msg = `*🤖 Deal Updater — Run Complete*\n`;
msg += `_${new Date().toLocaleDateString('en-US', {weekday: 'long', month: 'long', day: 'numeric'})}_\n\n`;
msg += `✅ *${processed.length} deals* processed and updated\n`;
if (stageChanges > 0) msg += `📈 *${stageChanges} stage changes* recommended — review in CRM\n`;
if (gaps > 0) msg += `⚠️ *${gaps} playbook gaps* flagged — check deals before next call\n`;

return [{ json: { message: msg } }];
```

---

## Playbook Completeness Check

The workflow checks these 10 items against signals for every deal. Anything with no evidence is flagged as a gap:

| # | Check | Why it matters |
|---|---|---|
| 1 | Budget confirmed | No budget = no deal |
| 2 | Goals / KPIs documented | Can't close without a success definition |
| 3 | Decision maker engaged | Champions can't sign |
| 4 | Competitors known | If you don't know who you're losing to, you can't win |
| 5 | Timeline established | No urgency = no close |
| 6 | Champion identified | Someone has to fight for you internally |
| 7 | Demo done | Buyers need to see it work |
| 8 | Proposal sent | Can't close what hasn't been proposed |
| 9 | Objections addressed | Unaddressed objections are silent killers |
| 10 | Next follow-up scheduled | No next step = deal goes cold |

---

## Efficiency Settings

| Setting | Value | Why |
|---|---|---|
| Batch size | 10 deals/run | ~20K tokens/run — keeps costs low |
| Skip window | 24 hours | Avoids re-processing deals updated today |
| Excluded stages | Closed Won, Closed Lost | No value in enriching closed deals |
| Model (standard) | claude-haiku | Fast and cheap for routine deals |
| Model (High Priority) | claude-sonnet | Better synthesis for VIP deals — worth the cost |
| Schedule | Nightly | Processes while you sleep, ready for morning standup |

---

## WHY THIS APPROACH

Standalone (no Wrench.ai):
This automation replaces the most common reason CRM data rots: manual entry is the last thing anyone does after a hard day of selling. By pulling from the systems reps already use — email, calendar, meeting transcripts — and writing structured notes automatically, the CRM becomes a living record instead of a historical archive.

What's inferred: Deal signals are interpreted from email subjects, calendar event names, and Slack text — not full transcripts or call recordings. Confidence will be lower when these signals are sparse. Flag those deals for manual review.

With Wrench.ai connected:
If a Wrench.ai MCP is connected, call `post_contacts_enrich_update` after each deal is processed to push the outcome signal back into the lead scoring model — so the next call's score reflects what actually happened on this one.
