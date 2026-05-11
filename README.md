# Claude Scheduled Tasks

Automated daily pipelines running via Claude's Cowork mode.

## Newsletters

| Task | Schedule | Description |
|------|----------|-------------|
| `daily-ai-newsletter` | 9:09 AM IST daily | AI Intel Drop — research, write, email + Substack + Medium |
| `daily-world-news` | 8:09 AM IST daily | The World Briefing — geopolitics, markets, India lens, email + Substack + Medium |

## Trading

| Task | Schedule | Description |
|------|----------|-------------|
| `daily-morning-briefing` | 7:37 AM IST daily | Indian markets morning scan, day trade ideas |
| `weekly-trading-prep` | 7:03 PM IST Sunday | Deep market scan, full trading plan for the week |
| `monday-premarket-scan` | 8:53 AM IST Monday | Pre-market entry levels and setups |

## Structure

Each folder contains a `SKILL.md` — the exact instructions Claude follows on every run. These are living documents; they get updated when bugs are fixed or new steps are added.

## Publishing

- **Newsletters** publish to email (EmailJS), Substack (`bytesizedbymanan.substack.com`), and Medium as a secondary channel
- **Medium** has a 2-story/24h limit — Substack is always the guaranteed publish destination
- **Google Drive** content system logs each run: Publishing Log + Topic Archive sheets

## Content System (Google Drive)

- Publishing Log: tracks every issue — headline, URLs, email status, review score
- Topic Archive: deduplication history across all newsletter runs
- Assets folder: infographic PNGs uploaded after each AI newsletter run
