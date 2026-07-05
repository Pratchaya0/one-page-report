---
name: onepage-html
description: Generate concise one-page HTML reports with consistent dark/light theming. Every report uses at least one Chart.js chart or diagram — never tables alone. Caveman-style brevity: data-dense, zero filler. Use when user asks for a report, dashboard, summary, visualization, or one-page overview of any data.
---

# Onepage HTML Report

Generate self-contained, single-page HTML reports. Concise. Chart-first. One file, no dependencies beyond CDN Chart.js.

## Operating Rules

### Brevity (Caveman-style)
- **No filler.** Drop pleasantries, hedging, "here is your report", long intros. Report speaks for itself.
- **Data-dense.** Every pixel earns its place. If a number can be a metric card, don't bury it in prose.
- **Labels short.** "Revenue" not "Total Revenue for Q4 2026". "Δ 7d" not "Change over the last 7 days".
- **No decorative emoji in body.** Use only for status dots or data labels where semantic.
- **Annotations on charts.** Use Chart.js tooltips and labels instead of long captions below.

### Charts Mandatory
- **Every report includes ≥1 chart.** Tables alone are not a report. Choose the right chart type for the data:
  - **Trend over time:** line chart
  - **Composition / parts of whole:** doughnut or stacked bar
  - **Comparison across categories:** horizontal bar (easier to read labels)
  - **Distribution / range:** box-like bar with min/max whiskers
  - **Correlation / relationship:** scatter with trend line
  - **Progress toward goal:** gauge-like doughnut or progress bar
  - **Hierarchy / flow:** sankey or tree diagram (render with inline SVG if simple)
  - **Before/after comparison:** side-by-side bars
- **Chart sits in `.panel > .panel-body > .chart-wrap > canvas`** structure.
- **Every `<canvas>` needs a unique `id`.** Chart.js destroys on re-init if id clashes.

### Theming
- **Template:** Read `template.html` from this skill directory first.
- **Replace `{{TITLE}}`** with report title (browser tab).
- **Replace `{{BODY}}`** with full body HTML including `<script>` blocks for charts.
- **Dark/light auto.** System preference detected via `prefers-color-scheme`. Manual toggle button included.
- **HTML/CSS colors from variables.** Use class utilities (`.text-positive`, `.text-negative`, `.text-warning`). Chart.js dataset colors are the exception — hardcode dark-mode hex (see Color Palette).

### Output
- **Single file.** Write to `reports/<name>-<date>.html`. Create `reports/` dir if needed.
- **No external files.** All CSS and JS inline or via CDN. Fonts from Google Fonts, Chart.js from jsDelivr CDN.
- **Print-friendly.** `@media print` styles included in template. Report prints clean.

## Workflow

1. **Collect data.** Understand what numbers need to be shown.
2. **Choose charts.** Pick ≥1 chart type that best reveals the data's story.
3. **Read template.** `cat` the template file from this skill directory.
4. **Build body.** Assemble: header → metrics → charts-in-panels → optional table → footer.
5. **Write `<script>` blocks.** One per chart, using Chart.js. Place at end of `{{BODY}}` content, after footer panels, still inside `.container`.
6. **Replace placeholders.** `{{TITLE}}` and `{{BODY}}` in template.
7. **Write file.** Output to `reports/<name>-<date>.html`.

## Body Structure

```html
<!-- HEADER (required) -->
<header class="header">
  <div>
    <h1 class="title">REPORT TITLE</h1>
    <p class="subtitle">One-line context</p>
  </div>
  <div class="header-actions">
    <span class="status">
      <span class="status-dot positive"></span>
      STATUS TEXT
    </span>
    <span class="timestamp">Generated: YYYY-MM-DD HH:MM TZ</span>
    <button class="theme-toggle" onclick="toggleTheme()">◐ THEME</button>
  </div>
</header>

<!-- METRICS (recommended: 4-6 cards) -->
<div class="metrics">
  <div class="metric">
    <div class="metric-label">Label</div>
    <div class="metric-value text-mono">123.4</div>
    <div class="metric-change">+5.2% vs prior</div>
  </div>
  <!-- repeat as needed -->
</div>

<!-- CHARTS in panels (≥1 required) -->
<div class="panel">
  <div class="panel-header">Chart Title</div>
  <div class="panel-body">
    <div class="chart-wrap">
      <canvas id="chart-unique-id"></canvas>
    </div>
  </div>
</div>

<!-- Optional table -->
<div class="panel">
  <div class="panel-header">Data Table</div>
  <table>
    <thead><tr><th>Col</th><th class="text-right">Val</th></tr></thead>
    <tbody>
      <tr><td>Item</td><td class="text-right text-mono">123</td></tr>
    </tbody>
  </table>
</div>

<!-- FOOTER -->
<div class="footer"><p>Report · YYYY-MM-DD · Data Source</p></div>
```

## Chart.js Patterns

### Line Chart (trends over time)
```js
new Chart(document.getElementById('trend'), {
  type: 'line',
  data: {
    labels: ['Jan','Feb','Mar','Apr','May','Jun'],
    datasets: [{
      label: 'Series A',
      data: [12, 19, 15, 25, 22, 30],
      borderColor: '#05AD98',
      backgroundColor: 'rgba(5, 173, 152, 0.1)',
      fill: true,
      tension: 0.3,
      pointRadius: 3,
      pointHoverRadius: 5,
    }]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    plugins: { legend: { labels: { usePointStyle: true, boxWidth: 8 } } },
    scales: {
      x: { grid: { color: 'var(--chart-grid)' }, ticks: { color: 'var(--text-muted)' } },
      y: { grid: { color: 'var(--chart-grid)' }, ticks: { color: 'var(--text-muted)' }, beginAtZero: false }
    }
  }
});
```

### Bar Chart (comparisons, horizontal preferred)
```js
new Chart(document.getElementById('bars'), {
  type: 'bar',
  data: {
    labels: ['Alpha','Beta','Gamma','Delta'],
    datasets: [{
      label: 'Value',
      data: [45, 72, 33, 58],
      backgroundColor: ['#05AD98','#3B82F6','#F5A623','#E85D6C'],
      borderRadius: 4,
      borderSkipped: false,
    }]
  },
  options: {
    indexAxis: 'y', // horizontal bars — labels readable
    responsive: true,
    maintainAspectRatio: false,
    plugins: { legend: { display: false } },
    scales: {
      x: { grid: { color: 'var(--chart-grid)' }, ticks: { color: 'var(--text-muted)' } },
      y: { grid: { display: false }, ticks: { color: 'var(--text-primary)', font: { size: 11 } } }
    }
  }
});
```

### Doughnut Chart (composition, progress)
```js
new Chart(document.getElementById('donut'), {
  type: 'doughnut',
  data: {
    labels: ['Completed','In Progress','Pending'],
    datasets: [{
      data: [65, 25, 10],
      backgroundColor: ['#05AD98','#3B82F6','#475569'],
      borderColor: 'var(--bg-panel)',
      borderWidth: 2,
    }]
  },
  options: {
    responsive: true,
    maintainAspectRatio: true,
    cutout: '70%',
    plugins: {
      legend: { position: 'bottom', labels: { usePointStyle: true, boxWidth: 8, padding: 16, color: 'var(--text-primary)' } }
    }
  }
});
```

### Multi-Dataset Line (comparison)
```js
new Chart(document.getElementById('multi'), {
  type: 'line',
  data: {
    labels: ['W1','W2','W3','W4','W5','W6','W7','W8'],
    datasets: [
      { label: 'This Year', data: [10,15,12,20,18,25,22,30], borderColor: '#05AD98', backgroundColor: 'transparent', tension: 0.3 },
      { label: 'Last Year', data: [8,12,10,14,15,18,20,22], borderColor: '#475569', backgroundColor: 'transparent', tension: 0.3, borderDash: [4,4] }
    ]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    plugins: { legend: { labels: { usePointStyle: true, boxWidth: 8, color: 'var(--text-primary)' } } },
    scales: {
      x: { grid: { color: 'var(--chart-grid)' }, ticks: { color: 'var(--text-muted)' } },
      y: { grid: { color: 'var(--chart-grid)' }, ticks: { color: 'var(--text-muted)' } }
    }
  }
});
```

### Scatter Chart (correlation)
```js
new Chart(document.getElementById('scatter'), {
  type: 'scatter',
  data: {
    datasets: [{
      label: 'Risk vs Return',
      data: [{x:5,y:8},{x:12,y:15},{x:8,y:10},{x:20,y:18},{x:15,y:12},{x:25,y:22}],
      backgroundColor: '#05AD98',
      pointRadius: 5,
      pointHoverRadius: 7,
    }]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    scales: {
      x: { title: { display: true, text: 'Risk (%)', color: 'var(--text-muted)' }, grid: { color: 'var(--chart-grid)' }, ticks: { color: 'var(--text-muted)' } },
      y: { title: { display: true, text: 'Return (%)', color: 'var(--text-muted)' }, grid: { color: 'var(--chart-grid)' }, ticks: { color: 'var(--text-muted)' } }
    },
    plugins: { legend: { labels: { color: 'var(--text-primary)' } } }
  }
});
```

### Sparkline (inline mini chart, no axes)
```js
new Chart(document.getElementById('spark1'), {
  type: 'line',
  data: {
    labels: ['','','','','','','','','',''],
    datasets: [{
      data: [3,5,4,7,6,8,9,7,10,12],
      borderColor: '#05AD98',
      borderWidth: 1.5,
      pointRadius: 0,
      tension: 0.4,
    }]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    plugins: { legend: { display: false }, tooltip: { enabled: false } },
    scales: { x: { display: false }, y: { display: false } },
    elements: { point: { radius: 0 } }
  }
});
```
> For sparklines, set canvas container height to ~40px with `style="height:40px"`.

## CSS Utilities (quick reference)

### Layout
`.grid-2`, `.grid-3`, `.grid-4`, `.grid-2-1` — CSS grid layouts.

### Text
`.text-right`, `.text-center`, `.text-muted`, `.text-positive`, `.text-negative`, `.text-warning`, `.text-info`, `.text-mono`, `.text-small`, `.text-tiny`

### Pills
`.pill`, `.pill-positive`, `.pill-negative`, `.pill-warning`, `.pill-info`, `.pill-accent`

### Callouts
`.callout` + `.callout.positive`, `.callout.negative`, `.callout.warning`

### Progress Bars
```html
<div class="bar-container"><div class="bar-fill positive" style="width:75%"></div></div>
```

### Sparkline Bars (CSS-only, no JS needed)
```html
<div class="spark">
  <div class="spark-bar up" style="height:40%"></div>
  <div class="spark-bar down" style="height:30%"></div>
  <div class="spark-bar up" style="height:60%"></div>
  <div class="spark-bar flat" style="height:20%"></div>
  <div class="spark-bar up now" style="height:80%"></div>
</div>
```

## Color Palette

Use Chart.js colors that match the theme. These are the CSS variable equivalents:
| Role | Hex (dark) | Hex (light) | CSS Class |
|------|-----------|-------------|-----------|
| Accent/positive | `#05AD98` | `#048A7A` | `var(--positive)` |
| Negative | `#E85D6C` | `#DC2626` | `var(--negative)` |
| Warning | `#F5A623` | `#D97706` | `var(--warning)` |
| Info/blue | `#3B82F6` | `#2563EB` | `var(--info)` |
| Muted/grey | `#475569` | `#94A3B8` | `var(--text-muted)` |

When defining Chart.js dataset colors, hardcode dark-mode hex values. They work fine in light mode too since Chart.js does not re-render on theme switch by default. If theme-adaptive charts are needed, destroy and re-create all charts on `toggleTheme()` — but this is usually overkill.

## File Naming

```
reports/
├── weekly-metrics-2026-07-05.html
├── q2-review-2026.html
├── system-health-2026-07-05.html
└── ab-test-results-2026-07.html
```

## Generation Checklist

1. Understand the data and choose ≥1 chart type
2. Read template from this skill directory
3. Build body: header → metrics → chart panels → optional table → footer
4. Write Chart.js `<script>` blocks for each canvas
5. Replace `{{TITLE}}` and `{{BODY}}` in template
6. Ensure `reports/` directory exists
7. Write HTML file
8. Confirm all canvas `id` values are unique
9. Confirm no unresolved `{{PLACEHOLDER}}` strings remain
