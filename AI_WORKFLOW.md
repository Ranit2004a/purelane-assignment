# Purelane Shopify Assignment — AI Workflow Notes

## 1. What I Delegated
- **Design Token & Style Extraction:** Automated parsing of 1500+ lines of raw prototype CSS into structured variables, utilities (`.purelane-d1`, `.purelane-d2`, `.glass`, `.glass-2`), and responsive breakpoints.
- **Liquid Schema Boilerplate:** Generated Shopify Theme Editor JSON schemas (`{% schema %}`) with presets, block options, default settings, and fallback presets for all 5 sections.
- **Edge-Case Safety Wrappers:** Synthesized Liquid conditional logic for edge cases (sold-out items, missing image placeholders, title truncation).
- **Git Commit Workflow:** Executed granular per-section commits to build a clean git history.

---

## 2. Where AI Failed & Required Manual Intervention
- **Shallow Git Clone Push Rejection:** `shopify theme init` created a shallow git clone (`--depth=1`). Pushing directly to a fresh GitHub repo triggered `remote unpack failed: index-pack failed`. Solved by re-initializing a clean, non-shallow Git repository tracking `main`.
- **Environment PATH Resolution on Windows:** Global npm packages (`@shopify/cli`) were installed at `C:\Users\Asus\AppData\Roaming\npm`, which was missing from Windows `$env:PATH`. Resolved by programmatically updating `$env:PATH` and User Environment variables.
- **Liquid Schema Type Strictness:** Required fixing initial schema presets so Liquid block defaults matched Shopify's schema validation standards without warnings.

---

## 3. What I Would Systematize for 20+ Theme Builds
- **CLI Automated Section Transpiler:** Build an internal Node.js CLI tool that accepts a static HTML section, parses design tokens into a Liquid template, and auto-generates schema JSON settings.
- **Reusable Purelane Component Library:** Standardize a pre-built Shopify snippet library (`purelane-product-card`, `purelane-price-badge`, `purelane-bundle-picker`).
- **One-Command Store Seeding Script:** Create a automated script using Shopify GraphQL Admin API to automatically create 8+ test products with edge-case attributes (sold out, no image, long title) on any fresh development store.
