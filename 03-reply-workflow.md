# Stage 3: Reply workflow

Turn the library into a team process for new inbound messages.

## Who it's for
These are the user stories we built for. Before building, walk through them with me: which roles this team actually
has, who approves, and whether one person covers several roles. Adjust the stories, then record them in AGENTS.md.

1. As anyone on the team, I want to paste a question (real or hypothetical) and get an AI draft in the campaign's
   voice.
2. As a social media monitor, I have a question from an email, DM, or comment that I need team input on. I want to
   paste it in and get people's input.
3. As a policy team member, I want to see voters' questions in context and draft proposed responses, with AI help.
4. As a senior policy team member, I want to edit others' drafts and approve responses to go out.
5. As a social media monitor, I want to see approved wordings and copy them back into the channel they came from.
6. As a campaign manager, I want a big-picture view of what messages are coming in, being answered, and going out.
7. As a policy team member, I want occasional feedback on how my drafts were received and what was edited before they
   went out.

## Building it
- **Paste to start**: a big "+ Conversation" button; paste the raw thread (as messy as it comes) and click Next.
  AI reads it into channel, sender (name, else handle), messages with who/when/from-campaign, a one-line "what they
  want", topics, and a search query. Show that progress at the top of the page. A bare question with no thread works
  too, for someone who just wants a quick draft (story 1); it can stay private and never be shared.
- **Private until shared**: a new draft lives only in the creator's browser (local storage) and uses stateless AI
  endpoints, so nothing about a constituent is stored server-side until someone clicks "Share with team". Sharing
  creates the database row, carrying over the parse, AI suggestion, and draft. Discard needs no confirmation.
- **Shared flow**: needs draft / needs review, then approve, then ready to post, then "copy & mark posted" (confirm
  the text that actually went out), plus no-reply-needed, reopen, and trash (confirmed soft delete that keeps
  history). Editing an approved reply sends it back for review.
- **Data**: a drafts table plus an append-only events table (created, parsed, AI draft, edit, comment, status,
  posted). Identity from the auth layer's email. Roles (monitor, drafter, approver) checked per action, assigned to match
  the stories we agreed on (everyone holding all of them is a fine start for a small team). Saves carry a version number; a stale save gets a conflict with "use theirs / keep mine".
- **Feed**: status filter pills, an open-items badge on the nav tab, polling every few seconds (slower off-tab).
  Comments per conversation. A history panel.
- **Overview** (story 6): a page for the campaign manager showing volume over time by channel and topic, how many are
  answered, deferred, or stuck, how long replies take, and which topics come up most, especially ones the library
  doesn't cover yet.
- **Drafter feedback** (story 7): the events table already records every edit, so show each drafter how their drafts
  changed on the way out: a diff between their draft and what was posted, plus any comments. A periodic digest or a
  "your recent drafts" view works; keep the tone of it as learning, not grading.
- **Editor**: one reply box. The AI's draft appears as ghost text inside it; Tab or "Accept AI draft" puts it in.
  Below the box, the AI's analysis: what the library doesn't cover, notes for the reviewer, which library items it
  used (clickable), and the model and voice, with a model picker and "Redraft".
- **Models**: put providers behind one structured-output interface (JSON-schema output; some newer models reject
  forced tool use, so use the provider's native structured-output mode). Offer a strong and a fast model per provider
  whose key is set; API keys as server secrets, never in the client.

## AI drafts
- **Pipeline**: search the library twice (with the one-line summary and with the search query), send the top
  questionnaire answers and snippets in the chosen voice, with do-not-copy items labeled INTERNAL GUIDANCE (follow,
  never quote). The model returns the reply, the ids it used, gaps, and cautions; drop cited ids it wasn't given.
- **Prompt**: start from the campaign's style guide, plus these rules, which came from real feedback:
  - Short by default (2-5 sentences; public comments 1-3). Open with the person's name; no sign-off.
  - Only positions in the library. Never another politician's or a party's position as the candidate's.
  - If there's no position, say it plainly ("The campaign doesn't have a position on X right now, but it's on our
    radar.") and ask what they think. No narrating caution ("we don't want to guess at his view", "the honest answer
    is"), and no padding the gap with loosely related positions or analogies.
  - When someone argues a detailed legal or technical case, don't litigate it in writing, don't concede a position is
    flawed, don't speculate about mechanisms: credit their effort, say the honest high-level thing, restate the
    commitment, offer a call. Put the concern in cautions for the reviewer.
  - Escalating to a call from a public comment: "We'd love to talk with you about this on the phone. Please DM us your
    number." Never ask for a number in a public thread.
- **Examples file**: keep a small list of replies the team liked (situation, reply, why it works) and include it in the
  prompt as a guide to tone and judgment, not as a source of positions.

Expect a few rounds of feedback on real messages once the team starts using it. When someone flags a bad draft, add a
rule or add their rewrite to the examples file, then re-run that exact conversation on two or three models and show
them the before/after.
