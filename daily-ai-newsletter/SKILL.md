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

Also identify: **one HOT TAKE** — a contrarian or non-obvious perspective on the lead story. This becomes the Substack Note and the hook line. It must be a specific claim, not a summary. Example: "Everyone is calling this a breakthrough. It's actually a sign the scaling era is ending." This is what makes people subscribe.

## STEP 3 — WRITE HTML EMAIL
Dark-themed HTML email. Style: bg #0f1117, text #e2e8f0, accent #4f8ef7. Structure:
- Hero image (real public URL from source or Unsplash)
- **Hook paragraph (CRITICAL — first 2 sentences must be the HOT TAKE, not a summary. Start with the most surprising/contrarian thing, not "Today in AI...")**
- Lead story section (full coverage)
- Today in AI (3 stories, 2-3 sentences each + source link)
- From the Frontier (argument / why it matters / what to watch)
- Use This Today (1 actionable prompt in a code block related to lead story — must be copy-paste ready, tested, specific)
- 5 Tools This Week (styled cards)
- What's Blowing Up (4 bullets)
- Quick Hits (4 one-liners with links)
- Footer: "The AI Intel Drop is published daily at 9 AM IST"

**Hook formula that works:** Lead with consequence, not event. Not "OpenAI released X" but "X just made [profession] obsolete — here's what they haven't told you yet."

## STEP 3B — AI SELF-REVIEW + EDITORIAL CHECKLIST
Before publishing anything, check:
- No topics repeated from last 4 issues
- No company/theme appears in more than 2 stories
- All factual claims have inline source links
- No vague hype words — replace "revolutionary/game-changing" with specific claims
- Headline under 12 words, no question marks
- "Use This Today" is copy-paste-ready and actually works
- Story diversity: at least 2 of: LLMs, Tools, Research, Safety, Business
- Reading time 4-7 minutes
- Hook paragraph starts with the HOT TAKE, not a summary
Fix any failures before proceeding. Log what was fixed.

## STEP 4 — SEND EMAIL via EmailJS
Read C:\Users\manan\OneDrive\Documents\Claude\Scheduled\daily-ai-newsletter\SKILL.md for EmailJS credentials. Send via Python subprocess curl. Verify response is "OK".

## STEP 5 — CREATE 3 INFOGRAPHICS VIA CANVA

Use the Canva MCP (mcp__01dda819-d5e2-4c5f-bc8e-e5e31beb7021) to generate professional, shareable graphics. These get embedded in Substack and shared as standalone visuals. They must look like something a top newsletter would publish — editorial, clean, bold.

### Infographic 1 — Lead Story Data Visual
Call mcp__01dda819-d5e2-4c5f-bc8e-e5e31beb7021__generate-design with prompt:
"Professional editorial infographic for an AI newsletter. Dark background #0f1117. Title: '[LEAD STORY HEADLINE - max 8 words]'. Show [REAL DATA from story — benchmark scores / model sizes / speed numbers / cost comparisons] as a clean horizontal bar chart or comparison table. Accent color #4f8ef7 (blue). White text. Bottom label: 'AI Intel Drop'. 1080x1080px. Clean, modern, no decorative elements."

### Infographic 2 — From the Frontier Insight Card  
Call generate-design with prompt:
"Bold insight card for AI newsletter. Dark background #0f1117. Large centered stat: '[BIGGEST NUMBER OR CLAIM from Frontier section]'. Below it: 3 bullet points of key context, white text 18px. Bottom: source outlet name in grey. Accent stripe color #f97316 (orange) on left edge. Clean editorial design. 1080x1080px. Font: bold, modern sans-serif."

### Infographic 3 — 5 Tools This Week Grid
Call generate-design with prompt:
"5-panel grid card for AI newsletter tools section. White background. Title top: '5 Tools This Week' in dark #0f1117 bold. 5 cards in grid: each card has colored top stripe (use different colors: blue, orange, green, purple, red), tool name in bold, one-line description. Clean card design with subtle shadows. 1080x1350px (portrait for mobile). Professional newsletter style."

For each:
1. Call generate-design → get design ID
2. Call export-design to get the PNG/JPG URL
3. Download to outputs directory as infographic_1.png, infographic_2.png, infographic_3.png using mcp__workspace__bash curl

If Canva MCP is unavailable or errors, fall back to Python matplotlib with these rules:
- White bg, no spines, horizontal bars, values on bars, dpi=180, tight_layout(pad=2.5)
- Colors: #4f8ef7 / #f97316 / #10b981, font size 13+minimum
- No emoji in titles

Verify all 3 files exist and are >10KB before proceeding.

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
  Click body area first to