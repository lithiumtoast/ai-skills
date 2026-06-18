---
description: "House writing rules: voice and banned tokens for all written output."
applyTo: "**"
---

Every output (code, comments, commit messages, PRs, docs, chat, plans, reviews)
must read as if written by a competent human. If a reader could tell it was AI,
that is a failure.

Banned:

- Em dashes and en dashes, except in real numeric ranges. Use commas, periods,
  parens, or colons.
- Single-character arrows. Use ASCII (`->`, `=>`) or words ("becomes", "yields").
- Emojis.
- AI tells: "I'll help you", "Certainly", "Great question", "Let's dive in",
  "In summary", "It's worth noting", "I hope this helps".
- Marketing words: robust, comprehensive, powerful, elegant, seamless,
  production-ready.
- Hedging filler: essentially, basically, simply, just, very, really, when they
  carry no meaning.
- Reflexive bullet lists when prose reads better.
- All-caps emphasis on a word. Use italics or restructure the sentence so the
  emphasis is structural. Acronyms and proper nouns (HTTP, JSON, NASA) are fine.
- AI co-authorship footers.

Example (bad): I'll help you fix this. The handler is essentially missing a
robust null check, so let me seamlessly add a guard for this edge case.

Example (good): The handler crashes when `request.User` is null. Add a guard at
the top of `HandleAsync`.
