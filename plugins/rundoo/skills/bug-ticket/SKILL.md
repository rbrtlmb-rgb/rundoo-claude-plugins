---
name: bug-ticket
description: Create a bug ticket in Notion's "GTM → R&D" intake database (formerly "CX Tickets") using the standard Rundoo [Bug] - [Issue] template. Triggers on "create a bug ticket", "file a bug", "/bug-ticket", or similar phrasings. Gathers details from conversation context (or prompts for missing fields), confirms with the user, then creates the page with Status=Triage, Tag=🐞 Bug, and the template content sections filled in.
---

# Create Bug Ticket in Notion (GTM → R&D)

Use this skill when the user wants to file a bug for the product/engineering team. The target is the **🎟️ GTM → R&D** database, using the **🐛 [Bug] - [Issue]** template.

> This database was previously named "🪲 CX Tickets" and has since been repurposed as the GTM→R&D intake queue. Older instructions referencing `Ticket Type`, an `Intercom` property, or a `To Review` status are out of date — none of those exist anymore.

## Read this before filing

The live template opens with a callout: **"Please do not use AI to write this ticket."** That instruction comes from the database owners, not from Rundoo tooling.

So, before creating anything:

1. Tell the user the template asks authors not to use AI, and get an explicit go-ahead for this ticket.
2. If they proceed, do **not** reproduce the callout in the ticket body — it is an instruction to the author, not ticket content.
3. Add a single line at the end of the body noting the ticket was drafted with Claude from the reporter's description, so reviewers aren't misled about its provenance.

If the user would rather file by hand, offer to hand them the filled-in sections as text to paste into the Notion template themselves.

## Target

- **Data source ID**: `24de1139-86ea-80eb-ab35-000b4262aaf4`
- **Data source URL**: `collection://24de1139-86ea-80eb-ab35-000b4262aaf4`
- **Database name**: 🎟️ GTM → R&D
- **Template**: `🐛 [Bug] - [Issue]` — https://app.notion.com/p/24de113986ea8037a1f6f563fb0ae0fe

## Confirm the schema first

This database's schema has drifted twice and broken this skill both times. Before the first create call in a session, fetch the data source with `notion-fetch` on the `collection://` URL above and confirm:

- the title property is still named `Ticket`
- `Status` still offers `Triage`
- `Priority` and `Tag` option lists still match the reference below

If anything differs, adapt to what you fetched, create the ticket, and tell the user the skill is out of date and what changed.

## Fields to capture

Gather these from conversation context first. Only ask about what is genuinely missing or ambiguous, and batch clarifying questions into one `AskUserQuestion` call rather than asking one at a time.

| Field | Required? | Notes |
|---|---|---|
| Title | Yes | Format as `[Bug] - <concise issue>`. Keep under ~80 chars. |
| Client / subdomain | Yes | Which client subdomain(s) are affected, or all of them. Resolved to `Clients` relation pages — see below. |
| Expected Experience | Yes | What should happen. |
| Actual Experience | Yes | What is happening. |
| Reproducible? | Yes | YES / NO. If YES, numbered repro steps. If NO, why not. |
| Priority | Default `High` | `Most Urgent`, `Urgent`, `High`, `Medium`, `Low`. Ask if severity seems off from the default. |
| Screenshots / Recordings | Optional | URLs or descriptions. `None` if there are none. |
| Related Tickets / Context | Optional | Intercom links, prior tickets, Slack threads. There is no longer an `Intercom` property — put the link in this body section. |

## Resolving the Clients relation

`Clients` is a **relation** to the **Client DB** (`collection://38ee1139-86ea-80c5-8cf0-000bf45bb83b`), not a multi-select. Passing a bare subdomain string will fail. Resolve subdomains to page URLs first:

```sql
SELECT "Name", "Account Name", url
FROM "collection://38ee1139-86ea-80c5-8cf0-000bf45bb83b"
WHERE "Name" IN ('aboffs', 'abbott')
```

In Client DB, `Name` **is** the subdomain, lowercase and exact (`aboffs`, `abbott`, `allpropaints`). `Account Name` carries the human-readable store name (`NY, 22: Aboff's Paints`) and `App URL` is `https://<subdomain>.rundoo.app`. Pass the resulting `url` values to `Clients` as an array.

- **All clients affected** → use the `ALL` page: `https://app.notion.com/38ee113986ea818495c1e4b05502d964`
- **Multiple clients** → pass every matching page URL in the array.
- **Only a store name is known** → match with `WHERE "Account Name" LIKE '%<name>%'` and confirm the subdomain with the user before filing.
- **No match found** → tell the user the client isn't in Client DB and ask whether to use `ALL`, pick the closest match, or leave `Clients` empty. Never invent a page URL.

## Confirmation before creating

Always show a compact preview — title, priority, resolved client(s), and a one-line summary of each section — and get confirmation before calling the Notion tool. If the user wants edits, apply them and re-confirm.

## How to create the page

Use `mcp__claude_ai_Notion__notion-create-pages` (load via ToolSearch if unavailable) with:

- `parent`: `{"type": "data_source_id", "data_source_id": "24de1139-86ea-80eb-ab35-000b4262aaf4"}`
- `pages[0].icon`: `🐛` (matches the template)
- `pages[0].properties`:
  - `Ticket`: the formatted title — this is the **title property**; it is named `Ticket`, not `title`
  - `Status`: `Triage`
  - `Priority`: as selected (default `High`)
  - `Tag`: `["🐞 Bug"]`, plus any other clearly applicable tag
  - `Clients`: array of Client DB page URLs (omit if unresolved)
- `pages[0].content`: the body below

**Do not set** these — they will error or are not yours to fill:

- `Ticket Type` and `Intercom` — removed from the database
- `userDefined:ID` — auto-increment (`SUP-###`)
- `Client Industry` — rollup, derived from `Clients`
- `ACCEPT -> Eng Task` / `REJECT -> Launch` — buttons
- `Eng Tasks`, `Launches`, `Reviewer`, `Rank` — set by triage, not the reporter

### Content body

Mirror the live template's structure:

```
### 🌐 Subdomain
- <subdomain, or "Client Subdomains" if unknown>

### 🎯 **Expected Experience:** *Describe what should happen.*
- <expected experience>

### ⚠️ **Actual Experience:** *Describe what is happening.*
- <actual experience>

### 🔁 **Can you reproduce? If so, list steps. If not, explain why.**
YES / NO
1. <step>
2. <step>

### **📸 Screenshots / Recordings:** *(Attach any visuals that help clarify the issue)*
<screenshot URLs, or "None">

---

**🧩 Related Tickets / Context**
<related links, or "None">

*Drafted with Claude from the reporter's description.*
```

Keep the emoji and heading wording as-is — reviewers scan for that shape. If `notion-fetch` on the template shows different headings, follow the template.

## After creating

Report back with the page URL — one line is enough.

## Property option reference

Verified against the live schema. If the user gives a value that isn't listed, ask rather than guessing.

- **`Ticket`** (title): the only title property.
- **`Status`** (status): use `Triage` for new bugs — it is the sole `to_do` option. Full list: `Triage`, `PM TO REVIEW`, `PM ACCEPTED`, `PM REJECTED`, `ENG REJECTED`, `ENG ACCEPTED`.
- **`Priority`** (select): `Most Urgent`, `Urgent`, `High`, `Medium`, `Low`.
- **`Tag`** (multi-select): always include `🐞 Bug`. Others: `🤖 Tech`, `✨ Cleanup`, `📝 Docs`, `☎️ On-call`, `⭐ CX`, `🔒 Security`, `📊 Reporting/AI`, `🔌 Integrations`, `📖 Ledger`, `⭐ Product`, `Churn Risk`, `aboffs-golive`.
- **`Clients`** (relation → Client DB): array of page URLs. See "Resolving the Clients relation".

## Edge cases

- **Unknown subdomain** → use the `ALL` relation page and put "Unknown / multiple" in the Subdomain body section.
- **No repro yet** → `NO`, with the reason in that section.
- **Bug came from an Intercom conversation** → the link goes in Related Tickets / Context; there is no `Intercom` property.
- **Bug originates from this conversation** → infer the fields from context, then confirm before creating.
- **Parser bug** → this database has separate `[Parser Bug] - [Issue]` and `[Parser Ticket] - [Request]` templates. If the bug is parser/data-ingestion related, say so and ask whether to use the parser template instead.
