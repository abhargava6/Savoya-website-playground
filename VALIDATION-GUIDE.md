# Validation Guide

How to verify new pages during and after development. This guide is for the AI assistant (Claude) to follow when building and reviewing pages.

---

## Validation Workflow

```
Plan → Build → Validate Design System → Validate vs Reference → Fix → Re-validate → Done
```

Every new page goes through three validation phases:
1. **Pre-build:** Define evaluation criteria in the build plan
2. **During build:** Screenshot-compare loop against design system and reference
3. **Post-build:** Final audit across all dimensions

---

## Phase 1: Pre-Build (Evaluation Criteria)

When creating a build plan for a new page, include an **Evaluation Criteria** section that defines what "done" looks like. Use this template:

### Evaluation Criteria Template

```markdown
## Evaluation Criteria

### Design System Compliance
- [ ] Uses correct color tokens (lux-bg, lux-surface, gold, etc.)
- [ ] Typography matches scale (see DESIGN-SYSTEM.md section 1)
- [ ] Section spacing follows py-20/py-24 pattern
- [ ] Components use established patterns (buttons, cards, forms)
- [ ] Animations use standard easing and durations
- [ ] Responsive breakpoints follow sm/md/lg patterns

### Reference Page Fidelity
- Reference URL: https://www.savoya.com/[page-slug]
- [ ] Layout structure matches reference
- [ ] Section order matches reference
- [ ] Content hierarchy matches reference
- [ ] Spacing within ~3px of reference
- [ ] Font sizes match reference
- [ ] Color usage matches reference (adapted to dark theme)
- [ ] Image placement and sizing matches reference

### Technical Requirements
- [ ] SEO meta tags complete (per TECH-STACK.md)
- [ ] All images lazy loaded with dimensions
- [ ] Accessibility: labels, headings, ARIA, contrast
- [ ] JS patterns reused from index.html (no new libraries)
- [ ] Page added to sitemap.xml

### Performance Targets
- [ ] Desktop Performance: 90+
- [ ] Mobile Performance: 80+
- [ ] SEO: 100
- [ ] Accessibility: 90+
- [ ] Best Practices: 100
```

---

## Phase 2: During Build (Screenshot-Compare Loop)

### Step 1: Take reference screenshot

Before building, capture the Savoya.com page we're recreating:

```javascript
// Navigate to the reference page
browser_navigate({ url: "https://www.savoya.com/page-slug" });

// Full page screenshot
browser_take_screenshot({ fullPage: true, filename: "reference-fullpage.png" });

// Section-by-section screenshots (scroll to each section)
browser_evaluate({ function: "() => document.querySelector('section-selector').scrollIntoView()" });
browser_take_screenshot({ filename: "reference-section-hero.png" });
// Repeat for each section
```

### Step 2: Build and screenshot our page

After implementing a section or group of sections:

```bash
# Serve locally
npx -y http-server . -p 8123 -c-1 --silent &

# Open in Playwright
browser_navigate({ url: "http://localhost:8123/page-name.html" });
browser_take_screenshot({ fullPage: true, filename: "build-round1-fullpage.png" });
```

### Step 3: Compare and document mismatches

For each comparison round, check these dimensions and document findings:

| Dimension | What to Check | Tolerance |
|-----------|--------------|-----------|
| **Spacing** | Padding, margins, gaps between elements | ~3px |
| **Typography** | Font size, weight, line-height, letter-spacing | Exact match |
| **Colors** | Background, text, border, accent colors | Exact hex |
| **Alignment** | Horizontal/vertical alignment, centering | ~2px |
| **Sizing** | Element widths, heights, image dimensions | ~3px |
| **Border** | Radius, width, color, style | Exact match |
| **Shadows** | Box-shadow values, opacity | Close match |
| **Responsive** | Layout changes at breakpoints | Structural match |

Document mismatches like this:
```
Round 1 findings:
- Hero heading is 48px, reference shows 56px → fix to text-[56px]
- Gap between cards is 16px, should be 24px → fix to gap-6
- Footer border missing gold tint → add border-gold/10
```

### Step 4: Fix and re-screenshot

Fix all mismatches, then repeat Steps 2-3. **Minimum 2 rounds.** Stop when no visible differences remain.

---

## Phase 3: Post-Build (Final Audit)

Run all validations after the page is complete.

### A. Design System Compliance Check

Use Playwright to inspect the page programmatically:

```javascript
// Check all design tokens are correct
browser_evaluate({ function: `() => {
  const results = {};
  
  // Color check: no unexpected colors
  const body = getComputedStyle(document.body);
  results.bodyBg = body.backgroundColor;
  
  // Typography check: correct fonts loaded
  results.bodyFont = body.fontFamily;
  results.h1Count = document.querySelectorAll('h1').length;
  results.h2Count = document.querySelectorAll('h2').length;
  
  // Component check: standard classes used
  results.revealElements = document.querySelectorAll('.reveal').length;
  results.cardPremium = document.querySelectorAll('.card-premium').length;
  results.btnGold = document.querySelectorAll('.btn-gold').length;
  
  // Spacing check: all sections use max-w-page
  results.sectionsWithMaxW = document.querySelectorAll('.max-w-page').length;
  results.totalSections = document.querySelectorAll('section').length;
  
  return results;
}` });
```

### B. Technical Validation

```javascript
browser_evaluate({ function: `() => {
  const audit = {};
  
  // SEO
  audit.title = document.title;
  audit.metaDesc = document.querySelector('meta[name="description"]')?.content || 'MISSING';
  audit.ogTitle = document.querySelector('meta[property="og:title"]')?.content || 'MISSING';
  audit.canonical = document.querySelector('link[rel="canonical"]')?.href || 'MISSING';
  audit.robots = document.querySelector('meta[name="robots"]')?.content || 'MISSING';
  audit.jsonLd = document.querySelectorAll('script[type="application/ld+json"]').length;
  
  // Images
  audit.totalImages = document.querySelectorAll('img').length;
  audit.lazyImages = document.querySelectorAll('img[loading="lazy"]').length;
  audit.imagesWithDimensions = document.querySelectorAll('img[width][height]').length;
  audit.imagesWithAlt = document.querySelectorAll('img[alt]').length;
  audit.imagesMissingAlt = document.querySelectorAll('img:not([alt])').length;
  
  // Accessibility
  audit.htmlLang = document.documentElement.lang || 'MISSING';
  audit.mainElement = !!document.querySelector('main');
  audit.mainId = document.querySelector('main')?.id || 'MISSING';
  audit.skipNav = !!document.querySelector('a[href="#main-content"]');
  audit.h1Count = document.querySelectorAll('h1').length;
  audit.ariaLabels = document.querySelectorAll('[aria-label]').length;
  audit.ariaExpanded = document.querySelectorAll('[aria-expanded]').length;
  audit.formInputs = document.querySelectorAll('input, textarea, select').length;
  audit.labeledInputs = document.querySelectorAll('input[aria-label], textarea[aria-label], select[aria-label], input[id]').length;
  
  // Performance
  audit.preconnect = document.querySelectorAll('link[rel="preconnect"]').length;
  audit.preload = document.querySelectorAll('link[rel="preload"]').length;
  audit.fontDisplaySwap = document.querySelector('link[href*="display=swap"]') ? 'YES' : 'NO';
  
  return audit;
}` });
```

**Expected results:**
```
SEO: title present, metaDesc present, ogTitle present, canonical present, jsonLd >= 1
Images: lazyImages = totalImages - (above-fold count), imagesWithDimensions = totalImages, imagesMissingAlt = 0
Accessibility: htmlLang = "en", mainElement = true, skipNav = true, h1Count = 1, labeledInputs = formInputs
Performance: preconnect >= 2, fontDisplaySwap = "YES"
```

### C. Reference Comparison (Playwright)

Side-by-side visual comparison between our page and the Savoya reference:

```javascript
// Screenshot our page at key viewports
browser_navigate({ url: "http://localhost:8123/page-name.html" });
browser_resize({ width: 1440, height: 900 });
browser_take_screenshot({ fullPage: true, filename: "final-desktop.png" });

browser_resize({ width: 375, height: 812 });
browser_take_screenshot({ fullPage: true, filename: "final-mobile.png" });

// Screenshot reference at same viewports
browser_navigate({ url: "https://www.savoya.com/page-slug" });
browser_resize({ width: 1440, height: 900 });
browser_take_screenshot({ fullPage: true, filename: "reference-desktop.png" });

browser_resize({ width: 375, height: 812 });
browser_take_screenshot({ fullPage: true, filename: "reference-mobile.png" });

// Compare visually and document any remaining differences
```

### D. Contrast Validation

Check all text colors against their backgrounds:

| Text Class | Opacity | Contrast on #0a0a0a | WCAG AA |
|-----------|---------|---------------------|---------|
| `text-white` | 100% | 19.3:1 | Pass |
| `text-white/90` | 90% | 17.1:1 | Pass |
| `text-white/80` | 80% | 14.8:1 | Pass |
| `text-white/70` | 70% | 12.5:1 | Pass |
| `text-white/60` | 60% | 6.7:1 | Pass |
| `text-white/50` | 50% | 5.3:1 | Pass (normal text) |
| `text-white/40` | 40% | 4.0:1 | Pass large text only |
| `text-gold` | #C5A55A | 7.8:1 | Pass AAA |

**Rule:** Never use `text-white/40` or lower for normal-sized body text (below 18px). Use `text-white/50` minimum.

### E. Performance Score Check

After deploying to GitHub Pages, run PageSpeed Insights:

```
URL: https://pagespeed.web.dev/analysis?url=https://abhargava6.github.io/Savoya-website-playground/page-name.html

Target scores:
  Desktop Performance: 90+
  Mobile Performance: 80+
  SEO: 100
  Accessibility: 90+
  Best Practices: 100

If any score is below target, check the failing audits and fix before marking the page as complete.
```

---

## Mobile Validation Phase

### Mobile Screenshot Testing

Test at three viewports to cover the most common devices:

```javascript
// iPhone SE (smallest common mobile)
browser_resize({ width: 375, height: 667 });
browser_take_screenshot({ fullPage: true, filename: "mobile-375.png" });

// iPhone 14 (standard mobile)
browser_resize({ width: 390, height: 844 });
browser_take_screenshot({ fullPage: true, filename: "mobile-390.png" });

// iPhone 14 Pro Max (large mobile)
browser_resize({ width: 428, height: 926 });
browser_take_screenshot({ fullPage: true, filename: "mobile-428.png" });
```

### Mobile Checklist

For each mobile screenshot, verify:

| Check | What to Look For |
|-------|-----------------|
| **No horizontal scroll** | Page fits within viewport width, no elements overflow right |
| **Hero text readable** | H1 is ~28px on mobile, not overlapping other elements |
| **Booking form visible** | Full form visible, not clipped or hidden behind other content |
| **Images not cropped** | Card images show faces/content properly at mobile heights |
| **Navigation** | Hamburger menu visible, desktop nav hidden |
| **Touch targets** | All buttons and links at least 48x48px |
| **Form fields** | Full width, easy to tap, labels visible |
| **Text sizes** | No text larger than 32px on mobile (except decorative stat numbers) |
| **Grid layouts** | Properly stacked to single column on mobile |
| **Spacing** | No excessive gaps pushing content off screen |

### Mobile Automated Check

```javascript
browser_evaluate({ function: `() => {
  const results = {};
  
  // Check for horizontal overflow
  results.hasHorizontalScroll = document.body.scrollWidth > window.innerWidth;
  
  // Check hamburger menu exists
  results.hasMobileMenu = !!document.getElementById('mobile-menu');
  results.hasMobileMenuBtn = !!document.getElementById('mobile-menu-btn');
  
  // Check no text is too large
  const allText = document.querySelectorAll('h1, h2, h3, p, span, a, button');
  const oversizedText = [];
  allText.forEach(el => {
    const size = parseFloat(getComputedStyle(el).fontSize);
    if (size > 36) oversizedText.push(el.tagName + ': ' + size + 'px');
  });
  results.oversizedText = oversizedText;
  
  // Check touch targets
  const smallTargets = [];
  document.querySelectorAll('a, button, input, textarea, select').forEach(el => {
    const rect = el.getBoundingClientRect();
    if (rect.width > 0 && rect.height > 0 && (rect.width < 44 || rect.height < 44)) {
      smallTargets.push(el.tagName + ': ' + Math.round(rect.width) + 'x' + Math.round(rect.height));
    }
  });
  results.smallTouchTargets = smallTargets;
  
  return results;
}` });
```

---

## Validation Checklist Summary

Use this as a final sign-off checklist:

### Design System
- [ ] Colors match palette (no raw hex outside tokens)
- [ ] Fonts are Playfair Display + Inter only
- [ ] Type scale matches DESIGN-SYSTEM.md (including mobile overrides)
- [ ] Spacing uses established patterns
- [ ] Components reuse existing patterns
- [ ] Animations use standard easing curve
- [ ] Responsive at 375px, 768px, 1440px

### Mobile Responsiveness
- [ ] No horizontal scroll at 375px
- [ ] Hero text readable (28-32px on mobile)
- [ ] Images not cropped or clipped
- [ ] Hamburger menu present and functional
- [ ] All touch targets >= 48x48px
- [ ] Form fields full-width and usable on mobile
- [ ] Grid layouts stack to single column
- [ ] No oversized text (nothing > 32px on mobile except stat numbers)
- [ ] Ambient orbs/decorative elements responsive or hidden on mobile

### Reference Fidelity
- [ ] Screenshot compared at 1440px desktop
- [ ] Screenshot compared at 375px mobile
- [ ] Layout structure matches
- [ ] Spacing within tolerance (~3px)
- [ ] Minimum 2 comparison rounds completed
- [ ] No visible differences remaining

### Technical
- [ ] HTML validates (no unclosed tags, no errors)
- [ ] SEO meta tags complete (title, desc, OG, Twitter, canonical)
- [ ] Structured data present and valid
- [ ] All images: alt, width, height, lazy loading
- [ ] All form inputs labeled (aria-label or label element)
- [ ] Skip nav link present
- [ ] `<main>` landmark present
- [ ] Single H1, sequential headings
- [ ] WCAG AA contrast on all text
- [ ] Reduced motion supported
- [ ] No console errors
- [ ] Page added to sitemap.xml

### Performance (post-deploy)
- [ ] PageSpeed desktop: 90+
- [ ] PageSpeed mobile: 80+
- [ ] SEO: 100
- [ ] Accessibility: 90+
- [ ] Best Practices: 100
