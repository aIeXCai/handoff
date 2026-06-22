# handoff

> From "what's this project about?" to "let's ship" — context handoff in under 2 minutes.

[中文文档 / Chinese Documentation](README.zh-CN.md)

---

`handoff` is a **Claude Code Skill** that auto-generates and maintains a structured `HANDOFF.md` for any project. When you start a new session, Claude reads this file and is instantly aligned — no manual briefing needed.

---

## Why?

| Pain Point | How handoff fixes it |
|---|---|
| Explaining your project from scratch in every new session | 7 fixed sections, readable in under 2 minutes |
| Verbal explanations eat into the context window | One file carries all the essential facts, zero token waste |
| Team knowledge scattered across different people's heads | Facts written to a file — anyone who opens it is aligned |
| Stale information as the project evolves | One-command re-scan picks up the latest state, preserves user judgments |

---

## Core Design

### 3 Principles

- **Short** — 7 fixed sections, each ≤ 5 lines. Read the whole thing in 2 minutes.
- **Accurate** — Every claim traceable to a commit, issue, or `file:line`. No guesses.
- **Repeatable** — Stable across runs. It scans code + asks you. No hallucination.

### 7 Fixed Sections

| # | Section | Content |
|---|---|---|
| 1 | **Identity** | What the project is, who it's for, core value (1-2 sentences) |
| 2 | **Tech Stack** | Each layer's choice and rationale (table) |
| 3 | **Structure** | Directory tree, depth ≤ 2 |
| 4 | **Data Model** | ER-level granularity — entities + relationships, no field expansion |
| 5 | **Key Decisions** | Each tagged with commit hash / issue link / code coordinate |
| 6 | **Startup & Verification** | Startup commands + the human-confirmed "success indicator" |
| 7 | **Pitfalls** | "Don't do X, because Y" format |

---

## Workflow

```
Scan the project → Ask the user (3 rounds) → Generate → Review diff → Write file
```

### The 3-Round Dialogue

| Round | What it does | Why |
|---|---|---|
| **1st** | Confirm scanned facts, fill in identity/pitfalls/success indicator | The machine can scan code, but it can't know *why* |
| **2nd** | Review old decisions & pitfalls, batch keep/modify/delete | Some entries go stale as the project evolves |
| **3rd** | Confirm the manual notes section | User-curated notes are never auto-overwritten |

---

## Install & Use

### Install

```bash
npx skills install handoff
```

### Usage

```
/handoff
```

Or just say in chat: `handoff`

### When to Use

- A long session is nearing the context limit — you want to "save and carry" the project state
- Starting a fresh session and want to get aligned fast
- The project structure has changed significantly
- A new teammate needs a quick overview of the project

---

## Example Output

The generated `HANDOFF.md` looks like this:

```markdown
## 1. Identity
Grit Mini-App — a workout logging tool for CrossFit/Hyrox enthusiasts.
Core value: structured plans + subjective feedback replace messy fitness logs.

## 2. Tech Stack
| Layer | Choice | Rationale |
|---|---|---|
| Frontend | WeChat Native + WXML | Mini-program ecosystem default |
| Backend | WeChat Cloud Development | Serverless, CloudBase integration |
| Data | CloudBase Document DB | Native integration with cloud functions |

## 3. Structure
src/
├── pages/          # Pages
├── components/     # Shared components
├── utils/          # Utility functions
└── cloudfunctions/ # Cloud functions

## 4. Data Model
- User → Checkin: one-to-many
- Plan → Exercise: one-to-many

## 5. Key Decisions
- Embed checkin in a single doc rather than a sub-collection → commit a3f2b1c
- Lazy-create checkin records → src/utils/checkin.js:42

## 6. Startup & Verification
- `npm run dev` → You should see the plan list on the home page in WeChat DevTools

## 7. Pitfalls
- Don't manually modify the `date` field on checkin → CloudBase triggers depend on its index
- Don't run heavy queries inside `onShow` → it fires on every page switch in mini-programs

## Manual Notes
<!-- handoff:manual-zone -->
(User-curated notes — never auto-overwritten by the skill)
<!-- /handoff:manual-zone -->
```

---

## vs. Alternatives

| | handoff | README.md | CLAUDE.md | Verbal |
|---|---|---|---|---|
| **Maintained by** | AI + human review | Human | Human | Human |
| **Update frequency** | One-command refresh | Occasional | Manual | Every time |
| **Scope** | Facts + decisions + pitfalls | Project intro | AI behavior rules | Anything |
| **Traceability** | Every claim → code coordinate | Not required | Not required | None |
| **Best for** | AI-assisted dev handoff | Human-facing intro | AI behavior config | One-off explanations |

---

## License

MIT

---

*Made for [Claude Code](https://claude.ai/code) — the agentic coding CLI from Anthropic.*
