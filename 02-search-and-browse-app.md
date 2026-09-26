# Stage 2: Search and browse app

A fast internal site the team uses to find and copy answers.

- **Search**: hybrid keyword (stemming, folding related word forms, stopwords that include question framing like
  "position", "stance") blended with embedding similarity. Short queries lean on keywords; long pasted questions lean
  on meaning. Published questionnaire answers first, with a "best match" flag above a tuned similarity threshold; then
  snippets weighted by source (candidate's own words highest, internal notes lowest), near-duplicates folded under
  "similar versions"; then the original Q&A threads.
- **Cards**: copy button, voice switch (I / He) in the header that changes cards and copied text, "converted from…" with
  a view-original toggle, verbatim quotes with a "play at m:ss" link into the recording.
- **Browse tabs**: all Q&As with filters; questionnaires as an expanding hierarchy (questionnaire > question > answer)
  with a note that only public questionnaires are included; forums with recordings and full transcripts
  (candidate-only toggle).
- **Source links everywhere**: the doc for logged replies, the recording (with timestamp) for forum answers, the
  published page for questionnaire answers. Say plainly when no public link exists.
- **Optional: an MCP server.** Expose the same search as an MCP server so staff can search the library from an AI
  assistant like Claude or ChatGPT while they write (e.g. "what has the campaign said about housing?"). Keep it a thin
  layer over the same search code, with the same rules: it sits behind the auth layer too, and it returns do-not-copy
  items labeled as internal guidance, never as quotable text. Ask the user whether they want this before building it.
- Deploy behind the auth layer; the same search code runs locally (Node) and in the worker (hosted embeddings from the
  same model, so thresholds carry over).
