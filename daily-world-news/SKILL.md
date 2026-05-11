---
name: daily-world-news
description: Daily world news newsletter — Semaform structure, regional status board, conflict trackers, escalation meter, Morning Brew markets strip, Axios-style analysis, Medium + Substack publish
---

---
name: daily-world-news
description: Daily world news newsletter — world-class design with Semaform structure, regional status board, conflict trackers, escalation meter
---

You are sending Manan's daily world news briefing via EmailJS. This is a world-class newsletter modelled on the best elements of Morning Brew (markets strip, scannability), Semafor (fact/analysis/disagreement structure), and Axios (tight "why it matters" callouts). Read every word before starting.

## PHILOSOPHY
A foreign correspondent who reads everything so Manan doesn't have to. Lead with narrative. Structure clearly. Separate facts from analysis. Show the India angle in everything. Make every visual element earn its place — no filler, no decoration for decoration's sake. This should feel like the FT, Axios, and Semafor had a baby that loves India.

---

## STEP 0 — DEDUPLICATION (run first)

Use `mcp__session_info__list_sessions` to find the last 4 sessions named "Daily world news". Use `mcp__session_info__read_transcript` on each (limit: 5 messages, most recent) to extract story titles/topics covered. Build COVERED_TOPICS list — never repeat any of these. Skip any search result that matches a covered topic.

---

## STEP 1 — RSS IMAGE FETCH (run in parallel with Step 2)

```python
import urllib.request, xml.etree.ElementTree as ET, json

feeds = [
    ("BBC World", "http://feeds.bbci.co.uk/news/world/rss.xml"),
    ("Reuters World", "https://feeds.reuters.com/reuters/worldNews"),
    ("Al Jazeera", "https://www.aljazeera.com/xml/rss/all.xml"),
    ("Guardian World", "https://www.theguardian.com/world/rss"),
    ("NDTV World", "https://feeds.feedburner.com/ndtvnews-world-news"),
]
namespaces = {'media': 'http://search.yahoo.com/mrss/'}
stories = []

for source, url in feeds:
    try:
        req = urllib.request.Request(url, headers={'User-Agent': 'Mozilla/5.0'})
        with urllib.request.urlopen(req, timeout=12) as r:
            root = ET.fromstring(r.read())
        for item in root.findall('.//item')[:5]:
            title = (item.findtext('title') or '').strip()
            link  = (item.findtext('link') or '').strip()
            image = None
            for tag in ['media:content', 'media:thumbnail']:
                el = item.find(tag, namespaces)
                if el is not None:
                    image = el.get('url'); break
            if not image:
                enc = item.find('enclosure')
                if enc is not None: image = enc.get('url')
            if title and link:
                stories.append({'source': source, 'title': title, 'link': link, 'image': image})
    except Exception as e:
        print(f"ERR {source}: {e}")

with_image = [s for s in stories if s['image']]
print(json.dumps(with_image[:8], indent=2))
print(json.dumps(stories[:15], indent=2))
```

Pick the best story with an image (not in COVERED_TOPICS). This is the hero image and lead story.

---

## STEP 2 — Research (5 parallel searches)

1. "[lead story] latest details analysis reactions [current month] 2026" — deep context on lead
2. "major geopolitics conflict war ceasefire diplomacy breaking news today 2026" — geopolitical events
3. "global markets Fed central banks oil gold dollar EM today [current date] 2026" — macro snapshot
4. "India foreign policy trade economy global impact news today 2026" — India angle
5. "underreported conflict region tension overlooked news May 2026" — the story nobody covered

---

## STEP 3 — Assess the world's current state

Before writing, make these determinations (they drive the visual elements):

**ACTIVE CONFLICTS** — List up to 4 ongoing conflicts/crises with their day count (e.g., Iran-US War Day 71, Ukraine Day 1535). These become the Conflict Tracker pills.

**REGIONAL STATUS** — Rate each of 5 world regions:
- 🔴 CRISIS (active conflict / humanitarian emergency)
- 🟠 ELEVATED (serious tensions / escalation risk)
- 🟡 WATCH (notable developments / diplomatic activity)
- 🟢 STABLE (calm / positive developments)

**MARKET MOOD** — One of: Risk-Off 🔴 / Cautious 🟠 / Neutral ⚪ / Risk-On 🟢

**ESCALATION LEVEL** for lead story (0–100, where 0=diplomacy, 100=open war) — drives the escalation meter bar width.

**KEY QUOTE** — Best quote from a world leader / diplomat / analyst today (for pull quote block).

---

## STEP 4 — Subject Line

Must BE the story. Not the newsletter name. Not the date.

Format: `[emoji] [8–12 word punchy sentence capturing the exact news]`

Good: "🕊️ Iran sends ceasefire response — Hormuz deal could unlock in 30 days"
Good: "📉 Fed holds, dollar surges, emerging markets take the hit"
Bad: "🌍 Your World Briefing — May 10, 2026" ← NEVER

---

## STEP 5 — Write the HTML Newsletter

Full dark-themed HTML email, ALL inline styles, max-width 640px. Every section must look visually distinct from the others. Use the exact HTML patterns specified below.

### DESIGN SYSTEM
```
Page bg:       #080c18     Content bg:    #0b1020
Card bg:       #111827     Deep dive bg:  #0e1628
India card bg: #0a1a12     Quote bg:      #13111f

Primary text:  #e2e8f0     Muted:         #64748b
Borders:       #1e293b

COLORS (use as accent borders, labels, highlights — not as full backgrounds):
Red/Crisis:    #f87171     Orange/Elevate:#fb923c
Amber/Watch:   #f59e0b     Blue/Markets:  #38bdf8
Green/India:   #4ade80     Purple/Analysis:#a855f7
Gold/Geo:      #fbbf24     Teal/Stable:   #2dd4bf

Font: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif
```

---

### SECTION A — MASTHEAD

```html
<!-- Masthead -->
<div style="padding:28px 36px 12px 36px;">
  <div style="font-size:28px;font-weight:800;color:#ffffff;letter-spacing:-0.5px;line-height:1;">The World Briefing</div>
  <div style="width:48px;height:3px;background:linear-gradient(90deg,#f59e0b,#f87171);margin:10px 0 8px 0;border-radius:2px;"></div>
  <div style="display:flex;justify-content:space-between;align-items:center;">
    <div style="font-size:11px;color:#64748b;text-transform:uppercase;letter-spacing:2px;">GEOPOLITICS · MARKETS · INDIA · WORLD</div>
    <div style="font-size:12px;color:#475569;">[FULL DATE e.g. Sunday, May 10 2026]</div>
  </div>
</div>
```

---

### SECTION B — CONFLICT TRACKER PILLS

Immediately below masthead, before hero image. Show up to 4 active conflicts as pill badges.

```html
<!-- Conflict Tracker -->
<div style="padding:8px 36px 20px 36px;display:flex;flex-wrap:wrap;gap:8px;">
  <!-- Repeat for each active conflict: -->
  <span style="display:inline-block;background:#1a0d0d;border:1px solid #f87171;border-radius:20px;padding:5px 14px;font-size:11px;color:#f87171;font-weight:600;letter-spacing:0.5px;">🔴 [CONFLICT NAME] · DAY [N]</span>
  <!-- For watch-level situations use amber: border:#f59e0b;color:#f59e0b;background:#1a1200 -->
</div>
```

If fewer than 2 active conflicts, include major ongoing diplomatic standoffs as 🟡 pills.

---

### SECTION C — HERO IMAGE

```html
<div style="padding:0 36px 24px 36px;">
  <img src="[RSS_IMAGE_URL]" alt="[Story description]" width="100%"
       style="width:100%;height:260px;object-fit:cover;border-radius:10px;display:block;" />
  <div style="font-size:11px;color:#475569;margin-top:6px;font-style:italic;">[Caption: what is shown] — Source: [outlet]</div>
</div>
```

Skip this section entirely if no RSS image was found.

---

### SECTION D — REGIONAL STATUS BOARD

This is the visual centrepiece. A world map broken into 5 regions with color-coded status. Use a table layout.

```html
<!-- Divider -->
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>

<!-- Regional Status Board -->
<div style="padding:22px 36px;">
  <div style="font-size:11px;color:#f59e0b;text-transform:uppercase;letter-spacing:2.5px;font-weight:600;margin-bottom:14px;">🌐 REGIONAL STATUS BOARD</div>
  <table width="100%" cellpadding="0" cellspacing="0">
    <!-- Header row -->
    <tr style="border-bottom:1px solid #1e293b;">
      <td style="font-size:10px;color:#475569;text-transform:uppercase;letter-spacing:1px;padding-bottom:8px;width:32%;">REGION</td>
      <td style="font-size:10px;color:#475569;text-transform:uppercase;letter-spacing:1px;padding-bottom:8px;width:20%;">STATUS</td>
      <td style="font-size:10px;color:#475569;text-transform:uppercase;letter-spacing:1px;padding-bottom:8px;">KEY DEVELOPMENT</td>
    </tr>
    <!-- One row per region. Status color: 🔴=#f87171 🟠=#fb923c 🟡=#f59e0b 🟢=#2dd4bf -->
    <tr>
      <td style="padding:10px 0;font-size:13px;color:#e2e8f0;font-weight:600;">🌎 Americas</td>
      <td style="padding:10px 0;">
        <span style="font-size:11px;font-weight:700;color:[STATUS_COLOR];">[🔴/🟠/🟡/🟢] [STATUS_WORD]</span>
      </td>
      <td style="padding:10px 0;font-size:12px;color:#94a3b8;line-height:1.5;">[One-liner on biggest development]</td>
    </tr>
    <!-- Repeat for: Europe, Middle East, Asia-Pacific, Africa -->
    <!-- Add a subtle border-top:1px solid #1a2035 to each row after the first -->
  </table>
</div>
```

---

### SECTION E — MARKETS STRIP

Morning Brew-style horizontal markets bar. Compact, number-first, scannable.

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:16px 36px;background:#0d1628;">
  <div style="font-size:10px;color:#38bdf8;text-transform:uppercase;letter-spacing:2px;font-weight:600;margin-bottom:10px;">MARKETS AT A GLANCE</div>
  <table width="100%" cellpadding="0" cellspacing="0">
    <tr>
      <!-- Each market indicator. ▲ = green, ▼ = red -->
      <td style="text-align:center;padding:0 8px 0 0;">
        <div style="font-size:10px;color:#64748b;margin-bottom:3px;">S&P 500</div>
        <div style="font-size:15px;font-weight:700;color:[▲=#4ade80/▼=#f87171];">[▲/▼] [VALUE]</div>
      </td>
      <td style="color:#1e293b;font-size:20px;">|</td>
      <td style="text-align:center;padding:0 8px;">
        <div style="font-size:10px;color:#64748b;margin-bottom:3px;">BRENT</div>
        <div style="font-size:15px;font-weight:700;color:[color];">$[VALUE]</div>
      </td>
      <td style="color:#1e293b;font-size:20px;">|</td>
      <td style="text-align:center;padding:0 8px;">
        <div style="font-size:10px;color:#64748b;margin-bottom:3px;">GOLD</div>
        <div style="font-size:15px;font-weight:700;color:[color];">$[VALUE]</div>
      </td>
      <td style="color:#1e293b;font-size:20px;">|</td>
      <td style="text-align:center;padding:0 8px;">
        <div style="font-size:10px;color:#64748b;margin-bottom:3px;">DXY</div>
        <div style="font-size:15px;font-weight:700;color:[color];">[VALUE]</div>
      </td>
      <td style="color:#1e293b;font-size:20px;">|</td>
      <td style="text-align:center;padding:0 0 0 8px;">
        <div style="font-size:10px;color:#64748b;margin-bottom:3px;">NIFTY 50</div>
        <div style="font-size:15px;font-weight:700;color:[color];">[VALUE]</div>
      </td>
    </tr>
  </table>
</div>
```

---

### SECTION F — HOOK

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:24px 36px;">
  <p style="font-size:16px;color:#e2e8f0;line-height:1.8;margin:0;">Good morning, Manan.</p>
  <p style="font-size:16px;color:#e2e8f0;line-height:1.8;margin:12px 0 0 0;">[2–3 sentences setting the scene — the tension, the shift, the mood of the world today. What is the underlying story beneath all the headlines? Not a summary — an interpretation. End with: "Here's what matters:"]</p>
</div>
```

---

### SECTION G — THE BIG STORY (Semaform Structure)

This is the structural heart of the newsletter. Separate fact from analysis from disagreement — exactly how Semafor does it.

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:26px 36px;">
  <div style="font-size:11px;color:#f87171;text-transform:uppercase;letter-spacing:2.5px;font-weight:600;margin-bottom:14px;">THE BIG STORY</div>
  <div style="font-size:22px;font-weight:800;color:#ffffff;line-height:1.3;margin-bottom:20px;">[HEADLINE — bold, max 12 words, punchy]</div>

  <!-- Escalation Meter (for conflict/crisis stories) -->
  <div style="margin-bottom:20px;">
    <div style="display:flex;justify-content:space-between;margin-bottom:5px;">
      <span style="font-size:10px;color:#64748b;text-transform:uppercase;letter-spacing:1px;">TENSION LEVEL</span>
      <span style="font-size:10px;color:#f59e0b;font-weight:600;">[e.g. HIGHLY ELEVATED]</span>
    </div>
    <table width="100%" cellpadding="0" cellspacing="0" style="border-radius:4px;overflow:hidden;">
      <tr>
        <!-- Fill [N]% based on your 0–100 assessment. Color: 0-30=#2dd4bf, 31-60=#f59e0b, 61-80=#fb923c, 81-100=#f87171 -->
        <td width="[N]%" style="background:[TENSION_COLOR];height:8px;"></td>
        <td style="background:#1e293b;height:8px;"></td>
      </tr>
    </table>
    <div style="display:flex;justify-content:space-between;margin-top:4px;">
      <span style="font-size:9px;color:#475569;">DIPLOMACY</span>
      <span style="font-size:9px;color:#475569;">OPEN WAR</span>
    </div>
  </div>

  <!-- THE NEWS — undisputed facts only -->
  <div style="margin-bottom:16px;">
    <div style="font-size:10px;color:#64748b;text-transform:uppercase;letter-spacing:1.5px;font-weight:700;margin-bottom:6px;border-left:2px solid #38bdf8;padding-left:8px;">THE NEWS</div>
    <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0;">[2–3 sentences of undisputed facts only. No interpretation. Who did what, when, where. Include hyperlinked source inline.]</p>
  </div>

  <!-- THE ANALYSIS -->
  <div style="margin-bottom:16px;">
    <div style="font-size:10px;color:#64748b;text-transform:uppercase;letter-spacing:1.5px;font-weight:700;margin-bottom:6px;border-left:2px solid #a855f7;padding-left:8px;">THE ANALYSIS</div>
    <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0;">[2–3 sentences of your interpretation. What is really happening beneath the headline? What power dynamics, incentives, or hidden agendas are at play?]</p>
  </div>

  <!-- WHY IT MATTERS — Axios-style callout -->
  <div style="background:#131c2e;border-radius:8px;padding:14px 18px;margin-bottom:16px;border-left:3px solid #f59e0b;">
    <div style="font-size:10px;color:#f59e0b;text-transform:uppercase;letter-spacing:1.5px;font-weight:700;margin-bottom:6px;">WHY IT MATTERS</div>
    <p style="font-size:14px;color:#e2e8f0;line-height:1.75;margin:0;">[2 sentences. Concrete consequences for markets, geopolitics, and specifically for India where relevant.]</p>
  </div>

  <!-- ROOM FOR DISAGREEMENT -->
  <div>
    <div style="font-size:10px;color:#64748b;text-transform:uppercase;letter-spacing:1.5px;font-weight:700;margin-bottom:6px;border-left:2px solid #64748b;padding-left:8px;">ROOM FOR DISAGREEMENT</div>
    <p style="font-size:14px;color:#94a3b8;line-height:1.75;margin:0;font-style:italic;">[1–2 sentences presenting a credible counternarrative, dissenting view, or what this story might be getting wrong. Never "bothsides" — just one honest counter.]</p>
  </div>
</div>
```

---

### SECTION H — PULL QUOTE

After the big story, place a pull quote from the most striking thing a world leader, diplomat, or analyst said today.

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:22px 36px;background:#13111f;">
  <div style="border-left:3px solid #a855f7;padding-left:20px;">
    <div style="font-size:24px;color:#a855f7;line-height:1;margin-bottom:8px;">"</div>
    <p style="font-size:16px;color:#e2e8f0;line-height:1.7;font-style:italic;margin:0 0 10px 0;">[The quote — exact words]</p>
    <div style="font-size:12px;color:#64748b;font-weight:600;">— [Name, Title, context]</div>
  </div>
</div>
```

---

### SECTION I — GEOPOLITICS WATCH (2 more stories)

Each story uses a compressed version of the Semaform structure.

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:26px 36px;">
  <div style="font-size:11px;color:#f59e0b;text-transform:uppercase;letter-spacing:2.5px;font-weight:600;margin-bottom:20px;">GEOPOLITICS WATCH</div>

  <!-- Story 2 -->
  <div style="margin-bottom:26px;">
    <div style="font-size:17px;font-weight:700;color:#ffffff;line-height:1.4;margin-bottom:12px;">[HEADLINE]</div>
    <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0 0 10px 0;"><strong style="color:#e2e8f0;">The news:</strong> [1–2 sentences of facts + source link]</p>
    <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0 0 10px 0;"><strong style="color:#e2e8f0;">The analysis:</strong> [2 sentences — what it means, who wins/loses]</p>
    <div style="background:#131c2e;border-radius:6px;padding:10px 14px;display:inline-block;">
      <span style="font-size:11px;color:#f59e0b;font-weight:700;">WHY IT MATTERS: </span>
      <span style="font-size:12px;color:#94a3b8;">[One tight sentence — the bottom line]</span>
    </div>
  </div>

  <div style="height:1px;background:#1a2035;margin-bottom:26px;"></div>

  <!-- Story 3 — same pattern -->
  <div>
    <div style="font-size:17px;font-weight:700;color:#ffffff;line-height:1.4;margin-bottom:12px;">[HEADLINE]</div>
    <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0 0 10px 0;"><strong style="color:#e2e8f0;">The news:</strong> [...]</p>
    <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0 0 10px 0;"><strong style="color:#e2e8f0;">The analysis:</strong> [...]</p>
    <div style="background:#131c2e;border-radius:6px;padding:10px 14px;display:inline-block;">
      <span style="font-size:11px;color:#f59e0b;font-weight:700;">WHY IT MATTERS: </span>
      <span style="font-size:12px;color:#94a3b8;">[...]</span>
    </div>
  </div>
</div>
```

---

### SECTION J — DEEP DIVE

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:26px 36px;">
  <div style="font-size:11px;color:#a855f7;text-transform:uppercase;letter-spacing:2.5px;font-weight:600;margin-bottom:14px;">DEEP DIVE</div>
  <div style="background:#0e1628;border-left:3px solid #a855f7;border-radius:10px;padding:22px 24px;">
    <div style="font-size:18px;font-weight:800;color:#ffffff;line-height:1.3;margin-bottom:20px;">[TITLE — the structural question this answers]</div>

    <div style="margin-bottom:16px;">
      <div style="font-size:11px;color:#a855f7;font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-bottom:6px;">What's actually happening</div>
      <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0;">[2–3 sentences of real context — not the headline version]</p>
    </div>
    <div style="margin-bottom:16px;">
      <div style="font-size:11px;color:#a855f7;font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-bottom:6px;">The second-order effects</div>
      <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0;">[2–3 sentences on downstream consequences — markets, alliances, India]</p>
    </div>
    <div>
      <div style="font-size:11px;color:#a855f7;font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-bottom:6px;">What to watch</div>
      <p style="font-size:14px;color:#cbd5e1;line-height:1.75;margin:0;">[2–3 sentences on the 3 specific signals that will tell you how this resolves]</p>
    </div>
  </div>
</div>
```

---

### SECTION K — INDIA LENS

Always present. Always find the India angle even if the story seems unrelated.

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:26px 36px;">
  <div style="font-size:11px;color:#4ade80;text-transform:uppercase;letter-spacing:2.5px;font-weight:600;margin-bottom:14px;">🇮🇳 INDIA LENS</div>
  <div style="background:#0a1a12;border-left:3px solid #4ade80;border-radius:10px;padding:20px 22px;">
    <p style="font-size:15px;color:#cbd5e1;line-height:1.8;margin:0;">[3–4 sentences. How do today's global events specifically affect India — trade, diplomacy, markets, oil, BRICS, geopolitics? What is India doing or signalling today? One specific risk and one specific opportunity for India. Always link source.]</p>
  </div>
</div>
```

---

### SECTION L — MARKETS & MACRO (narrative)

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:26px 36px;">
  <div style="font-size:11px;color:#38bdf8;text-transform:uppercase;letter-spacing:2.5px;font-weight:600;margin-bottom:14px;">MARKETS &amp; MACRO</div>
  <p style="font-size:15px;color:#cbd5e1;line-height:1.8;margin:0 0 16px 0;">[2–3 sentences of narrative — not just numbers. What story are markets telling? What are they pricing in / ignoring? What's the key macro tension right now?]</p>
  <!-- 3–4 bullet points: emoji + asset + one sentence -->
  <p style="font-size:14px;color:#94a3b8;line-height:2.2;margin:0;">
    🛢️ <strong style="color:#e2e8f0;">Crude (Brent)</strong> — [what's happening and why]<br/>
    📈 <strong style="color:#e2e8f0;">Equities</strong> — [what's happening and why]<br/>
    🪙 <strong style="color:#e2e8f0;">Gold &amp; Safe Havens</strong> — [what's happening and why]<br/>
    💱 <strong style="color:#e2e8f0;">Dollar &amp; EM FX</strong> — [what's happening and why, India rupee mention] <a href="[source]" style="color:#38bdf8;">Source</a>
  </p>
</div>
```

---

### SECTION M — UNDER THE RADAR

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:26px 36px;">
  <div style="font-size:11px;color:#64748b;text-transform:uppercase;letter-spacing:2.5px;font-weight:600;margin-bottom:14px;">🔍 UNDER THE RADAR</div>
  <p style="font-size:15px;color:#cbd5e1;line-height:1.8;margin:0;">
    <strong style="color:#fbbf24;">[TOPIC/COUNTRY]:</strong> [3 sentences — what's happening, why it matters structurally, why everyone missed it.] <a href="[source]" style="color:#38bdf8;">Source</a>
  </p>
</div>
```

---

### SECTION N — QUICK HITS

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:26px 36px;">
  <div style="font-size:11px;color:#64748b;text-transform:uppercase;letter-spacing:2.5px;font-weight:600;margin-bottom:14px;">QUICK HITS</div>
  <table width="100%" cellpadding="0" cellspacing="0">
    <!-- 5 hits, alternating background for scannability -->
    <tr style="border-bottom:1px solid #1a2035;">
      <td style="padding:10px 0;width:28px;vertical-align:top;">
        <span style="font-size:13px;">🌍</span>
      </td>
      <td style="padding:10px 0 10px 8px;font-size:13px;color:#94a3b8;line-height:1.6;">
        <strong style="color:#e2e8f0;">[COUNTRY/TOPIC]</strong> — [One sentence. Fact + implication.] <a href="[url]" style="color:#475569;font-size:11px;">[Source]</a>
      </td>
    </tr>
    <!-- Repeat for 4 more hits with appropriate emojis (🌎🌏🏛️📊⚡🔥) -->
  </table>
</div>
```

---

### SECTION O — FOOTER

```html
<div style="margin:0 36px;height:1px;background:#1e293b;"></div>
<div style="padding:20px 36px 32px 36px;">
  <p style="font-size:12px;color:#475569;line-height:1.9;margin:0;">
    Until tomorrow — your world desk &nbsp;·&nbsp; manankharbanda99@gmail.com<br/>
    Sources: [list 4–5 main outlets used as inline links in muted color #64748b]
  </p>
</div>
```

---

## STEP 6 — Send via EmailJS

```bash
python3 -c "
import json, pathlib
payload = {
    'service_id': 'service_izx75k5',
    'template_id': 'template_als8uks',
    'user_id': 'XQfRe_jpXZ4rbWjYO',
    'accessToken': 'p_3Qq6lPnsgp5WqZFLac1',
    'template_params': {
        'to_name': 'Manan',
        'to_email': 'manankharbanda99@gmail.com',
        'from_name': 'The World Desk',
        'subject': 'SUBJECT_HERE',
        'message': open('/tmp/worldnews.html').read(),
        'reply_to': 'manankharbanda99@gmail.com'
    }
}
pathlib.Path('/tmp/wn_payload.json').write_text(json.dumps(payload))
"

curl -s -X POST "https://api.emailjs.com/api/v1.0/email/send" \
  -H "Content-Type: application/json" \
  -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" \
  -d @/tmp/wn_payload.json
```

---

## STEP 7 — Confirm

- "OK" → log: ✅ World Briefing sent: [subject] | Hero image: yes/no | Conflict pills: [list] | Stories: [5–6 topics]
- Fails → log exact error, retry once.

---

## STEP 8 — Publish to Medium

After the email is confirmed sent, publish an editorial version to Medium. This is a **separate, cleaner format** — not the dark HTML email. It must feel like a proper foreign affairs article with real sourced hyperlinks on every factual claim.

### A. Build the Medium HTML

Write clean, Medium-compatible HTML using ONLY: `h2`, `p`, `blockquote`, `ul`, `li`, `pre`, `strong`, `em`, `a`, `hr`. **No inline styles. No divs.** Every factual claim MUST have an `<a href="[VERIFIED_URL]">` link to the actual article.

```html
<p><strong>The World Briefing · [FULL DATE e.g. Sunday, May 11 2026]</strong></p>

<p>[HOOK — rewritten for a public reader. NOT "Good morning, Manan." Lead with the most surprising or consequential thing happening in the world today. 2–3 sentences. Make it arresting.]</p>

<h2>🔴 [BIG STORY HEADLINE — max 12 words, punchy]</h2>

<p><strong>The news.</strong> [2–3 sentences of undisputed facts, with <a href="[SOURCE_URL]">inline source links</a> on every claim. Who did what, when, where.]</p>

<p><strong>The analysis.</strong> [2–3 sentences of interpretation. What is really happening beneath the headline? What power dynamics or incentives are at play?]</p>

<blockquote>[Most striking insight, implication, or tension from this story — stated as a direct claim, not a quote]</blockquote>

<p><strong>Why it matters.</strong> [2 sentences. Concrete consequences — for markets, for geopolitics, for India specifically. <a href="[SOURCE_URL]">Source</a>]</p>

<p><em>Room for disagreement: [1–2 sentences presenting a credible counternarrative or what this story might be getting wrong.]</em></p>

<h2>🌐 Geopolitics Watch</h2>

<p><strong>[Story 2 Headline].</strong> [2–3 sentences — the news, then the analysis, then why it matters. <a href="[URL]">Source</a>]</p>

<p><strong>[Story 3 Headline].</strong> [2–3 sentences with <a href="[URL]">source linked</a> inline. The news → analysis → bottom line.]</p>

<h2>🇮🇳 India Lens</h2>

<p>[3–4 sentences on how today's global events specifically affect India — trade, diplomacy, oil, BRICS, markets. One specific risk and one specific opportunity. Every claim sourced inline with <a href="[URL]">link</a>.]</p>

<h2>📊 Markets & Macro</h2>

<p>[2–3 sentences of market narrative — what story are markets telling today? What are they pricing in or ignoring?]</p>

<ul>
<li><strong>Crude (Brent):</strong> [what's happening and why] (<a href="[URL]">source</a>)</li>
<li><strong>Equities:</strong> [what's happening and why] (<a href="[URL]">source</a>)</li>
<li><strong>Gold & Safe Havens:</strong> [what's happening and why]</li>
<li><strong>Dollar & EM FX:</strong> [what's happening and why, India rupee] (<a href="[URL]">source</a>)</li>
</ul>

<h2>🔍 Under the Radar</h2>

<p>[3–4 sentences on the story everyone missed — what's happening, why it matters structurally, why it's underreported. Full sourced links: <a href="[URL]">outlet name</a>.]</p>

<h2>⚡ Quick Hits</h2>

<ul>
<li><strong>[Country/Topic]:</strong> [One sentence — fact + implication] (<a href="[URL]">source</a>)</li>
<li><strong>[Country/Topic]:</strong> [One sentence] (<a href="[URL]">source</a>)</li>
<li><strong>[Country/Topic]:</strong> [One sentence] (<a href="[URL]">source</a>)</li>
<li><strong>[Country/Topic]:</strong> [One sentence] (<a href="[URL]">source</a>)</li>
<li><strong>[Country/Topic]:</strong> [One sentence] (<a href="[URL]">source</a>)</li>
</ul>

<hr>

<p><em>The World Briefing is published daily at 9 AM IST — geopolitics, markets, and India analysis for people who want to understand the world, not just follow it.</em></p>

<p><strong>Sources:</strong> <a href="[URL]">[Outlet 1]</a> · <a href="[URL]">[Outlet 2]</a> · <a href="[URL]">[Outlet 3]</a> · <a href="[URL]">[Outlet 4]</a> · <a href="[URL]">[Outlet 5]</a></p>
```

Save this HTML to `/tmp/medium_wn.html`.

### B. Publish to Medium via browser

1. `mcp__Claude_in_Chrome__select_browser` — deviceId: `69880aa4-82b2-48e7-a1a0-9ad94ed26496`
2. `mcp__Claude_in_Chrome__tabs_context_mcp` (createIfEmpty: true) — get the tab ID
3. Navigate to a new draft (bypasses beforeunload dialog):
   ```javascript
   window.onbeforeunload = null; window.location.replace('https://medium.com/new-story');
   ```
4. Wait 3 seconds for editor to load
5. Click the Title field (near top centre of editor), type the article title — **subject line WITHOUT the leading emoji**
6. Press Enter twice to move cursor to body
7. Click once in the body area to focus the document, then write the HTML to clipboard:
   ```javascript
   (async () => {
     const html = `MEDIUM_HTML_CONTENT_HERE`;
     const blob = new Blob([html], {type: 'text/html'});
     await navigator.clipboard.write([new ClipboardItem({'text/html': blob})]);
     return 'done';
   })()
   ```
   ⚠️ The document MUST be focused (clicked) before running this — otherwise `NotAllowedError: Document is not focused` will be thrown.
8. Press `Ctrl+V` — wait 3 seconds for paste to complete
9. Verify "Saved" appears in the top bar. If you see "Something is wrong and we cannot save your story" — do NOT continue. Navigate to `medium.com/new-story` again and retry from step 5.

### C. Add a cover image

**Try the Unsplash picker first:**
1. Scroll to the very top of the editor
2. Click on the empty line above the first paragraph
3. Click the `+` button that appears on the left margin
4. In the toolbar, click the camera/📷 icon ("Add an image")
5. Look for an "Unsplash" tab — search for `[LEAD_STORY_KEYWORD]` (e.g. "geopolitics", "Iran", "Ukraine", "India")
6. Select the first high-quality, relevant result

**If Unsplash picker fails or is unavailable:**
- Do NOT leave a broken placeholder. Log in the confirm step: "Cover image needed — search '[KEYWORD]' on Medium's Unsplash picker"
- The article publishes fine without it; Manan can add later via the edit button

### D. Set topics and publish

1. Click the green **Publish** button (top right)
2. In the publish dialog, click "Add a topic..." and add these tags one at a time — type each tag, wait for the dropdown suggestion to appear, click it, then type the next:
   - `Geopolitics`
   - `World News`
   - `India`
   - `[Lead story specific tag, e.g. "Iran", "Ukraine", "Markets", "China"]`
3. **Uncheck "Paywall this story"** — must be free to read
4. "Notify your subscribers" — leave checked
5. Click the black **Publish** button
6. Confirm the "Your story has been published" modal

### E. Medium confirm
- Log: ✅ Medium published: [title] | URL: medium.com/p/[id] | Tags: [list] | Cover image: [yes — Unsplash / no — note search term]
- **Rate limit handling:** If Medium blocks publish with "You've reached your daily story limit" or similar — save as draft and log: ⚠️ Medium rate-limited — draft saved at [URL]. **Still proceed to Step 9 (Substack)** — do not stop.

---

## STEP 9 — Publish to Substack

Publish the same editorial article to Substack. Use `https://bytesizedbymanan.substack.com/publish/post/new`.

The HTML content is the same as Step 8's `medium_wn.html` — already written and saved to `/tmp/medium_wn.html`. Reuse it here.

### A. Open new Substack draft

1. `mcp__Claude_in_Chrome__select_browser` — deviceId: `69880aa4-82b2-48e7-a1a0-9ad94ed26496`
2. `mcp__Claude_in_Chrome__tabs_context_mcp` (createIfEmpty: true)
3. Navigate:
   ```javascript
   window.onbeforeunload = null; window.location.replace('https://bytesizedbymanan.substack.com/publish/post/new');
   ```
4. Wait 3 seconds for editor to load

### B. Set title and subtitle

1. `mcp__Claude_in_Chrome__find` — `textarea[placeholder="Title"]` — set via `mcp__Claude_in_Chrome__form_input`
   - Title: [article title — subject line WITHOUT the leading emoji]
2. `mcp__Claude_in_Chrome__find` — `textarea[placeholder="Add a subtitle…"]` — set via `mcp__Claude_in_Chrome__form_input`
   - Subtitle: `The World Briefing · [DATE] — Geopolitics, Markets & India`

### C. Paste content (HTML clipboard — preserves all hyperlinks)

CRITICAL: Use HTML clipboard, NOT plain text. Plain text strips all `<a href>` links.

1. Click once in the body/content area to focus the Substack editor
2. Run this JavaScript:
   ```javascript
   (async () => {
     const html = `FULL_SUBSTACK_HTML_WITH_ALL_LINKS_HERE`;
     const blob = new Blob([html], {type: 'text/html'});
     await navigator.clipboard.write([new ClipboardItem({'text/html': blob})]);
     return 'done';
   })()
   ```
   ⚠️ Document MUST be focused before running — otherwise `NotAllowedError` is thrown.
3. Press `Ctrl+V` — wait 3 seconds
4. Verify: links should appear as **clickable blue text** (e.g., "Source"), not raw URLs. If you see raw `https://...` text, the HTML blob failed — try Substack's source mode instead.

### D. Publish

1. Click **Continue** button (top right)
2. Confirm audience: **Everyone** (not just paid subscribers)
3. Click **"Send to everyone now"**
4. Capture the published Substack URL

### E. Substack confirm
- Log: ✅ Substack published: [title] | URL: [substack URL] | Links preserved: yes/no

---

## NON-NEGOTIABLE RULES

1. **COVERED_TOPICS check first.** Never repeat a story from the last 4 sessions.
2. **Subject line = the story.** Never date-based or generic.
3. **Conflict Tracker pills** must reflect actual current conflicts/crises — never generic placeholders.
4. **Regional Status Board** must be accurate to today's actual situation — never copy-paste from yesterday.
5. **Semaform structure** for The Big Story: NEWS → ANALYSIS → WHY IT MATTERS → ROOM FOR DISAGREEMENT. In that order. Always.
6. **Escalation meter** width must reflect your honest 0–100 assessment of tension level.
7. **Pull quote** must be a real, specific quote from today's news — not a paraphrase.
8. **India Lens** always present. Always find the angle. Even a seemingly unrelated story (e.g., Mali insurgency) has an India connection (Red Sea alt route, diaspora, peacekeeping, commodity prices).
9. **Markets strip and narrative** must use real estimated data — never "XX" placeholders in the final output.
10. **Write like a journalist.** Every sentence must earn its place. No filler. No "it remains to be seen." Interpret, don't just report.
11. **Both Medium and Substack must be attempted every run.** If Medium is rate-limited, save the draft and log it, then still publish to Substack. Substack is the guaranteed publish destination — never skip it.
12. **All hyperlinks must be clickable in both Medium and Substack.** Always use the HTML ClipboardItem approach (`new ClipboardItem({'text/html': blob})`), never plain text paste. Verify links appear as blue clickable text after paste.