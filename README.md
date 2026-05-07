# Scaler AI Sales Intelligence

## What I built

An AI-powered pre/post-call sales intelligence tool for Scaler BDAs, delivered over WhatsApp. The pre-call flow generates a personalised WhatsApp briefing for the BDA from the lead's profile — who they are, what angles will resonate, objections to expect, and an exact opening hook — before the call happens. The post-call flow accepts the call transcript or audio recording, runs a Curriculum Agent that searches scaler.com live for accurate program data, then generates a personalised trust PDF for the lead that directly addresses their open questions from the call. Every lead-facing send goes through a BDA approval gate. Three agents in sequence: Intel → BDA Nudge (pre-call), then Curriculum (web search) → Lead Content (post-call). Both PDFs land on WhatsApp via file hosting. Built as a single deployable HTML file — no backend, no infrastructure.

---

## One failure I found

**Input:** Karthik Iyer persona, post-call transcript path.

**What happened:** The Curriculum Agent's web search returned generic Scaler marketing copy rather than specific AI Engineering module names and instructor credentials. The Lead Content Agent filled the "evidence" fields with hedged language ("Scaler's curriculum is designed around...") rather than concrete module names like "RAG pipelines" or "production evals." Karthik's persona specifically calls out academic fluff — so a document that sounds like marketing copy is exactly the wrong output for him.

**Why it happens:** The web search tool retrieves scaler.com pages that are conversion-optimised, not curriculum-deep. The agent needs to be directed to hit the course syllabus URL specifically, not the homepage. Fix is a targeted URL in the search prompt rather than a broad query.

---

## Scale plan

At 100,000 leads/month, two things break first.

**API rate limits.** Each lead runs 3–4 Anthropic API calls plus a web search. At 100k/month that's ~400k model calls. The current single-key browser architecture hits rate limits within hours. Fix: a thin backend that pools keys, queues requests, and handles retries — the frontend stays identical.

**The in-browser API key model.** Exposing the Anthropic key client-side is fine for a demo but not for a BDA team of 200 people. At scale, the keys move to a backend, the frontend sends lead data to an endpoint, and the response comes back signed. The HTML shell stays the same; only the fetch targets change.

File hosting (file.io, 24hr expiry) would also need replacing with S3 or equivalent at volume, but that's a one-line URL swap, not an architecture change.
