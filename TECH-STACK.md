# Tech Stack & Development Guide

Technical reference for building new pages in the Savoya website project.

---

## Stack Overview

| Layer | Technology | Notes |
|-------|-----------|-------|
| **Markup** | HTML5 | Single-file pages, semantic elements |
| **Styling** | Tailwind CSS (CDN) | Runtime JIT via `cdn.tailwindcss.com` |
| **Fonts** | Google Fonts | Playfair Display + Inter, loaded with `display=swap` |
| **JavaScript** | Vanilla JS | No frameworks, no build step |
| **Images** | Unsplash + Webflow CDN | External URLs, lazy loaded |
| **Hosting** | GitHub Pages | Static deployment from repo |
| **Version Control** | Git + GitHub | Feature branches, no CI/CD |

---

## Architecture Decisions

### Single-file HTML pages
Each page is a self-contained `.html` file with inline styles and scripts. No build tools, no bundler, no npm.

**Why:** Simplicity, zero dependencies, instant deployment via GitHub Pages, easy to iterate.

**Trade-off:** Tailwind CDN is a ~300KB runtime JIT compiler. For production at scale, switch to Tailwind CLI with purged CSS output.

### No JavaScript frameworks
All interactivity is vanilla JS (~100 lines per page). IntersectionObserver for scroll animations, native scroll-snap for carousels, simple DOM manipulation for tabs/accordions.

**Why:** Zero TBT (Total Blocking Time), tiny JS footprint, no hydration cost.

### External images only
Images are hosted on Unsplash (`images.unsplash.com`) and Webflow CDN (`cdn.prod.website-files.com`). No local image files.

**Why:** No repo bloat, CDN-delivered, resizable via URL parameters.

---

## File Structure

```
website-sample/
  index.html                  # Homepage
  robots.txt                  # Crawler rules
  sitemap.xml                 # Search engine sitemap
  performance-comparison.html # Lighthouse comparison report
  DESIGN-SYSTEM.md            # Visual design reference
  TECH-STACK.md               # This file
  CLAUDE.md                   # AI assistant instructions
```

### Adding a new page
1. Copy the page template from `DESIGN-SYSTEM.md` section 7
2. Save as `page-name.html` in the project root
3. Reuse the header and footer from `index.html`
4. Add the URL to `sitemap.xml`
5. Deploy by pushing to the branch served by GitHub Pages

---

## CSS Framework: Tailwind CSS

### Setup (per page)
```html
<script src="https://cdn.tailwindcss.com"></script>
<script>
  tailwind.config = {
    theme: {
      extend: {
        colors: {
          'lux-bg': '#0a0a0a',
          'lux-surface': '#141414',
          'lux-elevated': '#1a1a1a',
          'gold': '#C5A55A',
          'gold-hover': '#D4B96A',
        },
        fontFamily: {
          'display': ['"Playfair Display"', 'Georgia', 'serif'],
          'body': ['"Inter"', 'system-ui', 'sans-serif'],
        },
        maxWidth: { 'page': '1170px' }
      }
    }
  }
</script>
```

### Custom CSS classes (copy to each page)
These are defined in a `<style>` block and are NOT Tailwind utilities. They must be copied to each new page:

| Class | Purpose |
|-------|---------|
| `.btn-gold` | Primary gold button (solid) |
| `.btn-gold-outline` | Secondary gold button (outline) |
| `.text-gold-gradient` | Gold gradient text effect |
| `.glass-dark` | Frosted glass card background |
| `.gold-glow` | Ambient gold box-shadow |
| `.ambient-orb` | Large blurred gold radial gradient |
| `.reveal` / `.reveal.active` | Scroll-in animation |
| `.word-reveal` | Hero word-by-word animation |
| `.stagger` | Staggered child animation delays |
| `.card-premium` | Card hover lift + shadow |
| `.accordion-content` / `.open` | Expandable accordion body |
| `.accordion-chevron` / `.open` | Rotating chevron icon |
| `.tab-panel` / `.active` | Tab content visibility |
| `.scroll-hide` | Hide scrollbar on overflow containers |

Full CSS source: see `<style>` block in `index.html` lines 49-154.

### Production migration path
When moving to production:
1. Install Tailwind CLI: `npm install -D tailwindcss`
2. Create `tailwind.config.js` with the extend block above
3. Run `npx tailwindcss -i input.css -o output.css --minify`
4. Replace CDN script with `<link rel="stylesheet" href="output.css">`
5. Move custom classes to `input.css` using `@layer components`

---

## Fonts: Google Fonts

### Loading
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@300;400;600&family=Inter:wght@400;500&display=swap" rel="stylesheet">
```

### Usage rules
- **Playfair Display** (`font-display`): Headings, section titles, quotes, stat numbers. Never for body text.
- **Inter** (`font-body`): Body text, labels, buttons, navigation, forms. Default for everything.
- **Weights loaded:** Playfair 300/400/600, Inter 400/500. Do not add more weights without good reason.
- **`display=swap`**: Required. Prevents invisible text during font load (FOIT).
- **Preconnect**: Required. Both `fonts.googleapis.com` and `fonts.gstatic.com` (with `crossorigin`).

---

## JavaScript Patterns

### No dependencies
All JS is vanilla. Do not add jQuery, Alpine, HTMX, or any library.

### Standard JS modules used across pages

**1. Scroll Reveal (IntersectionObserver)**
```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) entry.target.classList.add('active');
  });
}, { threshold: 0.2 });
document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
```

**2. Header glass effect on scroll**
```javascript
window.addEventListener('scroll', () => {
  const header = document.getElementById('site-header');
  if (window.scrollY > 50) {
    header.style.background = 'rgba(10,10,10,0.85)';
    header.style.backdropFilter = 'blur(20px)';
    header.style.borderColor = 'rgba(255,255,255,0.06)';
  } else {
    header.style.background = 'transparent';
    header.style.backdropFilter = 'none';
    header.style.borderColor = 'transparent';
  }
});
```

**3. Accordion toggle**
```javascript
function toggleAccordion(btn) {
  const content = btn.nextElementSibling;
  const chevron = btn.querySelector('.accordion-chevron');
  const isOpen = content.classList.contains('open');
  // Close all
  document.querySelectorAll('.accordion-content').forEach(c => c.classList.remove('open'));
  document.querySelectorAll('.accordion-chevron').forEach(c => c.classList.remove('open'));
  document.querySelectorAll('[aria-expanded]').forEach(b => b.setAttribute('aria-expanded', 'false'));
  // Open clicked (if was closed)
  if (!isOpen) {
    content.classList.add('open');
    chevron.classList.add('open');
    btn.setAttribute('aria-expanded', 'true');
  }
}
```

**4. Tab switching**
```javascript
function switchTab(btn) {
  document.querySelectorAll('.tab-btn').forEach(b => {
    b.classList.remove('active', 'text-white', 'border-gold');
    b.classList.add('text-white/40', 'border-transparent');
  });
  document.querySelectorAll('.tab-panel').forEach(p => p.classList.remove('active'));
  btn.classList.add('active', 'text-white', 'border-gold');
  btn.classList.remove('text-white/40', 'border-transparent');
  document.getElementById('panel-' + btn.dataset.tab).classList.add('active');
}
```

**5. Stat counter animation**
```javascript
// Triggered via IntersectionObserver at threshold 0.3
// Animates from 0 to data-target over 2200ms with easeOutQuart
// Supports data-suffix, data-decimals, data-comma attributes
```

**6. Carousel with dot navigation**
```javascript
// Scroll-snap based, scrollTo for dot clicks
// Scroll listener syncs active dot with scroll position
```

### Script placement
All `<script>` blocks go at the bottom of `<body>`, after the footer. Never in `<head>`.

---

## Image Handling

### Sources
- **Unsplash:** `https://images.unsplash.com/photo-{id}?w=400&h=500&fit=crop&crop=face&q=80`
- **Unsplash Plus:** `https://plus.unsplash.com/premium_photo-{id}?w=400&h=500&fit=crop&crop=face&q=80`
- **Webflow CDN:** `https://cdn.prod.website-files.com/{path}` (SVGs, WebPs)

### URL parameters (Unsplash)
| Param | Usage |
|-------|-------|
| `w=400` | Width in pixels |
| `h=500` | Height in pixels |
| `fit=crop` | Crop to fit dimensions |
| `crop=face` | Center on detected face |
| `crop=top` | Crop from top |
| `q=80` | Quality (80% is good balance) |

### Required attributes on every `<img>`
```html
<img src="..."
     alt="Descriptive text"
     width="400"
     height="500"
     loading="lazy"
     decoding="async"
     class="w-full h-full object-cover">
```

| Attribute | Rule |
|-----------|------|
| `alt` | Required. 10-125 chars. Use `alt=""` only for decorative images. |
| `width` / `height` | Required on every image. Prevents CLS. |
| `loading="lazy"` | All below-fold images. Omit for hero/above-fold. |
| `decoding="async"` | All lazy-loaded images. |
| `object-cover` | Standard fit for all photos. |

### Placeholder images
When source images aren't available, use: `https://placehold.co/400x500/141414/C5A55A?text=Placeholder`

---

## SEO Setup

### Per-page meta tags
Every page must include these in `<head>`:
```html
<meta name="description" content="...">          <!-- 120-155 chars -->
<meta name="robots" content="index, follow">
<link rel="canonical" href="https://www.savoya.com/page-slug">
<meta property="og:type" content="website">
<meta property="og:title" content="Page Title - Savoya">
<meta property="og:description" content="...">
<meta property="og:url" content="https://www.savoya.com/page-slug">
<meta property="og:image" content="...">          <!-- 1200x630 PNG/JPG -->
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta property="og:site_name" content="Savoya">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Page Title - Savoya">
<meta name="twitter:description" content="...">
<meta name="twitter:image" content="...">
```

### Structured data
Add JSON-LD in `<head>` before `</head>`:
- **Every page:** Organization schema
- **FAQ pages:** FAQPage schema
- **Service pages:** Service or LocalBusiness schema

### Sitemap
Add every new page to `sitemap.xml`:
```xml
<url>
  <loc>https://www.savoya.com/page-slug</loc>
  <lastmod>YYYY-MM-DD</lastmod>
  <changefreq>monthly</changefreq>
  <priority>0.8</priority>
</url>
```

---

## Accessibility Requirements

### Mandatory on every page
- `<html lang="en">`
- `<main id="main-content">` wrapping content (not header/footer)
- Skip nav link as first element in `<body>`
- Single `<h1>`, sequential heading order
- `aria-label` on every form input, icon-only button, and icon-only link
- `aria-expanded` on every toggle button
- `@media (prefers-reduced-motion: reduce)` in CSS
- All interactive elements minimum 48x48px touch target

### Testing
- Chrome DevTools Lighthouse (Accessibility audit)
- Tab through page with keyboard to verify focus order
- Test with `prefers-reduced-motion` enabled

---

## Mobile-First Development Rules

### Mandatory for every page
1. **Never use fixed pixel text sizes without a mobile override.** Every `text-[Xpx]` above 22px must have a smaller mobile default.
2. **Use `min-h-screen` instead of `h-screen`** for hero/full-height sections. `h-screen` clips content on short mobile screens.
3. **Every page must have a mobile hamburger menu.** Desktop nav is `hidden md:flex`; mobile menu is toggled via JS.
4. **Test at 375px width before committing.** Use Playwright `browser_resize({ width: 375, height: 812 })` or Chrome DevTools.
5. **Touch targets minimum 48x48px.** All buttons and links must be at least 48px in the smallest dimension.
6. **Avoid horizontal scroll.** No element should cause horizontal overflow on 375px screens.
7. **Images must have responsive heights.** Never use fixed `h-[Xpx]` without a mobile variant (e.g., `h-[180px] md:h-[220px]`).
8. **Grid gaps must be responsive.** Use `gap-6 md:gap-12` instead of just `gap-12`.
9. **Decorative elements (ambient orbs, etc.) must be responsive.** Use `w-[300px] h-[300px] md:w-[600px] md:h-[600px]` or `hidden md:block`.

### Mobile navigation pattern
```html
<!-- Hamburger button (mobile only) -->
<button id="mobile-menu-btn" class="md:hidden flex items-center justify-center w-10 h-10 text-white/60"
        aria-label="Open menu" onclick="document.getElementById('mobile-menu').classList.toggle('hidden')">
  <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M4 6h16M4 12h16M4 18h16"/>
  </svg>
</button>

<!-- Desktop nav (hidden on mobile) -->
<nav class="hidden md:flex items-center gap-8">...</nav>

<!-- Mobile menu panel (hidden by default, toggled by button) -->
<div id="mobile-menu" class="hidden md:hidden bg-lux-bg/95 backdrop-blur-xl border-t border-white/6 px-6 py-4 space-y-3">
  <a href="..." class="block py-2 text-white/60">Link</a>
  ...
</div>
```

---

## Hosting & Deployment

### GitHub Pages
- **Branch:** Pages served from the branch configured in repo Settings > Pages
- **Deploy:** Push to the serving branch; GitHub auto-deploys within ~60 seconds
- **URL:** `https://abhargava6.github.io/Savoya-website-playground/`
- **Custom domain:** Configure via CNAME file if needed

### Testing locally
```bash
# Start a local server (no install required)
npx -y http-server . -p 8123 -c-1

# Open in browser
open http://localhost:8123/index.html
```

### Performance testing
```bash
# Run PageSpeed Insights (requires deployed URL)
# Visit: https://pagespeed.web.dev/
# Test both mobile and desktop
# Target: Performance 90+, SEO 100, Accessibility 90+, Best Practices 100
```

---

## Branch Strategy

| Branch | Purpose |
|--------|---------|
| `main` | Stable baseline |
| `new-design` | Dark luxury redesign (gold theme) |
| `seo-optimized` | SEO, performance, accessibility improvements |
| `website-links` | Design system docs + new page development |

### Workflow
1. Create feature branch from latest work branch
2. Make changes, test locally with `http-server`
3. Screenshot and verify with Playwright or browser DevTools
4. Commit with descriptive message
5. Push and optionally open PR
6. After merge, re-run PageSpeed Insights to verify no regression

---

## Quality Checklist (before pushing any page)

### Visual
- [ ] Matches design reference or DESIGN-SYSTEM.md patterns
- [ ] Tested at 1440px, 1024px, and 375px widths
- [ ] All images load correctly
- [ ] Animations smooth (no jank)
- [ ] Dark theme consistent (no white flashes)

### Code
- [ ] Valid HTML5 (no unclosed tags)
- [ ] Tailwind config matches project config
- [ ] Custom CSS classes copied from index.html
- [ ] JS at bottom of body, not in head
- [ ] No console errors

### Performance
- [ ] All images have `loading="lazy"` (below fold) and `width`/`height`
- [ ] Font preconnect in place
- [ ] No unnecessary external resources

### SEO
- [ ] Title, description, OG, Twitter Card meta tags
- [ ] Canonical URL set
- [ ] Structured data if applicable
- [ ] Page added to sitemap.xml

### Accessibility
- [ ] Skip nav link present
- [ ] `<main>` landmark with id
- [ ] All inputs labeled
- [ ] All headings sequential
- [ ] Contrast meets WCAG AA
- [ ] Reduced motion respected
