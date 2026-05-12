# Claude Scheduled Tasks

Automated newsletter and analysis pipelines running daily via Claude Cowork.

## Tasks

### 📰 daily-ai-newsletter
**Schedule:** 9:00 AM IST daily  
**Publishes to:** Substack (primary) → Medium (secondary) + 2 Substack Notes  
**Pipeline:** Deduplicate → Research → Write HTML email → AI self-review → Send email → Canva infographics → Substack → Medium → Notes → Drive log → GitHub sync

### 🌍 daily-world-news
**Schedule:** 8:00 AM IST daily  
**Publishes to:** Substack (primary) → Medium (secondary) + 2 Substack Notes  
**Pipeline:** Research world + India stories → Write email → Substack → Medium → Notes → Drive log → GitHub sync

### 📈 daily-morning-briefing
**Schedule:** 7:30 AM IST daily  
**Delivers:** Personal trading + news briefing via email + Discord

### 📊 weekly-trading-prep
**Schedule:** Sundays 8:00 AM IST  
**Delivers:** Week-ahead trading setup — key levels, events, strategy

### 🔍 monday-premarket-scan
**Schedule:** Mondays 7:00 AM IST  
**Delivers:** Pre-market scan with momentum picks and gaps to watch

### 🔔 substack-notes-drip
**Schedule:** 12:30 PM IST daily  
**Posts:** 3 standalone Substack Notes from today's newsletters + Canva visual  
**Purpose:** Subscriber growth engine — 70-90% of new Substack subs come from Notes

## Architecture
- All tasks run as Claude Cowork scheduled tasks
- Infographics: Canva MCP (with matplotlib fallback)  
- Hyperlink preservation: HTML ClipboardItem API on paste
- Deduplication: Session transcript reading across last 4 runs
- Version control: Each run auto-pushes updated SKILL.md here
