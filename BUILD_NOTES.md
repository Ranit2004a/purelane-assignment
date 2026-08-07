# Purelane Shopify Assignment — Build & Technical Notes

**Store URL:** [https://purelane-assignment-1gstkou9.myshopify.com](https://purelane-assignment-1gstkou9.myshopify.com)  
**GitHub Repository:** [https://github.com/Ranit2004a/purelane-assignment.git](https://github.com/Ranit2004a/purelane-assignment.git)

---

## 1. Flags & Analysis of Original Prototype (`purelane-homepage.html`)

While visually compelling, the prototype had several production hurdles that required refactoring for Shopify:
- **Monolithic & Non-Modular:** The prototype was a 150KB single static HTML file with all sections, styles, and inline scripts bundled together without reusable components.
- **Hardcoded Product & Price Data:** All product titles, prices (`₹200`, `₹349`, `₹499`), compare prices, and badges were hardcoded into HTML strings rather than leveraging Shopify platform data.
- **Inline Data-URI Image Sprites:** Product bottles relied on hardcoded Base64 SVG strings in CSS variables (`--p-kbtl`, `--p-kitchen`, etc.), preventing merchants from uploading real product photography via Shopify Admin.
- **Accessibility & Touch Targets:** Missing ARIA tags, missing form controls for cart actions, non-semantic headings in places, and raw div buttons without keyboard focus states.

---

## 2. Code Architecture & What Was Changed

### Modular Section Architecture
Built on **stock Dawn theme** (v15+) to guarantee compatibility and zero third-party framework clutter. Converted the top 5 requested sections into independent Liquid sections:
1. **`sections/purelane-hero.liquid` (Section 01 - Hero):** Features interactive product stage slides (1-bottle, 2-bottle, 3-bottle combos) with customizable JSON schema for titles, subtext, price tags, and CTA buttons.
2. **`sections/purelane-shop.liquid` (Section 02 - Shop / Product Grid):** Connects to Shopify collections or `product_list` settings with responsive 4-column grid rendering.
3. **`sections/purelane-combos.liquid` (Section 03 - Best-selling Combos):** Custom block-based combo section allowing merchants to highlight multi-product packs with instant savings badges.
4. **`sections/purelane-bundles.liquid` (Section 04 - Bundles):** Tiers-based bundle builder with customizable discount badges, perks list, quantity counters, and tier highlight states.
5. **`sections/purelane-reviews.liquid` (Section 05 - Reviews Rail):** Star-rating quote cards with verified reviewer badges and location tags.

### Reusable Components & Edge-Case Protection
- Created **`snippets/purelane-product-card.liquid`** to standardize card rendering across all sections.
- **Sold Out Handling:** Detects `product.available == false`, renders high-contrast `SOLD OUT` pill badge, and disables the action button.
- **Missing Image Fallback:** Handles products without featured media gracefully with a clean SVG placeholder.
- **Long Title Handling:** Implemented `-webkit-line-clamp: 2` and uniform height constraints so titles like *"Ultra-Concentrated Plant-Based Botanical Laundry Detergent for Delicate Fabrics and Sensitive Skin"* never break grid alignment.
- **Fallback Data:** Built dual-mode rendering: sections render live Shopify store products when selected, but gracefully fallback to Purelane prototype data if no products are assigned yet.

### Performance & CSS Styling System
- Isolated all glassmorphism styles, dark gradients, typography (`Outfit` and `Inter` Google Fonts), and background animations into **`assets/purelane-theme.css`**.
- Kept Dawn's core performance lightweight with zero heavy JS dependencies.

---

## 3. What I Would Do With More Time
- **Dynamic Bundle AJAX Cart Customizer:** Add custom JS logic allowing users to pick 3 individual products from modal pickers in `#bundles` and auto-apply tier discount codes at checkout via Shopify AJAX Cart API.
- **Shopify Metaobjects for Reviews:** Convert review cards into a native Shopify Metaobject definition so customer reviews can be managed centrally in Shopify Admin.
- **Live Canvas WebGL Water Shaders:** Upgrade SVG water drift background to a lightweight WebGL shader for 60fps fluid motion on high-refresh-rate displays.
