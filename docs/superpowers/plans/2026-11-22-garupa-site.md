# Garupá Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a one-page institutional site for Garupá (environmental consulting, founded 2023 by Camila Bonilha and Mariana Vieira), presenting the company, team, services and projects before directing visitors to contact.

**Architecture:** Single static HTML file with embedded CSS (no build step, no framework), matching the sibling `pott-product-studio` site's convention. Mobile-first responsive layout. Real photos and brand assets extracted from the client-provided materials (presentation PDF, flyers) rather than stock imagery. Deployed as a new standalone git repo, published via GitHub Pages (no custom domain yet).

**Tech Stack:** HTML5, CSS3 (custom properties, flexbox/grid), vanilla JS only if needed for a mobile nav toggle. Google Fonts. Python3 + Pillow (already installed) and poppler (`pdfimages`/`pdftoppm`, already installed) used offline during asset prep, not shipped to the site.

**Spec:** This plan was produced directly from a brainstorming conversation (no separate spec file) covering: company materials in `/Users/crislachill/Downloads/garupa/` (presentation PDF, 2 flyers, Lattes CVs for both founders) and two reference sites (Biometria, Harpya) reviewed for structural/tone cues only — not copied.

## Global Constraints

- Single static `index.html` with embedded `<style>`; no build tooling, no JS framework.
- Content language: Portuguese (pt-BR), `lang="pt-BR"`.
- Palette: wine/burgundy + cream, sampled precisely from the client's own flyer (`Flyer digital.png`) during Task 1 — do not guess hex values in later tasks.
- No contact form / no backend — only `tel:` and `mailto:` links. No single WhatsApp CTA button yet (explicitly deferred by the user) — list both phone numbers as plain contact info instead.
- Mobile-first responsive; must work down to ~360px width with no horizontal scroll.
- Deploy target: new git repo at `~/Documents/garupa-site`, GitHub Pages, no custom domain (friend hasn't bought one yet).
- Projetos/Cases section content (specific case studies) is **not yet available** — build the section structurally with clearly-flagged placeholder content, to be swapped for real cases once the user sends them.

## Review Focus

- Mobile layout: hero text, nav, and service/team cards must not overflow or require horizontal scroll at 360–414px widths (common phone sizes for this audience).
- `tel:`/`mailto:` links must use correctly formatted hrefs (`tel:+5551982883311` style, no stray spaces/pipes from the flyer's display format) so tapping actually dials/opens mail.
- UTF-8 accented characters (á, ã, ç, é, ô) must render correctly everywhere — missing `<meta charset="UTF-8">` or wrong encoding on save would corrupt names like "Garupá" and "Mariana".
- Placeholder case-study content must be visually/textually distinguishable (or clearly called out to the user) so it can't accidentally be mistaken for real, publishable client case studies.
- Cropped team/hero photos must not cut off faces or key subject matter — verify crops visually before finalizing.

---

## Task 1: Project skeleton + asset extraction

**Files:**
- Create: `~/Documents/garupa-site/` (new git repo)
- Create: `~/Documents/garupa-site/assets/` (images)
- Create (scratch, not committed): extraction workspace in the session scratchpad

**Interfaces:**
- Produces: final asset files in `assets/` that later tasks reference by exact filename:
  - `assets/logo-mark.png` (the small arch "GA/RU/PÁ" badge icon)
  - `assets/hero.jpg` (field/landscape photo for the hero section)
  - `assets/camila.jpg`, `assets/mariana.jpg` (team headshots, cropped to a consistent portrait aspect ratio, e.g. 3:4)
  - `assets/fieldwork-1.jpg`, `assets/fieldwork-2.jpg` (supporting photos for services/área de atuação)
  - `palette.txt` (sampled hex values, see below) — reference only, not shipped

- [ ] **Step 1: Init the new repo**
  Run: `mkdir -p ~/Documents/garupa-site/assets && cd ~/Documents/garupa-site && git init`
  Expected: `.git` created, empty working tree.

- [ ] **Step 2: Extract embedded raster images from the presentation PDF**
  Run: `pdfimages -png "/Users/crislachill/Downloads/garupa/Apresentação Garupá.pdf" <scratch>/raw` then inspect each via the Read tool.
  Expected: a set of PNGs including the cover floral watermark, the field/landscape photo, the fieldwork-with-person photo, the two team headshots, and the small recurring arch logo icon (91×122).

- [ ] **Step 3: Sample exact palette hex values with Pillow**
  Write a small throwaway script (scratch only) that opens `Flyer digital.png`, samples a pixel from the wine banner/heading text and from the cream background, and prints hex. Save results to `palette.txt`.
  Expected: two hex values close to `#6b1530` (wine) and `#f2ede1` (cream); exact values used in Task 2.

- [ ] **Step 4: Crop and export final assets**
  Using Pillow, crop: the arch logo icon → `assets/logo-mark.png` (trim to its bounding box, keep transparency if present); the best landscape/field photo → `assets/hero.jpg` (16:9 or wider crop, no upscaling beyond source resolution); the two headshots → `assets/camila.jpg` / `assets/mariana.jpg` (consistent portrait crop centered on the face); two more fieldwork photos → `assets/fieldwork-1.jpg`, `assets/fieldwork-2.jpg`.
  Expected: 6 files in `assets/`, each opened and visually confirmed with the Read tool (face not cut off, no stray background from other slide elements).

- [ ] **Step 5: Commit**
  ```bash
  cd ~/Documents/garupa-site
  git add assets
  git commit -m "Add extracted brand assets and photos"
  ```

---

## Task 2: HTML skeleton, head, and base design system

**Files:**
- Create: `~/Documents/garupa-site/index.html`

**Interfaces:**
- Consumes: hex values from `palette.txt` (Task 1), `assets/logo-mark.png`
- Produces: CSS custom properties (`--wine`, `--cream`, `--text`, `--text-dim`, a serif heading font-family var, a sans body font-family var) that every later task's styles reuse by name — do not invent new color/font variables in later tasks.

- [ ] **Step 1: Write `<head>`**
  `<!DOCTYPE html>`, `lang="pt-BR"`, `<meta charset="UTF-8">`, viewport meta, `<title>Garupá — Projetos e Pesquisas Ambientais</title>`, meta description (one line summarizing licenciamento/recuperação de áreas degradadas in Pampa/Mata Atlântica, RS), Open Graph tags, a JSON-LD `LocalBusiness` block (name "Garupá", description, email `garupaprojetosambientais@gmail.com`, areaServed "Rio Grande do Sul, Brasil"), Google Fonts `<link>` for one serif display font (heading) + one sans font (body).

- [ ] **Step 2: Write the CSS custom-property block**
  `:root { --wine: <sampled>; --cream: <sampled>; --text: #2a2420-ish; --text-dim: ...; --font-display: ...; --font-body: ...; }`, plus `box-sizing: border-box` reset, smooth scroll, base body styles (cream background, dark text, body font).

- [ ] **Step 3: Build header/nav**
  Fixed/sticky header with `assets/logo-mark.png` + "GARUPÁ" wordmark text (styled in the display font, letter-spaced, mimicking the real logotype), and anchor nav links to each section (`#sobre #equipe #servicos #atuacao #projetos #contato`). Include a simple CSS-only or minimal-JS hamburger toggle for mobile.

- [ ] **Step 4: Build the hero section**
  `assets/hero.jpg` as background or side image, headline (one strong line describing what Garupá does), subheadline, no WhatsApp CTA button per the deferred decision — just scroll-to-contact anchor link styled as a secondary button.

- [ ] **Step 5: Visual verification**
  Open `index.html` in the browser pane (`navigate` to the `file://` path or serve via `preview_start`), take a screenshot at desktop width and at `resize_window` `mobile` preset.
  Expected: header, logo, and hero render with no layout overflow at both widths; cream/wine colors match the sampled palette; accented characters ("Garupá", "é", "ã", "ç") render correctly, not as mojibake.

- [ ] **Step 6: Commit**
  ```bash
  git add index.html
  git commit -m "Add HTML skeleton, head metadata, header and hero"
  ```

---

## Task 3: Sobre + Equipe sections

**Files:**
- Modify: `index.html` (append `#sobre` and `#equipe` sections)

**Interfaces:**
- Consumes: `--wine`/`--cream`/font vars from Task 2; `assets/camila.jpg`, `assets/mariana.jpg` from Task 1.

- [ ] **Step 1: Write the "Sobre" section**
  Content drawn from the presentation: founded 2023, consultoria especializada em conservação e restauração de vegetação nativa nos biomas Pampa e Mata Atlântica (RS), atuação junto a organizações para cumprimento de normas ambientais. Include the "garupá" plant symbolism paragraph (Aloysia gratissima) as a short aside/callout.

- [ ] **Step 2: Write the "Equipe" section**
  Two cards, Camila Leal Bonilha and Mariana de Souza Vieira, each: photo, name, "Bióloga, Dra. em botânica", and their distinct experience blurb from the presentation (Camila: analista ambiental, PRADs, EIAS; Mariana: restauração de áreas degradadas, controle de invasoras, elaboração de estudos de flora).

- [ ] **Step 3: Visual verification**
  Screenshot desktop + mobile; confirm both cards stack cleanly on narrow widths and photos aren't distorted (use `object-fit: cover` with a fixed aspect-ratio box).

- [ ] **Step 4: Commit**
  ```bash
  git add index.html
  git commit -m "Add Sobre and Equipe sections"
  ```

---

## Task 4: Serviços + Área de atuação sections

**Files:**
- Modify: `index.html` (append `#servicos` and `#atuacao` sections)

**Interfaces:**
- Consumes: `assets/fieldwork-1.jpg` / `fieldwork-2.jpg`.

- [ ] **Step 1: Write the "Serviços" section**
  Card/grid of the full service list from the flyer: Laudo de Cobertura Vegetal Florestal; Laudo de Cobertura Vegetal Campestre; Projetos de podas e transplantes de árvores; Laudo de Fauna; Plano de Recuperação de Área Degradada (PRAD); Projetos de Educação Ambiental; Projeto de Reposição Florestal Obrigatória; Estudos de Impacto Ambiental (EIA/RIMA); Controle de espécies exóticas invasoras; Coleta, análise e monitoramento de dados ambientais; Laudos geológicos, geotécnicos, pedológicos e hidrogeológicos. Each item gets a small icon (reuse simple inline SVG line icons, not copies of the flyer's exact icon art) and short label.

- [ ] **Step 2: Write the "Área de atuação" section**
  Short paragraph: atuação nos biomas Pampa e Mata Atlântica, Rio Grande do Sul, paired with `assets/fieldwork-1.jpg`/`fieldwork-2.jpg`.

- [ ] **Step 3: Visual verification**
  Screenshot desktop + mobile; confirm the service grid reflows to 1 column on narrow widths and no list item text is clipped.

- [ ] **Step 4: Commit**
  ```bash
  git add index.html
  git commit -m "Add Serviços and Área de atuação sections"
  ```

---

## Task 5: Projetos/Cases section (placeholder content)

**Files:**
- Modify: `index.html` (append `#projetos` section)

**Interfaces:**
- Produces: a repeatable `.case-card` markup block (image, title, 1-2 line description, outcome stat) that is trivial to find-and-replace once real case data arrives.

- [ ] **Step 1: Write 3 placeholder case cards**
  Each visually marked as a placeholder in an HTML comment directly above it (e.g. `<!-- PLACEHOLDER: replace with real case from Camila/Mariana -->`) so it's unmissable in source, with clearly generic placeholder copy (not invented fake client names or fabricated metrics).

- [ ] **Step 2: Visual verification**
  Screenshot desktop + mobile; confirm cards reflow correctly.

- [ ] **Step 3: Commit**
  ```bash
  git add index.html
  git commit -m "Add Projetos section with placeholder case cards"
  ```

---

## Task 6: Contato + footer

**Files:**
- Modify: `index.html` (append `#contato` section and `<footer>`)

**Interfaces:**
- Consumes: phone numbers `51 982883311`, `53 984215503`, email `garupaprojetosambientais@gmail.com` from the flyer.

- [ ] **Step 1: Write the "Contato" section**
  List both phone numbers as plain text with working `tel:+5551982883311` / `tel:+5553984215503` links, and the email as a `mailto:` link. No single highlighted WhatsApp button (deferred).

- [ ] **Step 2: Write the footer**
  Small logo mark, "© 2025 Garupá — Projetos e Pesquisas Ambientais".

- [ ] **Step 3: Verify link formatting**
  Run: `grep -o 'tel:[^"]*\|mailto:[^"]*' index.html`
  Expected: output shows exactly `tel:+5551982883311`, `tel:+5553984215503`, `mailto:garupaprojetosambientais@gmail.com` — no spaces or pipe characters leaking in from the flyer's display format.

- [ ] **Step 4: Commit**
  ```bash
  git add index.html
  git commit -m "Add Contato section and footer"
  ```

---

## Task 7: Responsive polish, accessibility, and SEO finishing

**Files:**
- Modify: `index.html`
- Create: `~/Documents/garupa-site/favicon-32x32.png`, `favicon-16x16.png`, `apple-touch-icon.png`, `robots.txt`, `sitemap.xml` (mirroring the sibling repo's convention)

- [ ] **Step 1: Generate favicons from `assets/logo-mark.png`**
  Use Pillow to resize to 32×32, 16×16, and 180×180, saved at repo root.

- [ ] **Step 2: Add favicon `<link>` tags and `theme-color` meta to `<head>`**

- [ ] **Step 3: Full responsive sweep**
  Using the browser pane, check widths 360px, 414px, 768px, 1024px, 1440px via `resize_window`; screenshot each; confirm no horizontal scrollbar at any width (check via `javascript_tool`: `document.documentElement.scrollWidth <= document.documentElement.clientWidth`).

- [ ] **Step 4: Check console for errors**
  Use `read_console_messages` with `onlyErrors: true`.
  Expected: empty.

- [ ] **Step 5: Add `robots.txt` and `sitemap.xml`**
  Minimal versions allowing all crawlers, pointing to the eventual site URL.

- [ ] **Step 6: Commit**
  ```bash
  git add favicon-16x16.png favicon-32x32.png apple-touch-icon.png robots.txt sitemap.xml index.html
  git commit -m "Add favicons, SEO meta, and responsive polish"
  ```

---

## Task 8: GitHub repo + Pages deploy

**Files:**
- Create: `.gitignore` (if needed), GitHub remote

**Interfaces:**
- None (final deployment step).

- [ ] **Step 1: Ask the user for explicit go-ahead before creating a public GitHub repo and pushing**
  This is a visible, hard-to-reverse action (creating a repo, pushing code publicly) — confirm with the user first, per the assistant's own operating rules, even though the plan itself was already approved.

- [ ] **Step 2: Create the GitHub repo and push**
  Run: `gh repo create garupa-site --public --source=. --remote=origin --push`
  Expected: repo created under the user's account, `main` branch pushed.

- [ ] **Step 3: Enable GitHub Pages**
  Run: `gh api repos/{owner}/garupa-site/pages -X POST -f "source[branch]=main" -f "source[path]=/"` (or guide the user through Settings → Pages if the API call needs scopes not granted).
  Expected: Pages URL returned, e.g. `https://<owner>.github.io/garupa-site/`.

- [ ] **Step 4: Final live verification**
  Navigate to the published URL in the browser pane, screenshot desktop + mobile.
  Expected: site matches the local build; no broken image links (absolute vs. relative paths resolved correctly on Pages).

- [ ] **Step 5: Commit any final fixes and report the live URL to the user**
