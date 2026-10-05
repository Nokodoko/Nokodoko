# resume.html — Cyberpunk Terminal Aesthetic Refactor

Standalone HTML resume for Chris Montgomery. Single-file, no build step, self-contained CSS. Matches the n0ko portfolio site's dark server-room aesthetic with terminal-style section headers, cyan glow effects, and clean print styles for PDF generation.

---

## 1. Title + Summary

**resume.html** — Single-file cyberpunk-aesthetic HTML resume. No framework, no dependencies beyond Google Fonts. Served statically from the rayne frontend at `/static/resume.html` and previewed locally via `python -m http.server 3000`.

Refactors the existing `resume.html` to more closely match `STYLE_GUIDE.md` and `style.css` design tokens: deeper section differentiation, glow borders, enhanced terminal prompt headers, and print-safe overrides.

---

## 2. Why

The existing `resume.html` uses the correct color tokens but under-applies the site's visual language:

- **Flat section borders.** `border-bottom: 1px solid var(--border-subtle)` gives sections no visual weight. The site uses cyan glow borders and radial gradient backgrounds for elevation.
- **Missing glow effects.** The site applies `box-shadow: 0 0 20px var(--cyan-glow)` on cards and highlights. The resume has none of this — it reads as a plain dark-mode document, not a terminal artifact.
- **Section headers lack full terminal treatment.** The `> ` prefix exists but the section `<h2>` elements lack the consistent mono font styling, letter-spacing, and visual separation the site uses for `.section-title`.
- **No visual hierarchy between sections.** Each section looks identical. The site alternates radial gradients and uses border-left accents on quote/case-study boxes — only the existing case study box uses this.
- **Print styles are functional but minimal.** No explicit `page-break` control, glow effects not fully zeroed out.

The resume covers one file. This spec covers exactly that surface.

---

## 3. Design Principles

1. **Token fidelity.** Use only CSS custom properties defined in `style.css` — no new hex values except in `@media print` overrides where tokens are redefined to white-background equivalents.
2. **Terminal-first identity.** Every section header uses the `> command` terminal prefix pattern: `> cat /etc/identity`, `> less experience.log`, `> ls ~/projects/`, `> cat stack.yml`, `> cat education.md`. The `> ` prefix renders in `--cyan-400`; the rest in `--text-primary`.
3. **Glow, not gradient.** Follow the style guide: flat colors + `box-shadow` glow effects, never CSS color gradients on UI elements. The server room is hard edges and LED strips.
4. **Print as first-class.** `@media print` must redefine all glow/shadow tokens to zero, background to white, text to near-black. The document must produce a clean PDF with no dark backgrounds or cyan glows.
5. **Content unchanged.** No resume content may be added, removed, or reworded. Structural HTML may be enhanced (additional wrapper divs, class additions) but all text nodes are preserved verbatim.
6. **Single file.** All CSS lives in a `<style>` block in the `<head>`. No external CSS dependencies beyond Google Fonts. No JavaScript.
7. **Readable density.** The resume must read cleanly at browser zoom 100% on a 1280px viewport and print to 1–2 pages at 11pt. Spacing must be tight without being cramped.

---

## 4. On-Disk Format

```
~/Portfolio/rayne/frontend/static/
  resume.html          # Single-file HTML resume (this file)
```

### resume.html

Self-contained HTML5 document. Inline `<style>` block with CSS custom properties (mirroring `style.css` tokens). No external scripts. Google Fonts loaded via `<link>`. All resume content in semantic HTML.

Structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- meta, title, Google Fonts link -->
  <style>
    /* CSS custom properties (mirrors style.css tokens) */
    /* @media print overrides */
    /* component styles */
  </style>
</head>
<body>
  <header class="resume-header">...</header>
  <main>
    <section class="resume-section" id="profile">...</section>
    <section class="resume-section" id="skills">...</section>
    <section class="resume-section" id="experience">...</section>
    <section class="resume-section" id="projects">...</section>
    <section class="resume-section" id="education">...</section>
  </main>
  <div class="back-link no-print">...</div>
</body>
</html>
```

---

## 5. Data Model

Not applicable. This project has no structured data entities — it is a static HTML document with no data layer, APIs, or persistent state.

---

## 6. CLI

Not applicable. This project is a static HTML file with no CLI interface.

---

## 7. JSON Output Format

Not applicable. This project has no JSON API or structured output.

---

## 8. Concurrency Model

Not applicable. Single static file with no concurrent access scenarios.

---

## 9. Migration

| Component | Current (resume.html v1) | Target (resume.html v2) |
|-----------|--------------------------|--------------------------|
| Section headers | `border-bottom: 1px solid var(--border-subtle)` | Gradient divider line + left cyan glow accent |
| Section background | Flat `var(--bg-void)` | Alternating with subtle radial gradient (even sections) |
| Skills grid | Plain label/value pairs | Skill category rows with cyan label, subtle hover state |
| Case study box | `border-left: 3px solid var(--cyan-400)` | Enhanced: add `box-shadow: 0 0 20px var(--cyan-glow)`, background `var(--bg-secondary)` |
| Project cards | `border: 1px solid var(--border-subtle)`, no hover | Add hover: `border-color: var(--cyan-400)`, `box-shadow: 0 0 20px var(--cyan-glow)` |
| Section title `> ` prefix | `color: var(--green-500)` | `color: var(--cyan-400)` to match STYLE_GUIDE convention |
| Print styles | Basic token overrides | Full `box-shadow: none`, `text-shadow: none`, `page-break-inside: avoid` on jobs |
| Header | Plain cyan text name | Name with `text-shadow: var(--shadow-cyan)` glow |
| Body max-width | `900px` | `860px` (tighter, resume-appropriate) |

Migration is a direct in-place edit of `/home/n0ko/Portfolio/rayne/frontend/static/resume.html`. No scripts. No data transformation.

---

## 10. Integration

### rayne Frontend (Go/templ)

The resume is served as a static file by the rayne frontend's `http.FileServer`. No route registration needed — files in `static/` are served automatically at `/static/<filename>`.

```
GET /static/resume.html → served by Go http.FileServer from frontend/static/
```

### Local Preview

```bash
# Start preview server
cd ~/Portfolio/rayne/frontend/static && python -m http.server 3000 &

# Access at:
# http://localhost:3000/resume.html
```

### Print / PDF Generation

Open `http://localhost:3000/resume.html` in Chromium, use `Ctrl+P` → Save as PDF. The `@media print` block ensures clean output.

---

## 11. What It Does NOT Do

- **Not a template engine.** Content is hardcoded HTML — no Jinja2, Go templ, or any server-side rendering. If resume content changes, edit the HTML directly.
- **Not responsive beyond print.** The resume is optimized for desktop viewport and print. No mobile-specific breakpoints are required (resumes are not read on phones in production contexts).
- **Not a portfolio page.** The resume does not include the Monty chat widget, navigation bar, or hero banner. It is a document, not the site.
- **Not versioned in the spec.** Version/date management of resume content is outside scope.

---

## 12. Tech Stack

| Concern | Choice | Rationale |
|---------|--------|-----------|
| Markup | HTML5 | Semantic, universally renderable, print-native |
| Styling | Vanilla CSS (inline `<style>`) | No build step, matches suckless philosophy, single-file |
| Fonts | Google Fonts CDN (JetBrains Mono, Inter) | Exact match to `STYLE_GUIDE.md` font stack |
| Design tokens | CSS custom properties (mirrors `style.css`) | Token consistency with portfolio site without shared stylesheet |
| Serving (dev) | `python -m http.server 3000` | Zero-dependency, no Node, no Go build required for preview |
| Serving (prod) | Go `http.FileServer` in rayne frontend | Already configured, zero additional setup |
| PDF export | Browser print dialog | No headless Chrome required for one-off export |

---

## 13. Project Infrastructure

### Directory Structure

```
~/Portfolio/rayne/frontend/
  static/
    resume.html          # Target file (this spec)
    css/
      style.css          # Design token source — READ ONLY, not modified
    js/
      chat.js
  STYLE_GUIDE.md         # Design token reference — READ ONLY
```

### Version Management

No version file. The file is tracked in git alongside the rayne frontend. Commit message convention: `feat: update resume — <change summary>`.

### CI Workflow

Not applicable. Static HTML file, no build pipeline.

### Scripts

```json
{
  "scripts": {
    "preview": "cd ~/Portfolio/rayne/frontend/static && python -m http.server 3000"
  }
}
```

---

## 14. Estimated Size

| Area | Files | LOC |
|------|-------|-----|
| HTML structure | 1 | ~120 |
| Inline CSS | 1 (same file) | ~250 |
| Print CSS | 1 (same file, `@media print`) | ~40 |
| **Total** | **1** | **~410** |

---

## 15. Task Manifest

| ID | Agent | Description | File Scope (read) | File Scope (write) | Depends On | Verify Command |
|----|-------|-------------|-------------------|--------------------|------------|----------------|
| T1 | `unix-coder` | Read source files: existing resume.html, style.css, STYLE_GUIDE.md to extract all design tokens and current resume content | `resume.html`, `style.css`, `STYLE_GUIDE.md` | — | — | `echo "read complete"` |
| T2 | `unix-coder` | Rewrite resume.html with enhanced cyberpunk aesthetic: cyan glow borders, terminal section headers, hover effects on project cards, enhanced case study box, alternating section backgrounds, print styles | `resume.html`, `style.css`, `STYLE_GUIDE.md` | `~/Portfolio/rayne/frontend/static/resume.html` | T1 | `test -f ~/Portfolio/rayne/frontend/static/resume.html && grep -q 'shadow-cyan' ~/Portfolio/rayne/frontend/static/resume.html` |
| T3 | `unix-coder` | Start Python HTTP server on port 3000 from the static directory for local preview | `~/Portfolio/rayne/frontend/static/resume.html` | — | T2 | `curl -s http://localhost:3000/resume.html | grep -q 'Chris Montgomery'` |

---

## 16. Dependency Graph

```
Phase 1 (sequential): T1 → T2 → T3

T1: Read source files (resume.html, style.css, STYLE_GUIDE.md)
  ↓
T2: Write enhanced resume.html with full cyberpunk aesthetic
  ↓
T3: Start python -m http.server 3000 for local preview
```

---

## 17. Target State

Files created: None

Files modified:
- `~/Portfolio/rayne/frontend/static/resume.html`

Files deleted: None

---

## 18. Verification Plan

**Per-task checks:**
- T1: `echo "read complete"` — trivial gate, just confirms reads ran
- T2: `test -f ~/Portfolio/rayne/frontend/static/resume.html && grep -q 'shadow-cyan' ~/Portfolio/rayne/frontend/static/resume.html`
- T3: `curl -s http://localhost:3000/resume.html | grep -q 'Chris Montgomery'`

**Integration check:** `curl -s http://localhost:3000/resume.html | grep -c '<section' | grep -qE '[5-9]'` — confirms at least 5 sections are present in the rendered document.

**Rollback:** `git checkout -- ~/Portfolio/rayne/frontend/static/resume.html` restores the prior version. The Python server can be killed with `pkill -f 'http.server 3000'`.

---

## 19. Success Criteria (Machine-Verifiable)

- [ ] `test -f ~/Portfolio/rayne/frontend/static/resume.html` exits 0
- [ ] `grep -q 'Chris Montgomery' ~/Portfolio/rayne/frontend/static/resume.html` exits 0
- [ ] `grep -q 'shadow-cyan' ~/Portfolio/rayne/frontend/static/resume.html` exits 0 (glow applied)
- [ ] `grep -q '@media print' ~/Portfolio/rayne/frontend/static/resume.html` exits 0 (print styles present)
- [ ] `grep -q 'box-shadow: none' ~/Portfolio/rayne/frontend/static/resume.html` exits 0 (glows zeroed in print)
- [ ] `grep -q 'less experience.log' ~/Portfolio/rayne/frontend/static/resume.html` exits 0 (terminal section headers)
- [ ] `grep -q 'cat stack.yml' ~/Portfolio/rayne/frontend/static/resume.html` exits 0
- [ ] `grep -q 'ls ~/projects' ~/Portfolio/rayne/frontend/static/resume.html` exits 0
- [ ] `grep -q 'ECCO Select' ~/Portfolio/rayne/frontend/static/resume.html` exits 0 (all experience preserved)
- [ ] `grep -q 'rayne' ~/Portfolio/rayne/frontend/static/resume.html` exits 0 (projects preserved)
- [ ] `grep -q 'Nichols College' ~/Portfolio/rayne/frontend/static/resume.html` exits 0 (education preserved)
- [ ] `grep -q '60%' ~/Portfolio/rayne/frontend/static/resume.html` exits 0 (case study metrics preserved)
- [ ] `curl -s http://localhost:3000/resume.html | grep -q 'Chris Montgomery'` exits 0 (server running)

---

## Agent Assignments

| Task | Agent | Rationale |
|------|-------|-----------|
| Read all source files | `unix-coder` | File I/O task, no architecture decisions |
| Write enhanced resume.html | `unix-coder` | Implementation task: HTML/CSS authoring, token application |
| Start preview server | `unix-coder` | Shell command execution |

---

## Execution Order

```
Phase 1: Read
  └── T1: Read resume.html, style.css, STYLE_GUIDE.md (agent: unix-coder)

Phase 2: Write [blocked by Phase 1]
  └── T2: Rewrite resume.html with enhanced aesthetic (agent: unix-coder)

Phase 3: Serve [blocked by Phase 2]
  └── T3: python -m http.server 3000 & (agent: unix-coder)
```

Recommended directive: `/pai` — plan-then-implement pipeline suits this single-agent linear task.

---

## Failure Modes

| Failure | Detection | Recovery |
|---------|-----------|----------|
| Port 3000 already in use | `curl` to T3 fails; `Address already in use` error | `pkill -f 'http.server 3000'` then retry |
| resume.html content truncated | `grep -c '<section'` returns < 5 | Re-run T2 with explicit section count check in task prompt |
| Glow effects missing | T2 verify command fails on `shadow-cyan` grep | Check that `--shadow-cyan` token is defined in `:root` block of the file |
| Print styles absent | `grep -q '@media print'` fails | Re-run T2; the print block was likely dropped |
| Google Fonts blocked (offline) | Page renders with fallback monospace fonts | Expected behavior offline — fallback stack (`'Fira Code', Consolas`) is acceptable |

---

## Detailed Design: CSS Enhancements Required

The following enumerates the specific changes T2 must apply, derived from comparing the existing `resume.html` against `STYLE_GUIDE.md`:

### 1. Section Header Enhancement

**Current:**
```css
.section h2 {
  color: var(--cyan-400);
  border-bottom: 1px solid var(--border-subtle);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
.section h2 .prompt {
  color: var(--green-500);  /* WRONG — should be cyan-400 per style guide */
}
```

**Target:**
```css
.resume-section h2 {
  font-family: var(--font-mono);
  font-size: 1.0rem;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: 0.04em;
  padding-bottom: 0.5rem;
  margin-bottom: 0.9rem;
  /* Gradient divider line matching style guide section-divider pattern */
  border-bottom: 1px solid transparent;
  background-image: linear-gradient(var(--bg-void), var(--bg-void)),
    linear-gradient(90deg, transparent 0%, var(--cyan-400) 20%, var(--cyan-400) 80%, transparent 100%);
  background-origin: border-box;
  background-clip: padding-box, border-box;
  /* Simpler fallback: keep 1px border, add glow */
}
.resume-section h2 .prompt {
  color: var(--cyan-400);  /* FIX: was green-500 */
}
```

**Simpler implementation (preferred for single-file):**
Use a `::after` pseudo-element for the gradient divider, avoid the multi-background border hack:

```css
.resume-section h2 {
  font-family: var(--font-mono);
  font-size: 1.0rem;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: 0.04em;
  padding-bottom: 0.5rem;
  margin-bottom: 0.9rem;
  position: relative;
}
.resume-section h2::after {
  content: '';
  display: block;
  height: 1px;
  background: linear-gradient(90deg, var(--cyan-400) 0%, var(--cyan-400) 60%, transparent 100%);
  opacity: 0.25;
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
}
.resume-section h2 .prompt {
  color: var(--cyan-400);
}
```

### 2. Section Alternating Background

Even-indexed sections get the subtle radial gradient matching `style.css`:

```css
.resume-section:nth-of-type(even) {
  background:
    radial-gradient(ellipse at 20% 50%, rgba(0, 212, 255, 0.03) 0%, transparent 60%),
    var(--bg-void);
  padding: 0.75rem 0;
  margin: 0 -0.5rem;
  padding-left: 0.5rem;
  padding-right: 0.5rem;
  border-radius: 6px;
}
```

### 3. Project Card Hover

```css
.project {
  background: var(--bg-secondary);
  border: 1px solid var(--border-subtle);
  padding: 0.5rem 0.75rem;
  border-radius: 4px;
  transition: border-color 0.25s ease, box-shadow 0.25s ease;
}
.project:hover {
  border-color: var(--cyan-400);
  box-shadow: var(--shadow-card), 0 0 20px var(--cyan-glow);
}
```

### 4. Case Study Box Glow

```css
.case-study-box {
  background: var(--bg-secondary);
  border: 1px solid var(--border-subtle);
  border-left: 3px solid var(--cyan-400);
  padding: 0.75rem 1rem;
  margin: 0.5rem 0;
  border-radius: 4px;
  box-shadow: 0 0 20px var(--cyan-glow);  /* ADD */
}
```

### 5. Header Name Glow

```css
.resume-header h1 {
  font-family: var(--font-mono);
  font-size: 2rem;
  font-weight: 700;
  color: var(--cyan-400);
  text-shadow: var(--shadow-cyan);  /* ADD — matches hero-title pattern */
  margin-bottom: 0.15rem;
}
```

### 6. Print Styles (Complete Override)

```css
@media print {
  :root {
    --bg-void: #fff;
    --bg-primary: #fff;
    --bg-secondary: #f7f7f7;
    --bg-tertiary: #efefef;
    --bg-surface: #f0f0f0;
    --cyan-400: #0077aa;
    --cyan-500: #006699;
    --green-500: #007744;
    --red-500: #cc2222;
    --text-primary: #111;
    --text-secondary: #444;
    --text-muted: #777;
    --border-subtle: #ddd;
    --cyan-glow: transparent;
    --cyan-glow-strong: transparent;
    --shadow-cyan: none;
    --shadow-card: none;
    --shadow-elevated: none;
  }
  body {
    padding: 0;
    font-size: 10.5pt;
    max-width: 100%;
    color: #111;
  }
  .no-print { display: none !important; }
  a { color: var(--cyan-400); text-decoration: none; }
  .resume-header { border-bottom-color: var(--cyan-400); }
  .resume-section h2::after { opacity: 0.4; }
  .job { page-break-inside: avoid; }
  .project { page-break-inside: avoid; }
  .case-study-box {
    box-shadow: none;
    border-left-color: var(--cyan-400);
  }
  .project:hover {
    box-shadow: none;
    border-color: var(--border-subtle);
  }
  /* Zero all glows */
  * {
    box-shadow: none !important;
    text-shadow: none !important;
  }
  .resume-header h1 {
    text-shadow: none !important;
    color: #003366;
  }
}
```

### 7. Skill Category Styling

```css
.skill-category {
  margin-bottom: 0.25rem;
  display: flex;
  gap: 0.4rem;
  align-items: baseline;
  flex-wrap: wrap;
}
.skill-category .label {
  font-family: var(--font-mono);
  font-size: 0.78rem;
  font-weight: 600;
  color: var(--cyan-400);
  white-space: nowrap;
  /* Add subtle cyan glow matching .tag pattern */
  text-shadow: 0 0 8px rgba(0, 212, 255, 0.3);
}
.skill-category .items {
  font-size: 0.83rem;
  color: var(--text-secondary);
  font-family: var(--font-sans);
}
```

---

## Open Questions

None. The task is fully specified.
