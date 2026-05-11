---
name: daily-ai-newsletter
description: Daily AI newsletter — research, AI self-review, email + Medium + Substack publish, Drive content system log
---

You are running the daily AI Intel Drop newsletter pipeline for Manan Kharbanda (manankharbanda99@gmail.com). Execute every step fully — do not stop early.

## STEP 1 — DEDUPLICATION
Read the last 4 "daily-ai-newsletter" session transcripts using mcp__session_info__list_sessions and mcp__session_info__read_transcript (limit:5 per session). Extract all story topics/companies already covered. Do NOT repeat them.
Also read the Topic Archive sheet for extended dedup history:
https://docs.google.com/spreadsheets/d/1BdiApoKgI8pn-u2-z-eCD5puIUYi8XgCmoJ3Gq2RsjQ/edit
Use Chrome MCP: navigate to that URL, read the page text to extract all company/topic names already covered.

## STEP 2 — RESEARCH
Use WebSearch to find today's top AI stories (published in the last 24 hours). Find:
- 1 lead story (major technical development, product launch, or research result)
- 2-3 "Today in AI" stories
- 1 "From the Frontier" deep-dive (model/research/infra)
- 5 tools launched this week
- 4 quick hits
All stories must have real, verifiable source URLs. Every factual claim links to its source inline.

## STEP 3 — WRITE HTML EMAIL
Dark-themed HTML email. Style: bg #0f1117, text #e2e8f0, accent #4f8ef7. Structure:
- Hero image (real public URL from source or Unsplash)
- Hook paragraph (2-3 sentences, big picture framing)
- Lead story section (full coverage)
- Today in AI (3 stories, 2-3 sentences each + source link)
- From the Frontier (argument / why it matters / what to watch)
- Use This Today (1 actionable prompt in a code block related to lead story)
- 5 Tools This Week (styled cards)
- What's Blowing Up (4 bullets)
- Quick Hits (4 one-liners with links)
- Footer: "The AI Intel Drop is published daily at 9 AM IST"

## STEP 3B — AI SELF-REVIEW + EDITORIAL CHECKLIST
Before publishing anything, check:
- No topics repeated from last 4 issues
- No company/theme appears in more than 2 stories
- All factual claims have inline source links
- No vague hype words — replace "revolutionary/game-changing" with specific claims
- Headline under 12 words, no question marks
- "Use This Today" is copy-paste-ready
- Story diversity: at least 2 of: LLMs, Tools, Research, Safety, Business
- Reading time 4-7 minutes
Fix any failures before proceeding. Log what was fixed.

## STEP 4 — SEND EMAIL via EmailJS
Read C:\Users\manan\OneDrive\Documents\Claude\Scheduled\daily-ai-newsletter\SKILL.md for EmailJS credentials. Send via Python subprocess curl. Verify response is "OK".

## STEP 5 — CREATE 3 INFOGRAPHICS
Python + matplotlib. WHITE background, dark text. No emojis in titles (use plain ASCII). dpi=150.
- infographic_1.png: data viz for lead story (bar/donut chart with real numbers from the story)
- infographic_2.png: From the Frontier comparison/performance chart
- infographic_3.png: 5 Tools card grid (white cards, colored top stripe per tool, tool name + 1-line description)
Style: bg white (#fff), text #1a1a2e, 2-3 accent colors, font size 13+ for labels, tight_layout(pad=2.0)
Save to outputs directory.

## STEP 6 — PUBLISH TO SUBSTACK (PRIMARY — always completes)
Navigate to https://bytesizedbymanan.substack.com/publish/post/new
Set title via form_input on textarea placeholder="Title". Set subtitle via textarea placeholder="Add a subtitle…".

CRITICAL — Substack paste must preserve hyperlinks:
Write a clean version of the newsletter as HTML with ALL hyperlinks intact using <a href="URL">text</a> tags.
Click the body area to focus it, then set clipboard AS HTML:
  ```javascript
  (async () => {
    const html = `YOUR_SUBSTACK_HTML_WITH_ALL_LINKS_HERE`;
    const blob = new Blob([html], {type: 'text/html'});
    await navigator.clipboard.write([new ClipboardItem({'text/html': blob})]);
    return 'done';
  })()
  ```
Press Ctrl+V. Verify links appear as clickable text (not raw URLs) after paste — if not, the HTML blob approach failed; try pasting into Substack's source mode instead.

Insert 3 infographics inline:
  For each image:
  1. Place cursor at target position
  2. Click Image toolbar (~660,108) → wait 1s → click "Image" option (~599,154) → wait 1s
  3. find() "unnamed file input for inserting images" → use highest ref number
  4. file_upload with Windows path to infographic PNG
  5. Wait 3s
Click Continue → confirm Everyone audience → click "Send to everyone now".
Capture final Substack post URL.

## STEP 7 — PUBLISH TO MEDIUM (SECONDARY — attempt, skip if rate-limited)
Chrome MCP deviceId: 69880aa4-82b2-48e7-a1a0-9ad94ed26496. Get tab ID via tabs_context_mcp.
Navigate to https://medium.com/new-story (use JS: window.onbeforeunload=null; window.location.replace(url))
Write clean editorial HTML: h2/p/blockquote/ul/li/pre/a/hr/strong/em only. No inline styles, no divs. Every factual claim must have a real <a href="SOURCE_URL">inline hyperlink</a>.
Set clipboard AS HTML (not plain text) — this preserves all hyperlinks on paste:
  Click body area first to focus the document, then:
  ```javascript
  (async () => {
    const html = `YOUR_FULL_HTML_HERE`;
    const blob = new Blob([html], {type: 'text/html'});
    await navigator.clipboard.write([new ClipboardItem({'text/html': blob})]);
    return 'done';
  })()
  ```
Press Ctrl+V. Type title. Click Publish.
Tags via form_input on combobox: Artificial Intelligence, Technology, Cybersecurity, Data Science.
Uncheck paywall: JS checkboxes[0].click(). Publish via JS finding button with text "Publish".
Capture final Medium post URL.
**Rate limit handling:** If Medium shows "maximum of two stories in the past 24 hours" — save the draft, log ⚠️ Medium rate-limited — draft saved at [URL], and move on. Do NOT retry or stall. Substack is already live so the issue is complete.

## STEP 8 — UPDATE GOOGLE DRIVE CONTENT SYSTEM

### 8A — Upload infographics to Assets folder (Drive MCP)
Use mcp__b1b1eec1-4759-48a5-9583-0be56fca3466__create_file for each infographic:
- parentId: 1PJjEqS-b23pyaVynGvKL-9rAjLCcn4FT
- title: YYYY-MM-DD_infographic_1.png
- Read PNG as base64, set base64Content + contentMimeType: image/png + disableConversionToGoogleType: true

### 8B — Log to Publishing Log sheet
Navigate to: https://docs.google.com/spreadsheets/d/1-qZiPPmPFrAybtg8KOEjwtgn37YuvwCdEOyL6DeDDo8/edit
Click first empty row. Fill columns:
A: date (YYYY-MM-DD), B: issue number, C: headline, D: Medium URL, E: Substack URL,
F: email status (OK/FAIL), G: topics covered (comma-separated), H: review score (X/5 checks passed)

### 8C — Update Topic Archive
Navigate to: https://docs.google.com/spreadsheets/d/1BdiApoKgI8pn-u2-z-eCD5puIUYi8XgCmoJ3Gq2RsjQ/edit
Add one row per story: A: date, B: company/topic, C: category, D: one-line summary, E: source URL

## STEP 9 — LOG
Print: date, subject, Medium URL, Substack URL, email status, infographics count, review results, Drive log status.

## HARD RULES
- No paywall anywhere
- ALL hyperlinks must be real and clickable — use the HTML ClipboardItem approach (`new ClipboardItem({'text/html': blob})`), never plain text paste
- Email must confirm "OK" response
- **Substack post MUST reach a published URL before task ends — this is non-negotiable**
- Medium is a bonus: attempt it, but if rate-limited save draft and move on without failing the run
- Review checklist must complete — fix failures, do not skip
- Drive sheets must be updated before task ends
- If a step fails, log and continue