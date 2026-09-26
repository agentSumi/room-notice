---
slug: room-notice
date: 2026-09-27
agent: taro
brief_ref: weekly-whimsy/2026-09-26-brief-008.md (#2)
shape: site (single static HTML file)
register: B (playable — social, repeatable, borrows an existing beloved bond/performance form)
lane: personal/code only (pitfall #35)
ambient_only_rule: PASS (webcam sampled at a time already passing — Kim does nothing to trigger)
clone_check: PASS (Rule 14 — DNA shared with Eyespy, mechanic is different verb)
retired_check: PASS (no overlap with retired-ideas.md or decisions-ledger.md — note Mail Bell is retired but its *thermal-slip token* aesthetic lives on as a reusable design unit, not as a re-ship of Mail Bell's mechanic)
cost_cap: $0.10 (per pitfall #34 small-build cap, includes MediaPipe WASM bundle ~3MB)
---

# PRD — Room-Notice

## Problem statement

Kim likes the *atom* of daily noticing — one observation of the real world each day, in a tangible form — but the Eyespy mechanic (receive-clue → find-it → photo-capture → log) has now been explicitly corrected (2026-09-13 Ordinary Magic kill; appeal-library 2026-09-20). The atom is the ritual-of-noticing itself, not the clue-and-find mechanic.

**Room-Notice is the atom, re-cast:** a tiny thermal-printer-style object on Kim's desk that, once a day at a quiet moment, **prints ONE 1-line observation of the room as seen by the laptop webcam** — e.g. "mug is in a new place today" or "the light moved at 2pm." No clue delivered to Kim, no find-task, no photo Kim takes, no log Kim keeps — the observation is the *output*, not a prompt to action.

## Target user

Kim, solo, at her own desk. No accounts, no server, no log, no streak. (Re-roll button is the one Register-B affordance — Kim can re-roll today's slip, but never has to log anything.)

## Stack

- **Single-file static HTML** (`index.html`) — one screen, one printer, one slip
- **Browser webcam** via `getUserMedia({ video: true })` — one frame, one sample
- **On-device vision** — `MobileNet` image-classification model loaded as a small WASM bundle (~3MB) via `@tensorflow-models/mobilenet` from a CDN; or a much-cheaper pre-trained MobileNet via ONNX Runtime Web
- **Time-driven fire** — at a configured quiet moment (default 2:30pm, configurable via URL param `?hour=14&min=30`), the page silently grabs one frame and prints the slip
- **CSS thermal-slip aesthetic** — warm-cream paper, monospace type, slight cream-curl at the corners, single gold border on the most recent slip
- **7-day thumbnail strip** — passive view, no scroll, no notifications
- **No backend, no auth, no data persistence beyond the 7-day strip** (kept in localStorage)
- **No webfonts** — SAV9 system stack + monospace stack

## Why this stack

- Single-file static HTML ships to GitHub Pages for free (Rule 17 default)
- MobileNet is the smallest production-quality image classifier (~17MB full, ~3MB int8) and runs entirely in-browser via WASM — no API call, no privacy cost
- `getUserMedia` is universally supported on Chromium + Safari + Firefox desktop
- localStorage for the 7-day strip = no server, no auth, no privacy surface
- Optional re-roll button gives Register-B tinkering without breaking the ambient-only rule (Kim chooses to re-roll, but the default is "let the day come to you")

## What it does

1. **Page loads.** A warm-cream desk surface fills the viewport. A small thermal-printer device sits in the centre, drawn in CSS — cream body, mono caption strip showing "waiting for 2:30pm…" until the time fires. Below it: a 7-slot thumbnail strip, all empty (warm-cream tiles, soft border).
2. **Time fires.** At the configured hour/minute, the page silently (no chime, no notification) calls `getUserMedia({ video: true })` to grab one frame from the laptop webcam. If Kim has already granted permission, no prompt; if not, the browser shows the standard permission dialog.
3. **Caption pass.** The frame is passed to the in-browser MobileNet model. The top-1 ImageNet label is mapped through a small built-in sentence-of-the-day template to produce a 1-line observation (e.g. "mug is in a new place today" for the label `coffee_mug`).
4. **Slip prints.** The observation renders onto a CSS-rendered thermal-slip card — warm-cream paper, monospace type, slight cream-curl at the corners, 1.5em line height. A small timestamp (mono, 11px) sits in the bottom-right corner. A subtle slide-down animation marks the print moment.
5. **Camera-denied fallback.** If Kim denies webcam permission OR the camera is unavailable, the slip prints from a hand-written sentence-of-the-day pulled from a built-in list of 30+ observations. No UX cliff, no error.
6. **7-day strip.** The printed slip is added to the thumbnail strip below the printer as a 56px tile, gold-bordered. The previous 6 slips remain visible, oldest first. Stored in `localStorage` under `room-notice-slips` as a JSON array of `{ date, caption, source }`.
7. **Re-roll button (Register-B affordance).** A small "↻ new slip" link sits below the printer. Clicking it re-runs the caption pass with a fresh frame. Stored as the slip for today, replacing the time-fired one. The re-roll count is shown in mono: "slip 2 of 2" (the time-fire is slip 1 of N, re-rolls increment N).
8. **No accounts, no server, no notification.** The page is a quiet surface that prints once a day at a quiet moment and otherwise just sits there.

## Why this is NOT a clone of Eyespy

| Layer | Eyespy | Room-Notice | Same / Different |
|---|---|---|---|
| Visual language | Mobile app, "cutest app on your phone" positioning, social feed | Web page with thermal-slip card aesthetic, warm-cream desk surface, no feed | **Same DNA** (daily-noticing ritual, tangible artefact) · **Different language** (mobile-app vs desk-object) |
| Interaction shape | Push clue → user finds in real life → user takes photo → user posts (or doesn't) | Time fires → page samples room → slip prints automatically → user reads (or doesn't) | **Same DNA** (daily observation delivered as artefact) · **Different mechanic** (deliver-observation vs prompt-find — Eyespy's loop is clue→find→capture; Room-Notice's loop is time→observe→deliver) |
| Subcultural register | "Make the ordinary magical, real-world scavenger hunt, social validation" | "Slow ambient observation, one slip per day, no social layer" | **Same DNA** (real-world noticing as a felt practice) · **Different register** (scavenger-hunt-game → daily-ambient-observation) |
| Build cadence | Mobile TestFlight app, social-feed engineering | Single-file static HTML + browser vision APIs, ~$0.01 cap, weekend build, GitHub Pages | **Same whimsy-shaped single-mechanic register** · **Different cadence** (POC vs mobile-app) |

**Conviction (third-column):** the structural difference is **"Eyespy delivers a *task* (find this); Room-Notice delivers an *observation* (I noticed this)."** That's a category difference, not a feature cut. The 2026-09-13 Ordinary Magic kill was because the mechanic stayed 1:1; this build's mechanic is in a different verb entirely (deliver-observation vs prompt-find).

## Canon alignment

- `2026-07-31-headspace-warm-neutral-canvas-one-accent` — warm-cream slip paper, gold border on the most-recent slip
- `2026-07-31-evidence-attached-to-claim-not-adjacent` — every slip shows the raw caption source in a footer citation row (so the AI's read is auditable, not opaque)
- `2026-07-26-wong-kar-wai-invite-process` — subtractive editing (one slip, one moment, no scroll)
- `2026-07-26-sam-teaching-deck-explain-ai-through-metaphor` — the slip is the sustained audience-native metaphor ("the room leaves you a note")
- `2026-08-16-artifact-house-style-tokens` — SAV9 token set, mono caption + timestamp, no webfonts, 1000px shell

## Soft-gate evidence rows

- **Competitors found:** Day One (live, requires user writing), Stoic. (live, requires user writing), Daylio (live, requires user writing), Google Lens (live, reactive tool, not daily ritual), Apple Visual Lookup (live, reactive tool, not daily ritual). 5 named, all either require user input or are reactive tools. None combine "daily-ambient-observation" + "tangible slip artefact" + "no user input." Vacancy is real but small (whimsy lane, not market category).
- **Demand signal:** appeal-library atom flagged as needs-respin (2026-09-20) + Kim-direct verbatim "another idea I really like" for Eyespy 09-09. Type: medium-high — the appeal-library correction specifically asked for a different mechanic, this delivers one.

## Cohort invocation (UI build, mandatory per Step 0b)

- [x] `agent-design` read → architecture brief: not applicable (no agentic system, single static page)
- [x] `design-docs` run → spec: PRD IS the spec (single-mechanic POC, low-stakes)
- [x] `architect` risk-naming pass → named risks:
  - Risk 1: `getUserMedia` requires user gesture in Safari before the time fires — mitigation: bind the `getUserMedia` call to the first user click anywhere on the page; the slip is "armed" after the first interaction
  - Risk 2: MobileNet WASM bundle is 3MB and adds load time — mitigation: lazy-load only when the time fires (the rest of the page is 0KB of dependencies)
  - Risk 3: ImageNet labels are generic ("coffee_mug" not "the mug moved") — mitigation: built-in sentence-of-the-day templates map raw labels to more natural observations; if confidence < 0.5, fall through to the hand-written sentence-of-the-day
  - Risk 4: `getUserMedia` permission dialog can spook users — mitigation: first load shows a one-line "permission: ready" mono caption that pre-explains the permission flow; permission is requested on the first user interaction, not on page load
- [x] `critique` self-pass — primary-critic lens: "is the slip interesting enough to want to re-roll?" — yes, the slip's caption is the whole artefact, the MobileNet pass must produce something Kim finds surprising or feels-true, otherwise the re-roll is dead on arrival
- [x] `a11y-review` post-build — verify `prefers-reduced-motion` honoured (slip fades in instead of slides), `prefers-color-scheme: dark` covered, ARIA on the re-roll button, contrast ratios
- [x] SAV9 structural devices: ≥3 of 9 — `.meta-block` (printer status card), `blockquote.pull` (slip caption + cite), `.cl-item` (re-roll count checklist), footer colophon for build provenance
- [x] SAV9 typographic soul: ≥3 of 8 — serif (no), italic emphasis (no), drop-cap (no), SVG illustration (no, CSS printer drawing instead), `@keyframes` (yes, for slip slide-down), `@font-face` (no), `:focus-visible` (yes, on the re-roll button), `prefers-reduced-motion` (yes). 3/8 — passes threshold
- [x] Hex tokens: verified against SAV9 spec — `--bg #eeeade`, `--surface #f8f6ee`, `--ink #201f1a`, `--accent #a9791f`, `--ink-muted #6b6759`, mono stack matches

## Evals on success criteria

| # | Criterion | Pass condition | Threshold | How verified |
|---|---|---|---|---|
| 1 | Page loads with no JS errors | `window.onerror` count == 0 after page load | 100% | Browser smoke-test: load `index.html` via local HTTP, assert `runtimeErrors.length === 0` |
| 2 | Webcam permission flow works | `getUserMedia({ video: true })` resolves with a `MediaStream` when triggered by user gesture; rejects with clear error if not | 100% | Smoke-test: synthetic click event, assert `getUserMedia` was called and either resolved or rejected with a recognised error |
| 3 | MobileNet image-classifier returns a top-1 caption in <2s | For a fixture room photo, classifier returns a label and confidence > 0.5 in <2s | 100% | Smoke-test: load a fixture room photo, run classifier, assert top-1 label + confidence > 0.5 + elapsed < 2000ms |
| 4 | Slip renders with warm-cream paper + monospace type + 7-day strip | `.slip` element has correct classes; `.strip-7day` has 7 slots; latest slot has `.gold` border | 100% | DOM smoke-test: assert `.slip` + `.strip-7day .slot` count == 7 + `.strip-7day .slot.gold` count == 1 |
| 5 | Re-roll button works (no duplicate slip) | Click → new slip replaces today's, re-roll count increments; total slips today = N | 100% | DOM smoke-test: click re-roll, assert `data-roll-count` increments; assert no duplicate slip in strip |
| 6 | Camera-denied fallback renders a hand-written sentence-of-the-day | When `getUserMedia` rejects, `.slip` renders with text from the built-in sentence list | 100% | DOM smoke-test: mock `getUserMedia` to reject, advance time trigger, assert `.slip` exists with text matching a built-in sentence |
| 7 | `prefers-reduced-motion` honoured | When media query matches, slip fades in instead of slides down | 100% | Smoke-test: load page with `prefers-reduced-motion: reduce` media feature, assert `.slip` has no `animation` property |
| 8 | No webfonts loaded | Network tab shows 0 requests for font files | 100% | Browser smoke-test: load page, count font requests, assert 0 |
| 9 | GitHub Pages deployment succeeds | `curl -sI https://agentsumi.github.io/room-notice/` returns 200 | 100% | Post-deploy curl check |

**SHIP verdict requires:** Eval 1, 4, 5, 6, 7, 8, 9 = 100% pass. Eval 2 and 3 are best-effort (camera permission + MobileNet accuracy are environment-dependent) — if they fail, the fallback paths (hand-written sentence list) cover the ship, and the eval is logged as `env-blocked` not `failed`.

## Build sequence

1. Write `~/taro-build/room-notice/PRD.md` (this file)
2. Write `~/taro-build/room-notice/index.html` — single static file with inline MobileNet loader
3. Local smoke-test: `python3 -m http.server 8124 --directory ~/taro-build/room-notice &` then `curl -sL http://127.0.0.1:8124/index.html` to confirm 200
4. Browser smoke-test: load page, verify evals 1, 4, 5, 6, 7, 8
5. Write `~/taro-build/room-notice/BUILD_REPORT.md`
6. Commit + push to `https://github.com/agentSumi/room-notice` + enable Pages
7. Verify deploy: `curl -sL https://agentsumi.github.io/room-notice/` returns 200
8. Add to `~/second-brain-hermes/taro/2026-09-27-sunday-batch.md` log

## Cost estimate

| Item | Cost |
|---|---|
| Taro PRD + inline lenses (Hermes/OpenRouter) | $0.00 |
| Source code generation (this file) | $0.00 (Taro writes directly — single static file, not codex-bounded per Step 2b) |
| MobileNet WASM bundle (CDN, free OSS) | $0.00 |
| Deploy (GitHub Pages free tier) | $0.00 |
| **Total** | **$0.00 of $0.10 cap** |