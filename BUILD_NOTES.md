# Purelane Shopify Theme — Build Notes

**Store:** [purelane-assignment-1gstkou9.myshopify.com](https://purelane-assignment-1gstkou9.myshopify.com)  
**Repo:** [github.com/Ranit2004a/purelane-assignment](https://github.com/Ranit2004a/purelane-assignment.git)  
**Base Theme:** Shopify Dawn v15+ (stock, unmodified core)  
**Date:** August 2026

---

## 1. What Was Built

A fully custom Shopify theme homepage built on top of Dawn, converting a static HTML prototype (`purelane-homepage.html`) into a production-ready, merchant-editable Shopify storefront.

### Sections Created

| File | Section | Key Features |
|---|---|---|
| `purelane-hero.liquid` | Hero Slider | 3-slide product stage, animated price pills, CTA buttons, JSON schema |
| `purelane-shop.liquid` | Product Grid | Shopify collection binding, 4-col grid, sold-out badges, image fallback |
| `purelane-combos.liquid` | Best-Selling Combos | Block-based combo cards, savings pills, product stack visuals |
| `purelane-bundles.liquid` | Bundle Picker | Tier-based bundle builder with discount badges and perks list |
| `purelane-reviews.liquid` | Reviews Rail | Star-rating cards, verified badges, location tags |
| `purelane-ingredients.liquid` | Ingredients | Nature-sourced claim blocks with icons |
| `purelane-before-after.liquid` | Before / After | Drag-slider reveal of cleaning results |
| `purelane-social-proof.liquid` | Social Proof | Sticky mobile ATC bar + trust icons |

### Snippets Created

| File | Purpose |
|---|---|
| `snippets/purelane-product-card.liquid` | Reusable product card with sold-out, no-image, long-title handling |
| `snippets/header-search.liquid` | Inline search pill (no popup, no full-screen overlay) |

### Core Asset

`assets/purelane-theme.css` — 3,400+ line design system including:
- Design tokens (`--brand`, `--paper`, `--accent`, `--surface`, `--ease`)
- Typography scale (`Outfit` display, `Inter` body — both from Google Fonts)
- Glass utilities (`.glass`, `.glass-2`, glassmorphism backdrop-filter)
- All section-specific styles, animations, and responsive breakpoints
- Navbar liquid glass effect (always-on blur/saturate, specular ::before overlay)
- Inline search pill (positioned between nav links and search icon)

---

## 2. Key Technical Decisions

### Why Dawn (not a blank theme)?
Dawn handles Shopify's cart AJAX, section rendering API, sticky header JS, and search modal JS natively. Building on it saves ~40 hours of boilerplate while keeping the core lean.

### Why not Tailwind / a framework?
The prototype design is highly bespoke. Raw CSS gives full control over glassmorphism, keyframe animations, clamp() fluid typography, and custom CSS variables without class-name bloat.

### Dual-mode rendering
Every section renders **live Shopify products** when a collection/product is assigned in the Customizer, but **gracefully falls back** to hardcoded prototype data if nothing is connected yet. This means the store looks great even before any real product data is added.

### SVG product tokens
Product bottle illustrations are Base64-encoded SVG strings stored in CSS custom properties (`--p-kitchen`, `--p-dish`, `--p-tap`, etc.). This lets the CSS reference them as `background-image` without extra HTTP requests, keeping the product stage performant.

---

## 3. Problems Encountered & How They Were Solved

| Problem | Solution |
|---|---|
| Dawn's white bar behind navbar | Targeted `header-wrapper`, `sticky-header` element selectors; stripped Dawn color-scheme background via `!important` overrides |
| Search modal opening full-screen | Overrode `.search-modal` to `position:absolute; inset:0` (transparent), repositioned `.search-modal__content` as an inline floating pill using `right: 170px; top:50%` |
| Nav links overlapping search pill | Used `translateX` shift on `details[open]` siblings via CSS sibling selector to slide links left |
| Schema name > 25 chars (Shopify limit) | Shortened all schema `name` fields to ≤25 characters |
| `index.json` BOM corruption (PowerShell) | Rewrote via `node fs.writeFileSync()` with explicit `{encoding:'utf8'}` to avoid UTF-8 BOM |
| Shallow Git clone push failure | Re-initialized a clean non-shallow repo; force-pushed to `main` |

---

## 4. Navbar — Liquid Glass Implementation

```css
header.header {
  background: linear-gradient(135deg,
    rgba(255,255,255,0.30),
    rgba(220,245,235,0.18),
    rgba(200,235,220,0.12)
  );
  backdrop-filter: blur(40px) saturate(260%) brightness(1.12) contrast(1.04);
  outline: 1px solid rgba(255,255,255,0.55);
  box-shadow:
    0 12px 28px rgba(0,40,30,0.12),
    inset 0 1.5px 1px rgba(255,255,255,0.85);
}

/* Specular top shine */
header.header::before {
  background: linear-gradient(180deg, rgba(255,255,255,0.42) 0%, transparent 40%);
}
```

- `blur(40px)` — real frosted glass look
- `saturate(260%)` — punches through colour from the hero below
- `::before` specular — mimics how glass catches light at the top edge

---

## 5. What I Would Do With More Time

- **AJAX Bundle Picker:** Let users pick 3 products from a modal, auto-apply discount codes via Shopify AJAX Cart API
- **Shopify Metaobjects for Reviews:** Move review cards to native Metaobject definitions for merchant-manageable content
- **WebGL Water Shader:** Upgrade the CSS SVG water drift background to a lightweight WebGL canvas for 60fps fluid motion
- **A/B Test Variant Sections:** Create alternate hero/combos layouts as section presets for split-testing
- **Metafield-driven pricing:** Bind combo prices to Shopify selling plans rather than hardcoded schema settings
