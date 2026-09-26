# Prompt: build a campaign comms answer library and reply workflow

Paste the project brief below the line into a coding agent at the start of the project. The agent will begin by
interviewing you about the campaign, the team, and what you need. Then hand it the stages one at a time; each stage
ends with something the team can use.

1. [Assemble the library](01-assemble-the-library.md)
2. [Search and browse app](02-search-and-browse-app.md)
3. [Reply workflow](03-reply-workflow.md)

Optional: [deployment tips](deployment-tips.md). We recommend Cloudflare (Workers, D1, Workers AI, Access).

---

You're helping build an internal tool for a political campaign's communications team. Staff answer a steady stream of
questions from the public (social media DMs and comments, email, texts, forum audiences). Today those answers live in a
long shared doc, in people's heads, and in recordings. The goal: make everything the campaign has already said
searchable and copy-ready, then give the team a shared workflow to draft, review, approve, and post new replies, with
AI help grounded only in what the campaign has actually said.

We'll work in three stages, which I'll give you one at a time: assemble the library, a search and browse app, and a
reply workflow with AI drafts.

## Start by interviewing me
Before writing any code, interview me about the campaign and what we need. Ask a few questions at a time, skip anything
I've already answered, and suggest a sensible default when I'm unsure. Cover:
- **The campaign**: candidate, office, state, and the time zone we work in.
- **The team**: who answers messages, who approves replies, roughly how many people need access, and where the team
  talks (e.g. WhatsApp or Slack).
- **The inbound**: which channels questions arrive on (DMs, public comments, email, texts, forums), roughly how many per
  week, and what's painful about answering them today.
- **Hosting and login**: whether we already have an account, domain, or preference. If I have none, recommend
  Cloudflare with Cloudflare Access (see deployment-tips.md) and explain it in plain terms.
- **AI providers**: which model providers we have, or can get, API keys for.
- **Style**: whether the campaign has its own drafting instructions or a style guide for replies. Ask me to paste it.
- **Priorities**: which stages matter most, and any deadline (a debate, a filing date, election day).

Don't ask about sources in detail yet; Stage 1 starts with that. When the interview is done, summarize what you
learned in AGENTS.md alongside the non-negotiables below, and confirm the summary with me.

## Non-negotiables (write these into an AGENTS.md / CLAUDE.md on day one and keep it current)
- **Private by default.** The data includes constituent names, contact details, and internal staff comments. The repo
  stays private; raw exports with every contact stay out of git.
- **Nothing reachable outside the auth layer.** Every request, including static files and data, is verified server-side
  (e.g. check the Access JWT in the worker and fail closed). Turn off any preview/default URLs that bypass auth. No
  bypasses "just for testing".
- **Never attribute someone else's words to the candidate.** In debate/forum transcripts only the candidate's turns
  become quotable material; opponents appear only in the full transcript.
- **Internal guidance is never copyable.** Internal notes and anything marked draft / "phone only" / "don't put in
  writing" render as clearly labeled do-not-copy cards: searchable, but no copy button and no rewording.
- **Don't invent content.** Snippets stay close to source wording; verbatim fields are verbatim; AI drafts may only use
  positions found in the library.
- Before anything outward-facing (deploys, uploading secrets, contacting services), confirm with the user unless they've
  said to go ahead.

## Working style
- Keep AGENTS.md current: layout, commands, non-negotiables, gotchas learned the hard way, open items.
- Commit after each change with a clear message. For small UI tweaks, deploy and ask the user to check rather than
  building test harnesses; screenshot-check larger UI changes locally with a headless browser.
- Report what you verified and what you didn't.
