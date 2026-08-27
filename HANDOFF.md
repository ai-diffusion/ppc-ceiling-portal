# Decorative CFB Trade Portal — Project Handoff & Technical Audit

## Executive Summary
This project hosts a high-converting, trust-building **Polymer Paper Composite (PPC) & Decorative CFB Order Portal** and **Admin Analytics Dashboard**. The portal serves as a self-contained static site hosted on **GitHub Pages**, backed by a live **Supabase PostgreSQL** instance (`jzofcuonpbudcwvwwymu`) over HTTPS.

---

## 1. Project Infrastructure & Repositories

| Layer | URL / Location | Details |
|---|---|---|
| **Live Customer Portal** | `https://ai-diffusion.github.io/ppc-ceiling-portal/index.html` | Self-contained HTML/JS static app |
| **Live Admin Dashboard** | `https://ai-diffusion.github.io/ppc-ceiling-portal/admin.html` | Analytics, heatmaps, PO export |
| **GitHub Deployment Repo** | `https://github.com/ai-diffusion/ppc-ceiling-portal` | Public repository serving GitHub Pages |
| **Source Catalog & Asset Repo** | `https://github.com/ai-diffusion/decorative-cfb-trade-portal` | Public source repo housing `catalog.json` & swatch images |
| **Live Supabase Instance** | `https://jzofcuonpbudcwvwwymu.supabase.co` | Project ref: `jzofcuonpbudcwvwwymu` (Region: Mumbai) |
| **CDN Image Base** | `https://cdn.jsdelivr.net/gh/ai-diffusion/decorative-cfb-trade-portal@main/public` | Hosts `/swatches/` and `/sheets/` for all 1,587 SKUs |

---

## 2. Catalog Audit & Image Hosting Architecture

### Catalog Dataset (`catalog.json`)
- **Total Designs**: 750 unique design families
- **Total Colourways (SKUs)**: 1,587 individual variants
- **Series Breakdown**:
  - `VOGUE`: 265 designs
  - `LINO`: 214 designs
  - `NATURAL`: 111 designs
  - `NXT`: 78 designs
  - `COLORPEDIA`: 63 designs
  - `SIGNATURE`: 14 designs
  - `ATELIER`: 3 designs
  - `INSULATION`: 2 designs
- **Suppliers**:
  - `Baaya`: 17 designs (Supplier Code Suffix: `YP`)
  - `Match Graphics`: 731 designs (Supplier Code Suffix: `XK`)

### Image Storage & CDN Delivery
- **Where are the images stored?**
  Images are hosted in the `decorative-cfb-trade-portal` repository under `public/swatches/` (600×600 tile swatches) and `public/sheets/` (600×1217 full sheet view), and delivered globally via **jsDelivr CDN**:
  - Swatch: `https://cdn.jsdelivr.net/gh/ai-diffusion/decorative-cfb-trade-portal@main/public/swatches/<file>.jpg`
  - Sheet: `https://cdn.jsdelivr.net/gh/ai-diffusion/decorative-cfb-trade-portal@main/public/sheets/<file>.jpg`
- **Benefits**:
  - $0 hosting cost.
  - Sub-second asset streaming directly inside `index.html`.
  - Keeps `ppc-ceiling-portal` GitHub Pages repository lightweight (~75 KB HTML file).

---

## 3. Trade Code Format & Supplier Masking Rule

### Required Trade Code Format
`PPC-{4hex}-{realSku}-{YP|XK}`

- **Examples**:
  - `PPC-AD28-9034-01-YP` (Baaya supplier design)
  - `PPC-D2AB-21233-01-XK` (Match Graphics supplier design)
- **Masking Logic**:
  - The customer-facing trade code contains the SKU positioned in the middle, with a 4-character hex prefix and supplier identifier suffix (`YP` for Baaya, `XK` for Match Graphics).
  - Unmasked supplier codes (`real_baaya_code` / supplier internal SKUs) are stored in the Supabase database and rendered **only** in `admin.html` for Purchase Order (PO) export.

---

## 4. Feature Matrix: What's Kept, Merged, and Removed

### ✅ KEPT & INTEGRATED:
1. **Share / Vote Board (`/board/$slug`)**: Allows distributors to generate a branded voting/pre-order board for their end-clients to comment, vote, and select ceiling patterns.
2. **Face / Reverse Construction Spec**: Choice of single-sided (`PPC`) or double-sided (`WPPPFCW`) ceiling panel specification.
3. **Insulation Add-on**: Integrated `MIPPC` / `MIPFC` insulation core options.
4. **Admin Analytics Dashboard (`admin.html`)**:
   - Real-time customer sessions & active time.
   - Design view counter & zoom interaction tracking.
   - Canvas-rendered **Mouse-Movement Heatmap**.
   - Purchase Order (PO) export tool with unmasked real supplier codes.
5. **Customer Wishlist & Order History**: Database-synced saved items and order track record.

### ❌ REMOVED:
1. **T-15 Profile / Channel Knowledge**: Completely stripped all references to T-15/T-24 grid channels, channel dimensions, and profile mounting hardware per explicit directive.
2. **Door / 1850 mm Options**: Excluded non-standard door sizes.
3. **Redundant Search/Pagination Overhead**: Clean fast client-side series filtering.

---

## 5. Maintenance & Deployment Workflow

### Updating the Site Code
To update `index.html` or `admin.html` on GitHub Pages:
```bash
git add index.html admin.html .gitignore
git commit -m "Update portal features"
git push
```
GitHub Pages automatically redeploys within 1–2 minutes.

### Database Credentials & Anon Key
- **Supabase URL**: `https://jzofcuonpbudcwvwwymu.supabase.co`
- **Anon Public Key**: Safe to commit in static HTML files (enforced via Supabase Row Level Security).
