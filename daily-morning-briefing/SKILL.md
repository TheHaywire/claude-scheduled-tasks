---
name: daily-morning-briefing
description: Morning briefing 7:37 AM IST — Indian markets, day trade ideas, schedule. MT5 handled by evening briefing.
---

You are running the MORNING briefing for Manan Kharbanda (manankharbanda99@gmail.com). Focus: Indian markets, day trading setup, schedule. MT5/international is covered by the separate evening briefing at 7 PM.

---

## MANAN CONTEXT

**Trading:** NSE F&O (Nifty/BankNifty, 9–10 AM IST). OpusTradingSystem on MT5 (US500Cash, Gold, GER40, USDCHF) — evening session.
**F&O rules:** No trades 9:15–9:30 AM or 3:15–3:30 PM. Max 2 open positions. Half-Kelly. 2 losses = stop.
**JARVIS v4 build priority:** SENTINEL → EXECUTOR → SCOUT+REGIME → DARWIN+ALPHA → ARCHITECT → REVIEWER+EVOLVE. Tuesday = JARVIS day, one module only.
**Businesses:** Magic in Mohmaya (wife's Ayurvedic brand). The Homoeo Clinic.
**Personal:** Recently married. Us Time 8–9:30 PM sacred. Shambhavi Kriya (7 AM) and Music non-negotiable.

---

## CREDENTIALS
- EmailJS: service_id=SERVICE_ID, template_id=TEMPLATE_ID, public_key=EMAILJS_PUBLIC_KEY, private_key=EMAILJS_PRIVATE_KEY
- Discord: bot=DISCORD_BOT_TOKEN, channel=DISCORD_CHANNEL_ID

---

## STEP 1 — FETCH DATA

**Calendar:** `list_events` (Google Calendar MCP), today, Asia/Kolkata, ordered by startTime.

**Indian markets:**
- `get_ltp` (CASH, index): "Nifty 50", "Bank Nifty", "Nifty IT", "Nifty Pharma", "Nifty Auto", "Nifty PSU Bank", "Nifty FMCG"
- `fetch_market_movers_and_trending_stocks_funds`: TOP_GAINERS, TOP_LOSERS, VOLUME_SHOCKERS, STOCKS_IN_NEWS (size=5)

**Global macro:** WebSearch — "top financial market news today [date]" + "S&P 500 overnight [date]" for macro context only (not detailed international prices — that's the evening briefing's job).

**Crypto:** Deribit MCP `get_index_price` for "btc_usd" and "eth_usd".

---

## STEP 2 — ANALYSE

**Macro tone:** RISK-OFF / RISK-ON / MIXED + one-sentence mood_line.

**Indian F&O + Equity trade ideas (4–5 total):**
- 2 Indian F&O setups (Nifty/BankNifty CE/PE or futures). Real setups only. Embed F&O rules.
- 2 Indian equity swings from movers/shockers. Volume + catalyst.
- 1 wildcard (sector play, defence/pharma/PSU theme, etc.)
Each: symbol, venue, direction, type (long/short/watch), price, entry, target, stop, rationale (2 sentences), risk, RM note.

**News (5):** Real market-moving headlines. Each with title + one-line market impact.

**JARVIS focus (Tuesdays only):** One specific, implementable module. Precise function names, wiring. One module, one session.

---

## STEP 3 — BUILD AND SEND

Construct and execute this Python script with your data filled into the `data = {...}` dict. The render() function is LOCKED — never modify it.

```python
import json, subprocess
from datetime import datetime
from zoneinfo import ZoneInfo
IST = ZoneInfo('Asia/Kolkata')
now = datetime.now(IST)
is_tuesday = now.weekday() == 1

def render(data):
    def chg_color(v):
        try:
            s = str(v).replace('%','').replace('+','').replace('−','-').replace('–','-')
            return '#4eca7f' if float(s) >= 0 else '#f45e6a'
        except: return '#888'
    def chg_arrow(v):
        try:
            s = str(v).replace('%','').replace('+','').replace('−','-').replace('–','-')
            return '▲' if float(s) >= 0 else '▼'
        except: return '–'
    def idea_card(i):
        colors = {'long':'#4eca7f','short':'#f45e6a','watch':'#f4d14e'}
        tag_bg = {'long':'#0d2a1a','short':'#2a0d0d','watch':'#2a2a0d'}
        bc=colors.get(i.get('type','watch'),'#888'); tbg=tag_bg.get(i.get('type','watch'),'#1a1a1a'); tfc=colors.get(i.get('type','watch'),'#888')
        return (f'<div style="background:#111118;border-left:4px solid {bc};border-radius:0 10px 10px 0;padding:14px 16px;margin-bottom:10px">'
                f'<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:10px">'
                f'<div><span style="font-size:14px;font-weight:800;color:#f0f0f8;font-family:Arial">{i["symbol"]}</span>'
                f'<span style="font-size:10px;color:#5a5a7a;background:#1a1a28;padding:2px 8px;border-radius:4px;margin-left:8px;font-family:Arial">{i["venue"]}</span></div>'
                f'<span style="font-size:10px;font-weight:800;background:{tbg};color:{tfc};padding:3px 10px;border-radius:4px;font-family:Arial">{i["direction"]}</span></div>'
                f'<table width="100%" cellpadding="0" cellspacing="0" style="margin-bottom:10px"><tr>'
                f'<td width="25%" style="padding:2px 4px 2px 0"><div style="background:#1a1a28;border-radius:6px;padding:7px 8px"><div style="font-size:9px;color:#444466;font-weight:700;text-transform:uppercase;font-family:Arial">Price</div><div style="font-size:12px;font-weight:800;color:#e0e0f0;margin-top:2px;font-family:Arial">{i["price"]}</div></div></td>'
                f'<td width="25%" style="padding:2px 4px"><div style="background:#1a1a28;border-radius:6px;padding:7px 8px"><div style="font-size:9px;color:#444466;font-weight:700;text-transform:uppercase;font-family:Arial">Entry</div><div style="font-size:12px;font-weight:800;color:#e0e0f0;margin-top:2px;font-family:Arial">{i["entry"]}</div></div></td>'
                f'<td width="25%" style="padding:2px 4px"><div style="background:#0d2010;border-radius:6px;padding:7px 8px"><div style="font-size:9px;color:#2a6a2a;font-weight:700;text-transform:uppercase;font-family:Arial">Target</div><div style="font-size:12px;font-weight:800;color:#4eca7f;margin-top:2px;font-family:Arial">{i["target"]}</div></div></td>'
                f'<td width="25%" style="padding:2px 0 2px 4px"><div style="background:#200d0d;border-radius:6px;padding:7px 8px"><div style="font-size:9px;color:#6a2a2a;font-weight:700;text-transform:uppercase;font-family:Arial">Stop</div><div style="font-size:12px;font-weight:800;color:#f45e6a;margin-top:2px;font-family:Arial">{i["stop"]}</div></div></td>'
                f'</tr></table>'
                f'<div style="font-size:11px;color:#6a7a9a;line-height:1.6;border-top:1px solid #222230;padding-top:8px;font-family:Arial">{i["rationale"]}</div>'
                f'<div style="margin-top:8px"><span style="font-size:10px;font-weight:700;background:#252510;color:#c8b84a;padding:2px 8px;border-radius:3px;font-family:Arial">⚠ {i["risk"]}</span>'
                f'<span style="font-size:10px;color:#333350;margin-left:8px;font-family:Arial">{i["rm"]}</span></div></div>')
    def sector_pill(name, chg):
        try:
            v=float(str(chg).replace('%','').replace('+','').replace('−','-').replace('–','-'))
            bg='#0a1f0a' if v>=0 else '#1f0a0a'; col='#4eca7f' if v>=0 else '#f45e6a'; brd='#1a4a1a' if v>=0 else '#4a1a1a'; sign='+' if v>=0 else ''
            return f'<span style="background:{bg};border:1px solid {brd};border-radius:6px;padding:5px 11px;font-size:11px;font-weight:700;color:{col};font-family:Arial;display:inline-block;margin:2px 3px">{name} {sign}{v:.1f}%</span>'
        except: return ''
    def mover_row(name, ltp, chg, last=False):
        col=chg_color(chg)
        try: sign='+' if float(str(chg).replace('%','').replace('+','').replace('−','-').replace('–','-'))>=0 else ''
        except: sign=''
        b='' if last else 'border-bottom:1px solid #1a1a28;'
        return (f'<tr><td style="padding:6px 0;{b}font-size:12px;font-weight:600;color:#c0c0e0;font-family:Arial">{name}</td>'
                f'<td style="padding:6px 0;{b}font-size:12px;color:#555570;font-family:Arial;text-align:right">{ltp}</td>'
                f'<td style="padding:6px 0 6px 10px;{b}font-size:12px;font-weight:800;color:{col};font-family:Arial;text-align:right">{sign}{chg}</td></tr>')
    def idx_box(name, ltp, chg, hl=''):
        col=chg_color(chg)
        try:
            s=str(chg).replace('%','').replace('+','').replace('−','-').replace('–','-')
            sign='+' if float(s)>=0 else ''; arr=chg_arrow(chg)
        except: sign=''; arr='–'
        pl=f'<div style="font-size:10px;color:#333348;margin-top:2px;font-family:Arial">{hl}</div>' if hl else ''
        return (f'<td width="50%" style="padding:4px"><div style="background:#0a0a12;border:1px solid #1e1e30;border-radius:10px;padding:14px 16px">'
                f'<div style="font-size:9px;color:#555570;font-weight:700;letter-spacing:1px;text-transform:uppercase;font-family:Arial">{name}</div>'
                f'<div style="font-size:26px;font-weight:900;color:#f0f0f8;margin:5px 0 3px;font-variant-numeric:tabular-nums;font-family:Arial">{ltp}</div>'
                f'<div style="font-size:13px;font-weight:700;color:{col};font-family:Arial">{arr} {sign}{chg}</div>{pl}</div></td>')
    d=data
    tm={'RISK-OFF':('#f45e6a','#2a0808','#5a1515'),'RISK-ON':('#4eca7f','#082a12','#155a25'),'MIXED':('#f4d14e','#2a2808','#5a4a15')}
    tc,tbg,tbrd=tm.get(d.get('macro_tone','MIXED'),tm['MIXED'])
    cal_rows=''.join(f'<tr><td style="padding:8px 0;border-bottom:1px solid #1a1a28;vertical-align:top;white-space:nowrap;width:110px"><span style="font-size:10px;color:#4a4a6a;font-weight:700;font-family:Arial">{e["time"]}</span></td><td style="padding:8px 0 8px 12px;border-bottom:1px solid #1a1a28;vertical-align:top"><div style="font-size:13px;font-weight:700;color:#d0d0ee;font-family:Arial">{e["name"]}</div><div style="font-size:11px;color:#555570;margin-top:2px;font-family:Arial">{e["desc"]}</div></td></tr>' for e in d.get('calendar',[]))
    news_rows=''.join(f'<tr><td style="padding:8px 0;border-bottom:1px solid #1a1a28"><div style="font-size:13px;font-weight:600;color:#d0d0ee;line-height:1.4;font-family:Arial">{h["title"]}</div><div style="font-size:11px;color:#4a6a8a;margin-top:3px;font-family:Arial">→ {h.get("impact","")}</div></td></tr>' for h in d.get('news',[]))
    sct=''.join(sector_pill(s['name'],s['chg']) for s in d.get('sectors',[]))
    g_r=''.join(mover_row(m['name'],m['ltp'],m['chg'],i==len(d.get('gainers',[]))-1) for i,m in enumerate(d.get('gainers',[])))
    l_r=''.join(mover_row(m['name'],m['ltp'],m['chg'],i==len(d.get('losers',[]))-1) for i,m in enumerate(d.get('losers',[])))
    sh_r=''.join(f'<tr><td style="padding:6px 0;border-bottom:1px solid #1a1a28;font-size:12px;font-weight:600;color:#c0c0e0;font-family:Arial">{s["name"]}</td><td style="padding:6px 0;border-bottom:1px solid #1a1a28;font-size:12px;color:#4eca7f;font-weight:700;text-align:right;font-family:Arial">{s["ltp"]} &nbsp;+{s["chg"]}</td><td style="padding:6px 0 6px 10px;border-bottom:1px solid #1a1a28;text-align:right"><span style="font-size:10px;font-weight:700;color:#f4954e;background:#221508;padding:2px 6px;border-radius:3px;font-family:Arial">{s["vol"]}× vol</span></td></tr>' for s in d.get('shockers',[]))
    t_c=''.join(idea_card(i) for i in d.get('ideas',[]))
    sh_sec=('<div style="font-size:9px;font-weight:800;color:#f4954e;text-transform:uppercase;letter-spacing:1px;margin:14px 0 8px;font-family:Arial">⚡ VOLUME SHOCKERS</div><table width="100%" cellpadding="0" cellspacing="0">'+sh_r+'</table>') if sh_r else ''
    jarvis_block=''
    if d.get('is_tuesday'):
        jarvis_block=(f'<table width="100%" cellpadding="0" cellspacing="0" style="margin-bottom:12px"><tr><td style="background:#14101e;border:1px solid #2a1a40;border-radius:12px;padding:20px 22px">'
                      f'<div style="font-size:10px;font-weight:800;color:#8844cc;letter-spacing:1.5px;text-transform:uppercase;margin-bottom:14px;font-family:Arial">🔨 JARVIS BUILD FOCUS — TUESDAY</div>'
                      f'<div style="background:#0d0a18;border:1px solid #3a1a5a;border-radius:8px;padding:14px 16px;font-size:12px;color:#b088ee;line-height:1.8;font-family:Arial">{d.get("jarvis_task","")}</div>'
                      f'</td></tr></table>')
    return f"""<!DOCTYPE html><html lang="en"><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1"><meta name="color-scheme" content="dark"><title>🌅 Morning Briefing — {d.get('date','')}</title></head>
<body style="margin:0;padding:0;background:#0a0a10;font-family:Arial,sans-serif">
<table width="100%" cellpadding="0" cellspacing="0" style="background:#0a0a10"><tr><td align="center" style="padding:16px 8px">
<table width="640" cellpadding="0" cellspacing="0" style="max-width:640px;width:100%">
<tr><td style="background:linear-gradient(135deg,#12122a 0%,#0f1c38 50%,#0a2050 100%);border-radius:16px;padding:26px 28px 22px;border:1px solid #1e2850">
  <div style="font-size:11px;color:#4a5a8a;font-weight:700;letter-spacing:2px;text-transform:uppercase;margin-bottom:8px;font-family:Arial">TITAN COMMANDER · MORNING INTELLIGENCE</div>
  <div style="font-size:28px;font-weight:900;color:#ffffff;margin-bottom:4px;font-family:Arial">🌅 Morning Briefing</div>
  <div style="font-size:13px;color:#6a7aaa;font-family:Arial">{d.get('date','')} &nbsp;·&nbsp; Pre-Market &nbsp;·&nbsp; {d.get('time_ist','7:37 AM IST')}</div>
  <div style="margin-top:14px;padding:11px 16px;background:{tbg};border-left:3px solid {tc};border-radius:0 8px 8px 0">
    <span style="font-size:12px;font-weight:800;color:{tc};font-family:Arial">● {d.get('macro_tone','MIXED')}</span>
    <span style="font-size:12px;color:{tc};opacity:0.8;font-family:Arial"> &nbsp;—&nbsp; {d.get('mood_line','')}</span>
  </div>
</td></tr><tr><td style="height:10px"></td></tr>
<tr><td style="background:#13131e;border:1px solid #1e1e30;border-radius:12px;padding:20px 22px">
  <div style="font-size:10px;font-weight:800;color:#5e9ef4;letter-spacing:1.5px;text-transform:uppercase;margin-bottom:14px;font-family:Arial">📅 TODAY'S SCHEDULE</div>
  <table width="100%" cellpadding="0" cellspacing="0">{cal_rows}</table>
</td></tr><tr><td style="height:10px"></td></tr>
<tr><td style="background:#13131e;border:1px solid #1e1e30;border-radius:12px;padding:20px 22px">
  <div style="font-size:10px;font-weight:800;color:#a06af4;letter-spacing:1.5px;text-transform:uppercase;margin-bottom:14px;font-family:Arial">🌍 GLOBAL MACRO</div>
  <table width="100%" cellpadding="0" cellspacing="0">{news_rows}</table>
  <div style="margin-top:12px;display:inline-block;padding:4px 16px;border-radius:20px;background:{tbg};border:1px solid {tbrd}">
    <span style="font-size:11px;font-weight:800;color:{tc};font-family:Arial">MACRO TONE: {d.get('macro_tone','MIXED')}</span>
  </div>
</td></tr><tr><td style="height:10px"></td></tr>
<tr><td style="background:#13131e;border:1px solid #1e1e30;border-radius:12px;padding:20px 22px">
  <div style="font-size:10px;font-weight:800;color:#4eca7f;letter-spacing:1.5px;text-transform:uppercase;margin-bottom:14px;font-family:Arial">🇮🇳 INDIAN MARKETS</div>
  <table width="100%" cellpadding="0" cellspacing="0" style="margin-bottom:14px"><tr>{idx_box('NIFTY 50',d.get('nifty_ltp','–'),d.get('nifty_chg','–'),d.get('nifty_hl',''))}{idx_box('BANK NIFTY',d.get('bn_ltp','–'),d.get('bn_chg','–'),d.get('bn_hl',''))}</tr></table>
  <div style="font-size:9px;color:#444460;font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-bottom:8px;font-family:Arial">SECTORS</div>
  <div style="margin-bottom:16px">{sct}</div>
  <table width="100%" cellpadding="0" cellspacing="0"><tr>
    <td width="50%" style="vertical-align:top;padding-right:10px">
      <div style="font-size:9px;font-weight:800;color:#4eca7f;text-transform:uppercase;letter-spacing:1px;margin-bottom:8px;font-family:Arial">🟢 TOP GAINERS</div>
      <table width="100%" cellpadding="0" cellspacing="0">{g_r}</table>
    </td>
    <td width="50%" style="vertical-align:top;padding-left:10px;border-left:1px solid #1a1a28">
      <div style="font-size:9px;font-weight:800;color:#f45e6a;text-transform:uppercase;letter-spacing:1px;margin-bottom:8px;font-family:Arial">🔴 TOP LOSERS</div>
      <table width="100%" cellpadding="0" cellspacing="0">{l_r}</table>
    </td>
  </tr></table>
  {sh_sec}
</td></tr><tr><td style="height:10px"></td></tr>
<tr><td style="background:#13131e;border:1px solid #1e1e30;border-radius:12px;padding:20px 22px">
  <div style="font-size:10px;font-weight:800;color:#f4d14e;letter-spacing:1.5px;text-transform:uppercase;margin-bottom:14px;font-family:Arial">₿ CRYPTO PULSE</div>
  <table width="100%" cellpadding="0" cellspacing="0"><tr>
    <td width="50%" style="padding-right:6px"><div style="background:#0a0a12;border:1px solid #252515;border-radius:10px;padding:14px 16px"><div style="font-size:9px;color:#8a7a20;font-weight:700;letter-spacing:1px;text-transform:uppercase;font-family:Arial">BITCOIN / BTC</div><div style="font-size:26px;font-weight:900;color:#f0f0f8;margin:5px 0 2px;font-family:Arial">{d.get('btc','–')}</div><div style="font-size:10px;color:#333330;font-family:Arial">Deribit index</div></div></td>
    <td width="50%" style="padding-left:6px"><div style="background:#0a0a12;border:1px solid #252515;border-radius:10px;padding:14px 16px"><div style="font-size:9px;color:#8a7a20;font-weight:700;letter-spacing:1px;text-transform:uppercase;font-family:Arial">ETHEREUM / ETH</div><div style="font-size:26px;font-weight:900;color:#f0f0f8;margin:5px 0 2px;font-family:Arial">{d.get('eth','–')}</div><div style="font-size:10px;color:#333330;font-family:Arial">Deribit index</div></div></td>
  </tr></table>
  <div style="font-size:11px;color:#5a5a30;margin-top:10px;line-height:1.5;font-family:Arial">{d.get('crypto_note','')}</div>
</td></tr><tr><td style="height:10px"></td></tr>
<tr><td style="background:#13131e;border:1px solid #1e1e30;border-radius:12px;padding:20px 22px">
  <div style="font-size:10px;font-weight:800;color:#f45e9a;letter-spacing:1.5px;text-transform:uppercase;margin-bottom:6px;font-family:Arial">💡 TODAY'S TRADE IDEAS — INDIAN MARKETS</div>
  <div style="font-size:10px;color:#333348;margin-bottom:14px;font-family:Arial">Last session close · Confirm live prices before any entry · F&O: no entry before 9:30 AM</div>
  {t_c}
</td></tr><tr><td style="height:10px"></td></tr>
<tr><td style="background:#0d1a20;border:1px solid #1a3a4a;border-radius:12px;padding:16px 22px">
  <div style="font-size:10px;font-weight:800;color:#4ecaf4;letter-spacing:1.5px;text-transform:uppercase;margin-bottom:8px;font-family:Arial">🌐 MT5 TONIGHT</div>
  <div style="font-size:12px;color:#4a8aaa;line-height:1.6;font-family:Arial">NY session opens <strong style="color:#4ecaf4">7:30 PM IST</strong>. Full MT5 session prep — live US500, Gold, GER40, USDCHF levels + edge signal conditions — will be in your <strong style="color:#4ecaf4">Evening Briefing at 7:00 PM IST</strong>.</div>
  <div style="margin-top:10px;font-size:11px;color:#cc6666;font-family:Arial">⚠️ OpusTradingSystem codebase is still running widened parameters. Do not trust live signals until reverted to walk-forward scope.</div>
</td></tr><tr><td style="height:10px"></td></tr>
{jarvis_block}
<tr><td style="text-align:center;padding:16px 0 8px;border-top:1px solid #141420">
  <div style="font-size:10px;color:#28283a;line-height:1.8;font-family:Arial">
    Not financial advice &nbsp;·&nbsp; F&O: no first 15 min · no last 15 min · half-Kelly · max 2 positions · 2 losses = stop<br>
    <span style="color:#1a1a28">Titan Commander · Morning Intelligence · Evening Briefing at 7:00 PM IST</span>
  </div>
</td></tr>
</table></td></tr></table></body></html>"""

# ── FILL IN YOUR DATA BELOW ───────────────────────────────────────────────────
data = {
    "date":       "FILL: e.g. Monday, April 28 2026",
    "time_ist":   "7:37 AM IST",
    "macro_tone": "FILL: RISK-OFF or RISK-ON or MIXED",
    "mood_line":  "FILL: one sentence — dominant market theme today",
    "calendar":   [], # Fill from Google Calendar MCP
    "news":       [], # Fill: [{"title": "...", "impact": "..."}] x5
    "nifty_ltp": "FILL", "nifty_chg": "FILL", "nifty_hl": "FILL",
    "bn_ltp":    "FILL", "bn_chg":    "FILL", "bn_hl":    "FILL",
    "sectors":   [], # [{"name": "Pharma", "chg": "+2.36"}, ...] all 5 sectors
    "gainers":   [], # [{"name": "...", "ltp": "₹...", "chg": "+...%"}] x3
    "losers":    [], # x3
    "shockers":  [], # [{"name": "...", "ltp": "₹...", "chg": "...%", "vol": "137"}] only if >5x
    "btc":       "FILL", "eth": "FILL",
    "crypto_note": "FILL: one sentence on BTC/ETH structure",
    "ideas":     [], # 4-5 Indian F&O + equity ideas
    "is_tuesday": is_tuesday,
    "jarvis_task": "" # Fill on Tuesdays
}

html    = render(data)
subject = f"🌅 Morning Briefing – {data['date']}"

ej = subprocess.run(["curl","-s","-X","POST","https://api.emailjs.com/api/v1.0/email/send",
    "-H","Content-Type: application/json",
    "-d", json.dumps({"service_id":"SERVICE_ID","template_id":"TEMPLATE_ID","user_id":"EMAILJS_PUBLIC_KEY","accessToken":"EMAILJS_PRIVATE_KEY","template_params":{"to_email":"manankharbanda99@gmail.com","subject":subject,"message":html}})],
    capture_output=True, text=True)
print(f"EmailJS: {ej.stdout.strip()}")

ideas_str='\n'.join(f"{n+1}. **{i['symbol']}** — {i['direction']} | Entry: {i['entry']} | T: {i['target']} | SL: {i['stop']}" for n,i in enumerate(data.get('ideas',[])[:3]))
te='🔴' if 'OFF' in data.get('macro_tone','') else '🟢' if 'ON' in data.get('macro_tone','') else '🟡'
top_news=data['news'][0]['title'] if data.get('news') else 'No news'
dc_msg=f"""🌅 **Morning Briefing — {data['date']}**

📊 Nifty: **{data.get('nifty_ltp','–')} ({data.get('nifty_chg','–')})** | BankNifty: **{data.get('bn_ltp','–')} ({data.get('bn_chg','–')})**
₿ BTC: **{data.get('btc','–')}** | ETH: **{data.get('eth','–')}**

🌍 **{te} {data.get('macro_tone','–')}** — {top_news}

💡 **Indian Setups**
{ideas_str}

🌐 MT5 full session prep → **Evening Briefing at 7:00 PM IST**
_Not financial advice_"""
subprocess.run(["curl","-s","-X","POST","https://discord.com/api/v10/channels/DISCORD_CHANNEL_ID/messages",
    "-H","Authorization: Bot DISCORD_BOT_TOKEN",
    "-H","Content-Type: application/json","-d",json.dumps({"content":dc_msg})], capture_output=True)
print("Discord: sent")
print(f"Done. {len(html):,} chars")
```

---
## RULES
1. Never write your own HTML. Always use render() above. Never modify it.
2. Fill every field. No placeholders in final output.
3. Indian F&O + equity ideas only in morning. MT5 is the evening briefing's job.
4. Always send both channels. Partial briefing beats no briefing.
5. OpusTradingSystem codebase warning must appear every day without exception.