# Stage 1: Assemble the library

Turn every source into structured, searchable records. Keep each source's raw form in the repo (where privacy allows)
and write a small deterministic loader or an extraction spec for each, so the library can be rebuilt.

## First, inventory the sources with me
Interview me about everything the campaign has already said, before building any loaders. Many people won't think of
all of these unprompted, so go through the list below and ask about each one:
- a shared doc or spreadsheet where replies to the public are logged;
- answers to questionnaires from advocacy groups, newspapers, or voter guides, and whether each was published;
- exports from a texting platform, email inbox, or social media DMs;
- recordings or transcripts of forums, debates, town halls, interviews, or podcasts (and for each, whether a
  transcript or captions already exist);
- the campaign website, platform or issues pages, press releases, and stump speech;
- canned responses, talking points, or internal guidance staff already use.

For each source, find out:
- where it lives and how you can get it (a file I send you, an export I have to run, a link, or a login you can't use
  yourself);
- roughly how big it is;
- whether it contains private information about constituents;
- whether it's the candidate's own words, staff writing for the campaign, or internal-only guidance;
- whether any of it is outdated, still a draft, or not for use in writing.

Also ask who the team speaks as when replying: as the candidate ("I support…"), as the campaign ("She supports…"),
or a mix depending on the channel or staffer. Then explain the voice-versions idea (step 5 below): every answer in the
library, whichever voice it was written in, becomes available in both, so a good answer the candidate gave in person
can be pasted into a campaign-voice reply without retyping. Most people haven't thought of this, and it tends to
change how they picture using the library, so make sure they hear it.

Then write the inventory into AGENTS.md, propose an order to work through the sources (richest and most on-record
first), and confirm it with me. The steps below cover the usual sources; adapt them to whatever we actually have, and
skip the ones we don't.

## Building the library

1. **Inbound/reply log doc** (usually the richest source). Export it (e.g. pandoc to markdown, keeping tracked changes
   and comments), split by date heading, and extract per chunk with a written spec into:
   - *threads*: date, channel, sender, inbound text, the reply, reply kind (answered / deferred / pleasantry / none),
     topics, internal notes;
   - *snippets*: reusable answers with topic, title, text, source type (reply, canned template, internal note),
     example questions, keywords.
   Expect the same exchange to be logged several times under different dates: merge same-person copies that share a
   reply, and show "also logged on…". Watch for staff comments embedded as doc comments; they're internal notes.
2. **Published questionnaires**: parse deterministically, verbatim, one record per question, with a public source URL
   per questionnaire (or per question when it was published as several articles). These are on-record positions and
   should rank first in search.
3. **Text-message platform export**: keep only substantive candidate replies. Detect mass blasts (same outbound text,
   after stripping the personalized greeting, sent to 5+ contacts) and drop them. Staff-written replies signed by staff
   or in third person are "campaign text", not the candidate's words. Use the campaign's local time zone for dates.
4. **Forums and debates**: get transcripts, cheapest route first.
   - **Look for an existing transcript**: a transcript link shared by the organizer or a meeting tool (grab it with a
     headless browser), or captions on the video page.
   - **Transcribe only if you have to.** Transcription with speaker labels ("diarization") means signing up for a paid
     service, getting an API key from the user, and often downloading recordings by hand. It's worth it for a few
     important forums, not for everything. Before starting, tell the user which recordings you'd transcribe, the total
     minutes, and the estimated cost, and let them pick. To keep costs down, extract the audio and trim it to the
     candidate's panel. If voices get merged, re-run with the expected speaker count.

   Map speakers from self-introductions and the moderator's hand-offs, and have the user confirm the mapping
   before extracting. Store each candidate answer with the moderator's question, plus snippets that carry both a cleaned
   `text` and a verbatim `quote` with timestamp. Fix name misspellings only inside [brackets] in quotes.
5. **Voice versions** (a clever bit, and one of the most useful features of the tool): staff sometimes speak as the
   candidate ("I") and sometimes as the campaign ("He/She"), and the sources mix both. Generate both versions of every
   snippet and answer, changing only pronouns and verb agreement, so any answer works in either voice. The voice switch
   in Stage 2 and the voice choice for AI drafts in Stage 3 both depend on this. Store a hash of the source text
   so a changed snippet drops its stale conversion and lands in a pending list for re-conversion.
6. **Restricted list**: a file mapping ids to a reason ("draft", "phone only", "superseded") for do-not-copy items.
7. **Build step**: merge everything into one JSON library plus embeddings (a small local embedding model is plenty).
   Commit the build output so deploys don't need every raw source.

AI-written steps (extraction, voice conversion) can be done by the agent in-session against a written spec; keep the
spec files so re-running on new material gives consistent results.
