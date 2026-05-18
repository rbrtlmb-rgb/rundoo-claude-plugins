---
name: bug-ticket
description: Create a bug ticket in Notion's "CX Tickets" database using the standard Rundoo bug template. Triggers on "create a bug ticket", "file a bug", "/bug-ticket", or similar phrasings. Gathers details from conversation context (or prompts for missing fields), confirms with the user, then creates the page with Ticket Type=Bug, Status=To Review, and the template content sections filled in.
---

# Create Bug Ticket in Notion (CX Tickets)

Use this skill whenever the user wants to create a bug ticket. The target is the **CX Tickets** database in Notion, using the **[Bug] - [Actual Issue]** template.

## Target

- **Database / data source ID**: `collection://24de1139-86ea-80eb-ab35-000b4262aaf4`
- **Database name**: 🪲 CX Tickets
- **Template page URL** (for reference): https://www.notion.so/rundoo/Bug-Actual-Issue-24de113986ea8037a1f6f563fb0ae0fe

## Fields to capture

Gather these from the conversation context first. Only ask the user for items that are genuinely missing or ambiguous. Batch any clarifying questions with `AskUserQuestion` rather than asking one at a time.

| Field | Required? | Notes |
|---|---|---|
| Title (the bug summary) | Yes | Format as `[Bug] - <concise issue>`. Keep under ~80 chars. |
| Subdomain / Client | Yes | Which client subdomain(s) are affected. If "all" or unknown, ask. Must be a value in the `Client` multi-select options. |
| Expected Experience | Yes | What should happen. |
| Actual Experience | Yes | What is happening. |
| Reproducible? | Yes | YES / NO. If YES, capture numbered repro steps. If NO, capture why. |
| Screenshots / Recordings | Optional | URLs or descriptions. Leave blank if none. |
| Related Tickets / Context | Optional | Links to Intercom, prior tickets, Slack threads, etc. |
| Priority | Default `High` | One of: `Most Urgent`, `Urgent`, `High`, `Medium`, `Low`. Ask if severity feels different from the default. |
| Intercom URL | Optional | If the bug came from an Intercom conversation, include the link. |

## Confirmation before creating

Always show the user a compact preview of the ticket (title + priority + summary of each section) and confirm before calling the Notion tool. If they want edits, apply them and re-confirm.

## How to create the page

Use `mcp__claude_ai_Notion__notion-create-pages` (load via ToolSearch if not yet available) with:

- `parent.data_source_id`: `24de1139-86ea-80eb-ab35-000b4262aaf4`
- `pages[0].properties`:
  - `title` (the title-property): the formatted title
  - `Ticket Type`: `Bug`
  - `Status`: `To Review`
  - `Priority`: as selected (default `High`)
  - `Tag`: include `🐞 Bug`
  - `Client`: array of selected subdomain values (must match existing options exactly)
  - `Intercom`: URL if provided
- `pages[0].content`: enhanced-Markdown body following the template structure below

### Content body template

Render the page body in this exact structure (using enhanced Markdown):

```
### 🌐 Subdomain
- <subdomain or "Client Subdomains">

### ❔ Expected Experience: *Describe what should happen.*
- <expected experience>

### ❓ Actual Experience: *Describe what is happening.*
- <actual experience>

### ✅ Can you reproduce? If so, list steps. If not, explain why.
- YES / NO
- <numbered steps or explanation>

### 📸 Screenshots / Recordings: *(Attach any visuals that help clarify the issue)*
<screenshot URLs or "None">

---

**🧩 Related Tickets / Context**
<related links or "None">
```

## After creating

Report back to the user with the page URL. Keep it short — one line with the link is enough.

## Property option reference (important)

These are the only valid values for selects/multi-selects on this database. If the user gives something that doesn't match, ask for clarification rather than guessing.

- **Priority** (select): `Most Urgent`, `Urgent`, `High`, `Medium`, `Low`
- **Status** (status): use `To Review` for new bugs
- **Ticket Type** (select): always `Bug` for this skill
- **Tag** (multi-select): include `🐞 Bug`; other relevant tags from the schema may be added if clearly applicable (`🤖 Tech`, `🔌 Integrations`, `📊 Reporting/AI`, `📖 Ledger`, etc.)
- **Client** (multi-select): must exactly match an existing subdomain option (e.g., `aboffs`, `scoreseed`, `all`). The full list lives in the data source schema — fetch it via `notion-fetch` on the collection URL above if needed.

If the user names a client that isn't an existing option (e.g., a new client subdomain), tell them and ask whether to (a) pick the closest existing match, (b) use `all`, or (c) skip the Client property.

## Edge cases

- **Multiple clients affected** → set `Client` as a multi-select array with all the subdomains.
- **Unknown subdomain** → use `all` for the Client property and put "Unknown / multiple" in the Subdomain content section.
- **No repro yet** → set "NO" and put the reason in the steps section.
- **Bug originates from this conversation** (e.g., user described a problem they just hit) → infer fields from context, then confirm before creating.
