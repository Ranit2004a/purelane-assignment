# Purelane Shopify Theme — AI Workflow Notes

**Project:** Purelane Clean Homecare — Shopify Theme Build  
**AI Tool:** Antigravity (Google DeepMind)  
**Date:** August 2026

---

## 1. How AI Was Used in This Project

### Prompt → Code (Primary Use)
The majority of the theme was built through natural-language instruction. The AI:
- Read the static prototype (`purelane-homepage.html`) and extracted design tokens, colour palettes, and layout patterns
- Converted sections into Shopify Liquid templates with full `{% schema %}` blocks, block types, presets, and setting defaults
- Generated all CSS from scratch — design system, animations, glassmorphism effects, responsive breakpoints

### Iterative Refinement Loop
Every UI decision went through a rapid iteration cycle:

```
User describes change (plain English)
    ↓
AI reads current file → identifies exact lines → makes surgical edit
    ↓
shopify theme dev auto-syncs → user previews in browser
    ↓
User gives feedback → next iteration
```

Typical iteration time: **under 30 seconds** from instruction to synced change.

### What AI Handled End-to-End
- Full section architecture (8 Liquid sections, 2 snippets)
- 3,400+ line CSS design system written from a single prompt
- All Shopify schema JSON (blocks, settings, presets, types)
- Edge-case handling (sold-out, missing image, long titles, fallback data)
- Navbar liquid glass effect (specular highlight, backdrop-filter, prismatic outline)
- Inline search pill (no popup, repositioned between nav links and search icon)
- Animated trust marquee strip (CSS keyframe marquee with hover-pause, icon bounce)
- `index.json` template order management (section insertion and restoration)
- Git setup and Shopify CLI push troubleshooting

---

## 2. Prompting Strategy That Worked

### Be Visual, Not Technical
Instead of:
> "Set `right` to `170px` and `width` to `clamp(120px, 22vw, 240px)`"

Use:
> "shift the search bar a little to the left so the profile and cart icon are still visible"

The AI translates spatial intent into exact CSS values automatically.

### Undo by Description
> "remove all the things you have done — go back to the stage where the navbar was fixed"

The AI read the change history, identified the exact files and line ranges changed, and restored them in the correct order without a git reset.

### Reference the Visual Gap
Attaching a screenshot with the message was far more effective than describing it in words. The AI could identify which section boundary needed a strip, what the background colour was, and what style of content would fit.

---

## 3. Where AI Required Human Correction

| Situation | What Happened | Fix |
|---|---|---|
| Schema name > 25 chars | AI wrote `"Purelane – Section Divider"` (26 chars) — Shopify rejected it | User spotted the error; AI shortened to `"Purelane Divider Strip"` (22 chars) |
| PowerShell BOM in JSON | `Set-Content` wrote a UTF-8 BOM byte, making `index.json` invalid | Switched to Node.js `fs.writeFileSync` with explicit UTF-8 encoding |
| Over-eager section insertion | Replace tool duplicated JSON content when the target string matched unexpectedly | Rewrote the whole file via a Node.js script to guarantee clean output |
| Search pill overlapping icons | `right: calc(100% + 12px)` was relative to the wrong parent | AI inspected the DOM structure (`header-search.liquid`), recalculated offset based on icon widths (~44px each) |
| Navbar glass invisible on scroll | First pass only applied glass on `.scrolled-past-header` — not the default state | Added always-on `header.header` rule separate from scroll-state rules |

---

## 4. AI Limitations Observed

- **No live browser access** — the AI cannot see the rendered result; it relies entirely on user screenshots and descriptions
- **CSS specificity guessing** — Dawn's generated inline styles sometimes overrode the custom CSS; required `!important` escalation after inspecting the cascade
- **File length awareness** — For files >3,000 lines, the AI uses grep + targeted line reads rather than reading the whole file, which means it occasionally missed context from distant lines
- **Shopify schema validation** — The AI doesn't know Shopify's current schema constraints (e.g. name length, valid `type` values) by memory alone; it learns them when the CLI reports an error

---

## 5. Workflow Summary

```
1. Static prototype reviewed → sections identified → build plan created
2. Dawn theme initialized via Shopify CLI
3. AI builds section-by-section in order (hero → shop → combos → bundles → reviews)
4. CSS design system written once, referenced everywhere
5. Live preview via `shopify theme dev` throughout
6. UI polish loop: user screenshots → AI fixes → re-preview
7. Files committed and pushed to GitHub
8. Final push via `shopify theme push`
```

### Tools Used
| Tool | Role |
|---|---|
| Antigravity AI | Code generation, file editing, debugging, CSS authoring |
| Shopify CLI (`shopify theme dev`) | Live preview with hot-reload on every file save |
| Shopify CLI (`shopify theme push`) | Deploy to store |
| Git + GitHub | Version control |
| Node.js | JSON file rewriting (avoiding PowerShell BOM issues) |

---

## 6. What I Would Systematize for Scale

- **Section template generator:** A Node.js CLI that accepts a component name + field list and outputs a boilerplate `.liquid` + schema JSON in one command
- **CSS token extractor:** A script that reads a static HTML file and auto-generates CSS custom properties from all inline styles
- **Shopify seeder script:** GraphQL Admin API script to auto-create 8+ test products (sold-out, no-image, long-title variants) on any fresh dev store
- **Prompt library:** A saved set of proven prompts for common Shopify patterns (mega menu, sticky ATC, product variant picker, review metaobjects)
