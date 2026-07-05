# Onepage HTML Report Skill

> **Chart-first. Dark/Light mode. Zero filler.**
> Generate concise one-page HTML reports — for AI coding agents.

[![Skill](https://img.shields.io/badge/skill-onepage--html-%2305AD98)](./skills/onepage-html/SKILL.md)
[![License](https://img.shields.io/badge/license-MIT-blue)](./LICENSE)

## 🎯 What It Does

Turns your AI agent into a report generator. Ask for a dashboard, get a self-contained HTML file with:

- **📊 Chart.js charts** (line, bar, doughnut, scatter, sparkline)
- **🌓 Dark/Light theme** (auto-detects system preference)
- **📄 Single file output** — zero dependencies beyond CDN
- **🖨️ Print-friendly** — looks good on paper
- **✂️ Caveman-style brevity** — data-dense, no fluff

## 📸 Example Output

Prompt:
> "Generate a Q2 2026 performance review with revenue chart and cost breakdown"

Agent generates → `reports/q2-2026-review.html`:

| Metrics | Charts | Extras |
|---------|--------|--------|
| Revenue $1.2M (+12%) | 📈 Monthly revenue line chart | 🌓 Theme toggle |
| Costs $820K (+5%) | 🍩 Cost breakdown doughnut | 🖨️ Print styles |
| Profit $380K (+28%) | — | ⚡ Zero server cost |
| Customers 3,420 (+8%) | — | 📦 Single file |

## 🚀 Install

```bash
npx skills add Pratchaya0/one-page-report
```

Or specify your agent:

```bash
npx skills add Pratchaya0/one-page-report -a claude   # Claude Code
npx skills add Pratchaya0/one-page-report -a cursor   # Cursor
npx skills add Pratchaya0/one-page-report -a zed      # Zed
npx skills add Pratchaya0/one-page-report -a codex    # OpenAI Codex
```

## 📂 Skills

| Skill | Description | Triggers |
|-------|-------------|----------|
| `onepage-html` | Chart-first HTML reports with dark/light theme | "generate report", "create dashboard", "visualize data", "one-page overview" |

## 🔧 Usage

After install, just ask your agent naturally:

```
"Make a weekly metrics dashboard with trend chart"
"Generate a system health report for last 30 days"
"Create a one-page Q2 review with revenue vs cost chart"
```

The skill auto-activates and produces `reports/<name>-<date>.html`.

## 🏗️ Architecture

```
skill loads → agent reads template.html
           → agent builds body: header → metrics → charts → table → footer
           → injects Chart.js scripts
           → writes single-file HTML
```

**Zero server cost.** All rendering happens in the browser via Chart.js CDN.

## 📄 License

MIT — free to use, modify, and share.
