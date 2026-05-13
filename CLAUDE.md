# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A static, single-file Korean interactive e-book titled **"우연이 만드는 진로"** (Career-making Serendipity), based on Krumboltz's Planned Happenstance Learning Theory (HLT). Written by Professor Lee Seung-won at Hansung University.

There is no build system, no package manager, and no test suite. The entire application is self-contained in one HTML file.

## File Structure

- `HLT_전자책_통합본-2.html` — The entire application: CSS, HTML, and JavaScript all in one file (2185 lines)
- `index.html` — Redirect shim that forwards to the main file
- `README.md` — Minimal placeholder

## Development

Open directly in a browser — no server required for most features. To serve locally:

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

The only external dependency is Chart.js loaded from CDN:
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
```

## Architecture

### Page System
All 17 pages live as `<div id="page-{id}" class="page">` siblings inside `<main id="content">`. Exactly one has `.active` at a time. Navigation is driven by `showPage(id)` which toggles `.active` on pages and `.active` on sidebar `.nav-item` links.

The linear reading order is declared explicitly:
```js
const pageOrder = ['cover','prologue','ch1','ch2','ch3','ch4','ch5','ch6','ch7','ch8','ch9','ch10','ch11','epilogue','appendix-a','appendix-b','appendix-c'];
```

### Layout
Fixed left sidebar (260px, `--sidebar-w`) + `margin-left`-offset main content. On mobile (`@media` breakpoint), the sidebar slides off-screen and a hamburger button (`#menu-btn`) toggles it via `toggleSidebar()` / `closeSidebar()`.

### Theming
CSS custom properties (`--green`, `--indigo`, `--amber`, etc.) are defined in `:root` with a full dark-mode override via `@media (prefers-color-scheme: dark)`. All colors in the file reference these tokens — never use raw hex values directly.

### Interactive Features

**PHCI Self-Assessment (Appendix A)**
- Data: `phciSkills` array — 5 skill categories × 5 Likert-scale questions each
- Questions are rendered dynamically into `#questions-{si}` containers on page load
- Scoring: `updatePhciScore(si)` recalculates per-skill totals live; `showPhciResult()` renders a Chart.js radar chart and summary into `#phci-result-section`
- Key DOM ids: `score-badge-{si}`, `bar-{si}`, `phci-radar-chart`, `phci-result-grid`, `phci-action-message`

**Reflection Worksheet (Appendix B)**
- Tab switching: `wsTab(idx)` toggles `.active` on `.ws-tab-btn` and `.ws-tab-content` elements
- History cards and plan rows are built dynamically via `makePlanRows(containerId, count, placeholder)`
- Save feedback: `wsSave(msgId)` flashes a confirmation message (no persistence — worksheet data is in-memory only)

**Reading Progress Bar**
- `#progress-bar-top` is a fixed bar scaled via `transform: scaleX()` on the scroll event

## Conventions

- All CSS is in the `<style>` block at the top of the HTML file, organized with comment banners like `/* ─── Section Name ─── */`
- All JavaScript is in the `<script>` block at the bottom, organized with `/* ══ Section ══ */` banners
- Korean content is the default language (`lang="ko"`); fonts: `Nanum Myeongjo` (serif headings) and `Noto Sans KR` (body)
- Chapter content pages follow a consistent structure: eyebrow label → chapter title → body content → `.reflection-q-box` → `.chapter-nav` prev/next buttons
