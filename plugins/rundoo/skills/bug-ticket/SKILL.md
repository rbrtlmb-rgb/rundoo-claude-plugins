---
name: bug-ticket
description: Create a bug ticket in Notion's "GTM → R&D" intake database (formerly "CX Tickets") using the standard Rundoo [Bug] - [Issue] template. Triggers on "create a bug ticket", "file a bug", "/bug-ticket", or similar phrasings. Gathers details from conversation context (or prompts for missing fields), then writes the ticket concisely in the reporter's own voice — plain language, no AI-style analysis or suggested fixes — confirms the wording with the user, and creates the page with Status=Triage, Tag=🐞 Bug.
---

# Create Bug Ticket in Notion (GTM → R&D)

Use this skill when the user wants to file a bug for the product/engineering team. The target is the **🎟️ GTM → R&D** database, using the **🐛 [Bug] - [Issue]** template.

> This database was previously named "🪲 CX Tickets" and has since been repurposed as the GTM→R&D intake queue. Older instructions referencing `Ticket Type`, an `Intercom` property, or a `To Review` status are out of date — none of those exist anymore.

## Read this before filing

The live template opens with a callout: **"Please do not use AI to write this ticket."** That instruction comes from the database owners, not from Rundoo tooling.

So, before creating anything:

1. Tell the user the template asks authors not to use AI, and get an explicit go-ahead for this ticket.
2. If they proceed, do **not** reproduce the callout in the ticket body — it is an instruction to the author, not ticket content.
3. Show the user the full body text before creating it. They are the author of record and the ticket goes out under their name, so they read it and approve the wording — that is where the human check lives.

If the user would rather file by hand, offer to hand them the filled-in sections as text to paste into the Notion template themselves.

## Write it the way the reporter would

These tickets go to engineers who triage a queue. A ticket that states the problem and stops is faster to act on than one that explains, hedges, and recommends. Write as the reporter — first person, plain, specific — not as an assistant summarizing a conversation.

**Length budget.** The whole body should fit on one screen.

- Title: under ~70 characters.
- Expected and Actual: one or two short sentences each. Often one is enough.
- **Repro steps are the exception — spend detail here.** See "The reproduce section" below.
- Nothing else unless it actually exists (screenshots, related links).

**Write like this**

- Plain and direct: "Tax isn't applying to special orders." "Customer called about this twice today."
- Concrete specifics over description: store, order number, SKU, timestamp, browser, POS vs web.
- Fragments are fine: "Only in Chrome. Works in Safari."
- Say only what is known. If the reporter didn't say when it started, don't write "recently."

**Do not write**

- Suggested fixes, root-cause guesses, or `Impact` / `Recommendation` / `Next steps` sections. Triage decides those. If the reporter volunteered a theory, put it in Related Tickets / Context in their own words and label it as their guess.
- Hedges: "it appears," "it seems," "this may be due to."
- Essay connectors: "Additionally," "Furthermore," "Moreover," "Notably," "Overall."
- Padding adjectives: "critical," "seamless," "significant," "robust."
- A closing summary sentence. The last section is the last word.
- Bold or emoji inside section text — the headings already carry the formatting.

**Titles.** Name what is broken and where. Skip process verbs ("investigate," "look into") and condition pileups.

| Instead of | Write |
|---|---|
| `[Bug] - Investigation into intermittent tax calculation discrepancies affecting special order workflows` | `[Bug] - Tax is $0 on special orders` |
| `[Bug] - User reports inability to complete checkout under certain conditions` | `[Bug] - Checkout fails on split payment` |
| `[Bug] - Printing issue` | `[Bug] - Invoice prints blank` |

Name the client in the title only when the bug is specific to that client.

**Worked example.** Reporter says: *"aboffs called, when they ring up a special order the tax line is 0. started yesterday I think. happens on every special order, regular orders are fine."*

Title: `[Bug] - Tax is $0 on special orders at aboffs`

```
### 🌐 Subdomain
- aboffs

### 🎯 **Expected Experience:** *Describe what should happen.*
- Special orders should charge tax like any other order.

### ⚠️ **Actual Experience:** *Describe what is happening.*
- Tax line comes through as $0 on every special order. Regular orders are fine. Reporter thinks it started yesterday.

### 🔁 **Can you reproduce? If so, list steps. If not, explain why.**
NO — reported by aboffs over the phone, not reproduced in-house yet.
- They say it's every special order; regular orders ring up tax fine.
- Haven't checked which register or clerk. Asked them to send a screenshot of the order.
```

Everything outside the repro section is clipped: no impact paragraph, no theory about tax rules, no next steps. The repro section is where the detail goes — see below.

**When detail is missing, stay thin — do not pad.** Ask the reporter one batched round of questions for what triage will obviously need (subdomain, whether it reproduces, when it started), then file with what you have. Never invent a repro step, a timestamp, or a browser you were not given: a short accurate ticket is useful, a padded speculative one wastes triage time and gets the skill distrusted.

## The reproduce section

This is the one section worth being generous with. Terse Expected/Actual lines tell an engineer *what* is wrong; the repro tells them how to *see* it, which is what actually gets a bug fixed. Be thorough here even though the rest of the ticket is clipped.

The brevity rules still apply in one respect: more **observation**, not more **analysis**. Extra detail means the exact click path and the exact data used — never a theory about why it happens.

**Lead with the setup**, then the steps. State whatever of this is known:

- Where: POS register, web back office, mobile; which page or menu.
- Who: user role or permission level, if it might matter.
- Environment: browser and OS, printer or hardware model, Chrome vs Edge.
- Starting data: the specific order, customer, SKU, or account used — real identifiers, not "an order."
- Frequency: every time, or how often out of how many tries.

**Then number the steps.** Each one an imperative the engineer can follow blind:

- One action per step. "Add SKU 41255 to the cart," not "add items and go to checkout."
- Include the exact values typed or selected — quantities, tender amounts, discount codes, dates.
- Mark the step where it goes wrong and say what appears instead: "→ tax line shows $0.00."
- Note anything already ruled out: "Same on a fresh browser profile." "Only special orders; regular orders are fine."

Ten specific steps beat four vague ones. Do not compress a path the engineer has to guess at.

**When it cannot be reproduced,** the section still carries weight — say what was tried and what is missing, so triage knows where to start rather than re-treading it:

```
NO
- Reported by aboffs over the phone; not reproduced in-house yet.
- Tried on my end: two special orders on the demo tenant, tax applied correctly both times.
- Haven't confirmed which register or whether it's every clerk. Waiting on a screenshot of the order.
```

**Worked example, YES case.** Reporter says: *"can't take a split payment — put half on card half on cash and it errors out. happens every time at the front register."*

Title: `[Bug] - Checkout fails on split cash/card payment`

```
### 🔁 **Can you reproduce? If so, list steps. If not, explain why.**
YES — every time, 4 for 4 attempts. Front register (POS), Chrome on Windows 11, clerk role.

1. Start a new sale at the POS.
2. Add SKU 41255, qty 1 ($48.00).
3. Tap Checkout, then Split Payment.
4. Enter $20.00 as Cash and tap Apply. → applies fine, $28.00 balance remains.
5. Select Card for the remaining $28.00 and swipe.
6. → Spinner hangs about 10 seconds, then "Payment could not be completed."
   Sale stays open and the $20.00 cash is still applied.

Full card payment on the same order works. Cash-only works. Same on a fresh
browser profile and a second register.
```

Note what that example does not do: no guess about the payment processor, no suggested fix. Just the path and the observation.

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
| Title | Yes | `[Bug] - <what's broken, where>`. Under ~70 chars. See "Write it the way the reporter would." |
| Client / subdomain | Yes | Which client subdomain(s) are affected, or all of them. Resolved to `Clients` relation pages — see below. |
| Expected Experience | Yes | What should happen. |
| Actual Experience | Yes | What is happening. |
| Reproducible? | Yes | YES / NO, plus frequency. If YES, setup + numbered steps with exact values — go into detail here. If NO, what was tried and what's still unknown. See "The reproduce section." |
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

Show the user the **actual title and body text**, verbatim, not a summary of it — plus the priority and resolved client(s). They are filing this under their name, so they need to read the words that will land in Notion and adjust the voice if it doesn't sound like them. Apply any edits and re-confirm.

If the draft has grown past one screen, cut it before showing it.

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
YES / NO — <frequency, e.g. "every time, 4 for 4">. <Where: POS/web, browser, role.>
1. <one action, with the exact value used>
2. <...>
3. → <what appears instead, at the step where it breaks>
<what was already ruled out, or — if NO — what was tried and what's still unknown>

### **📸 Screenshots / Recordings:** *(Attach any visuals that help clarify the issue)*
<screenshot URLs, or "None">

---

**🧩 Related Tickets / Context**
<related links, or "None">
```

Keep the emoji and heading wording as-is — reviewers scan for that shape. If `notion-fetch` on the template shows different headings, follow the template.

**Plain characters only in body text.** Notion escapes markdown-significant characters it finds mid-sentence, so they render with a visible backslash. Write the word instead:

| Don't write | Write |
|---|---|
| `~5K rows` | `about 5K rows` |
| `~2 min`, `~$40 off` | `about 2 min`, `about $40 off` |
| `qty 1 * $48.00` | `qty 1 at $48.00` |
| `order_id`, `snake_case` mid-sentence | wrap it in backticks |

The same goes for a stray `#`, `>` or `|` starting a line — it turns into a heading, quote or table. Backtick it or reword. Confirmed live on SUP-3100, where `(~5K rows)` published as `(\~5K rows)`.

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
- **No repro yet** → `NO`, plus what was tried and what is still unknown. Don't collapse it to one line; see "The reproduce section."
- **Bug came from an Intercom conversation** → the link goes in Related Tickets / Context; there is no `Intercom` property.
- **Bug originates from this conversation** → infer the fields from context, then confirm before creating.
- **Parser bug** → this database has separate `[Parser Bug] - [Issue]` and `[Parser Ticket] - [Request]` templates. If the bug is parser/data-ingestion related, say so and ask whether to use the parser template instead.
