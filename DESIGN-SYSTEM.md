# Savoya Design System

A comprehensive guide for building new pages that match the established visual language.

---

## 1. Foundation

### Color Palette

| Token | Value | Tailwind | Usage |
|-------|-------|----------|-------|
| Background | `#0a0a0a` | `bg-lux-bg` | Page background |
| Surface | `#141414` | `bg-lux-surface` | Cards, alternate sections |
| Elevated | `#1a1a1a` | `bg-lux-elevated` | Hover states, raised elements |
| Gold | `#C5A55A` | `text-gold` / `bg-gold` | CTAs, highlights, accents |
| Gold Hover | `#D4B96A` | `text-gold-hover` / `bg-gold-hover` | Button hover states |
| Gold Glow | `rgba(197,165,90,0.06-0.08)` | `.gold-glow` / `.ambient-orb` | Ambient effects |
| Primary Text | `#f5f5f5` | `text-white` | Headings, emphasis |
| Body Text | `rgba(255,255,255,0.5)` | `text-white/50` | Standard body copy |
| Secondary Text | `rgba(255,255,255,0.50)` | `text-white/50` | Labels, subtitles |
| Muted Text | `rgba(255,255,255,0.40)` | `text-white/40` | Hints, helper text (use sparingly, contrast borderline) |
| Border Subtle | `rgba(255,255,255,0.06)` | `border-white/6` | Dividers, card borders |
| Border Medium | `rgba(255,255,255,0.10)` | `border-white/10` | Active borders |
| Border Emphasis | `rgba(255,255,255,0.15)` | `border-white/15` | Hover borders |

**Contrast notes (WCAG AA on #0a0a0a):**
- `text-white/50` = ~5.3:1 -- passes AA for normal text
- `text-white/40` = ~4.0:1 -- passes AA for large text only (18px+ or 14px bold)
- Gold `#C5A55A` on `#0a0a0a` = 7.8:1 -- passes AAA

### Typography

**Fonts (Google Fonts, loaded with `display=swap`):**
- **Display:** `font-display` = Playfair Display (serif) -- headings, quotes
- **Body:** `font-body` = Inter (sans-serif) -- everything else

**Weights loaded:** Playfair 300, 400, 600 | Inter 400, 500

**Type Scale:**

| Element | Size | Weight | Line Height | Tracking | Tailwind |
|---------|------|--------|-------------|----------|----------|
| Hero H1 | 48/68px | 300 | 1.08 | -0.02em | `text-[48px] md:text-[68px] font-light leading-[1.08] tracking-[-0.02em]` |
| Section H2 | 44px | 300 | tight | -- | `font-display text-[44px] leading-tight font-light` |
| Contact H2 | 40px | 300 | tight | -- | `font-display text-[40px] leading-tight font-light` |
| Card Title H3 | 16-17px | 600 | -- | -- | `font-semibold text-[17px] text-white` |
| Tab Title H3 | 28px | 300 | -- | -- | `font-display text-[28px] text-white font-light` |
| Experience H3 | 22px | -- | -- | -- | `font-display text-[22px] text-white` |
| City Name H3 | 24px | -- | -- | -- | `font-display text-[24px] text-white` |
| Footer H4 | 11px | 500 | -- | 0.15em | `text-[11px] uppercase tracking-[0.15em] text-white/40 font-medium` |
| Body | 16px | 400 | relaxed | -- | `text-[16px] text-white/50` |
| Small Body | 14px | 400 | relaxed | -- | `text-[14px] text-white/50 leading-relaxed` |
| Caption | 13px | 400 | -- | -- | `text-[13px] text-white/50` |
| Label (uppercase) | 10-11px | 500 | -- | 0.2em | `text-[11px] text-white/50 uppercase tracking-[0.2em] font-medium` |
| Nav Link | 15px | 500 | -- | -- | `text-[15px] font-medium` |
| Button | 15px | 600 | -- | -- | `font-semibold text-[15px]` |
| Button Small | 13px | 600 | -- | -- | `font-semibold text-[13px]` |
| Stat Number | 52px | -- | none | -- | `text-[52px] leading-none .text-gold-gradient` |
| Quote | 24/30px | -- | relaxed | -- | `font-display text-[24px] md:text-[30px] leading-relaxed text-white/90` |

### Spacing

**Page container:** `max-w-page mx-auto px-6` (max 1170px, always 24px horizontal padding)

| Pattern | Value | Usage |
|---------|-------|-------|
| Section padding | `py-20 px-6` | Standard sections |
| Section padding (large) | `py-24 px-6` | Stats section |
| Section heading margin | `mb-14` to `mb-16` | Below heading block to content |
| Gold divider | `w-10 h-px bg-gold mx-auto mt-5` | Below section headings |
| Card padding | `p-5` | Standard card interior |
| Form card padding | `p-8 md:p-10` | Form containers |
| Form field padding | `p-3` | Input containers |
| Grid gap | `gap-5` | Cards grid |
| Grid gap (large) | `gap-12` | Stats grid |
| Header height | `h-[72px]` | Fixed header |
| Footer padding | `pt-14 pb-10 px-6` | Footer top section |

### Border Radius

| Size | Value | Usage |
|------|-------|-------|
| Small | `rounded-xl` | Form inputs, small cards |
| Medium | `rounded-2xl` | Cards, images, sections |
| Full | `rounded-full` | Buttons, avatars, badges |

---

## 2. Components

### Buttons

**Primary (Gold Solid):**
```html
<button class="btn-gold w-full font-semibold text-[15px] py-3.5 rounded-full hover:scale-[1.02] transition-transform duration-200">
  Label
</button>
```
- Gold background, dark text
- Full-width or auto-width
- 2% scale on hover

**Secondary (Gold Outline):**
```html
<button class="btn-gold-outline text-[13px] px-6 py-2.5 rounded-full font-semibold">
  Label
</button>
```
- Transparent bg, gold border and text
- Fills gold on hover

**Text Link:**
```html
<a href="#" class="text-white/60 hover:text-white transition-colors duration-200">
  Label
</a>
```

### Cards

**Standard Card (with hover lift):**
```html
<div class="bg-lux-surface rounded-2xl overflow-hidden border border-white/6 card-premium hover:border-white/15">
  <div class="relative h-[300px] overflow-hidden">
    <img src="..." alt="..." class="w-full h-full object-cover transition-transform duration-700 group-hover:scale-105"
         loading="lazy" decoding="async" width="400" height="500">
    <div class="absolute inset-0 bg-gradient-to-t from-lux-surface via-transparent to-transparent"></div>
    <!-- Optional badge -->
    <div class="absolute top-3 left-3 bg-gold text-lux-bg text-[10px] px-2.5 py-1 rounded-full font-bold uppercase">Badge</div>
  </div>
  <div class="p-5">
    <h3 class="font-semibold text-[17px] text-white">Title</h3>
    <p class="text-[13px] text-white/50 mt-1">Subtitle</p>
  </div>
</div>
```
- Lifts 6px on hover with shadow bloom
- Image zooms 5% on hover
- Gradient overlay fades image into card bg

**Glass Card (forms, overlays):**
```html
<div class="glass-dark rounded-2xl p-8 md:p-10">
  <!-- content -->
</div>
```
- Frosted glass: `rgba(255,255,255,0.03)` + `blur(24px)`
- Subtle border: `rgba(255,255,255,0.06)`

### Form Inputs

**Labeled Input (booking form style):**
```html
<div class="bg-white/5 rounded-xl p-3 border border-white/6">
  <label class="text-[10px] text-white/50 uppercase tracking-wider flex items-center gap-1 mb-1">
    <svg class="w-3 h-3 text-white/40">...</svg> Label
  </label>
  <input type="text" aria-label="Label" placeholder="Placeholder"
         class="w-full bg-transparent text-[14px] text-white outline-none placeholder:text-white/20">
</div>
```

**Underline Input (contact form style):**
```html
<input type="text" placeholder="Full Name" aria-label="Full Name"
       class="bg-transparent border-b border-white/10 text-white text-[14px] py-3 outline-none placeholder:text-white/20 focus:border-gold transition-colors">
```

### Section Heading Block

```html
<div class="text-center mb-14 reveal">
  <h2 class="font-display text-[44px] leading-tight font-light">Section Title</h2>
  <p class="text-white/50 text-[16px] mt-4">Subtitle description text.</p>
  <div class="w-10 h-px bg-gold mx-auto mt-5"></div>
</div>
```

### Badges / Pills

**Gold Badge:**
```html
<div class="bg-gold text-lux-bg text-[10px] px-2.5 py-1 rounded-full font-bold uppercase">Label</div>
```

**Inline Tag:**
```html
<span class="bg-gold text-lux-bg text-[10px] px-1.5 py-0.5 rounded font-bold uppercase ml-1">New</span>
```

### Image Overlays

**Gradient fade to background:**
```html
<div class="absolute inset-0 bg-gradient-to-t from-lux-surface via-transparent to-transparent"></div>
```

**Gradient fade to black (hero/city cards):**
```html
<div class="absolute inset-0 bg-gradient-to-t from-black/80 via-black/20 to-transparent"></div>
```

---

## 3. Layout Patterns

### Standard Section

```html
<section class="py-20 px-6">
  <div class="max-w-page mx-auto">
    <!-- heading block -->
    <!-- content grid -->
  </div>
</section>
```

**Alternating background:** Use `bg-lux-surface border-t border-b border-white/6` on every other section.

### Responsive Grids

| Layout | Mobile | SM | MD | LG | Tailwind |
|--------|--------|----|----|-----|----------|
| 4-col cards | 1 col | 2 cols | 2 cols | 4 cols | `grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-5` |
| 2-col form | 1 col | -- | 2 cols | -- | `grid-cols-1 md:grid-cols-2 gap-5` |
| Stats | 2 cols | -- | 4 cols | -- | `grid-cols-2 md:grid-cols-4 gap-12` |
| Footer links | 2 cols | 3 cols | 5 cols | -- | `grid-cols-2 sm:grid-cols-3 md:grid-cols-5 gap-6` |
| Content + Image | stacked | -- | side-by-side | -- | `flex flex-col md:flex-row gap-10` |

### Split Layout (text + form)

```html
<div class="flex flex-col md:flex-row gap-16">
  <div class="md:w-[40%]"><!-- text content --></div>
  <div class="md:w-[60%]"><!-- form/image --></div>
</div>
```

### Horizontal Scroll Carousel

```html
<div class="flex gap-5 overflow-x-auto pb-4 scroll-hide snap-x snap-mandatory">
  <div class="min-w-[300px] snap-start flex-shrink-0"><!-- item --></div>
  <!-- more items -->
</div>
```

Navigation arrows:
```html
<button aria-label="Previous" onclick="container.scrollBy({left:-320,behavior:'smooth'})"
        class="absolute left-0 top-1/2 -translate-y-1/2 w-10 h-10 rounded-full flex items-center justify-center text-white/60 hover:text-gold border border-white/10 hover:border-gold/30 bg-lux-bg/80 backdrop-blur-sm transition-all z-10">
```

---

## 4. Animation System

### Easing

All animations use the same Apple-inspired curve:
```
cubic-bezier(0.25, 0.46, 0.45, 0.94)
```

### Durations

| Speed | Duration | Usage |
|-------|----------|-------|
| Quick | 200ms | Link hovers, color transitions |
| Standard | 300ms | Border, opacity changes |
| Medium | 400ms | Card hover lift, tab fade |
| Slow | 500ms | Accordion expand |
| Reveal | 800ms | Scroll-in elements |

### Scroll Reveal

Add `reveal` class to any element that should animate in on scroll:
```html
<div class="reveal">Content appears on scroll</div>
```

For staggered children:
```html
<div class="stagger">
  <div class="reveal">First (0ms delay)</div>
  <div class="reveal">Second (60ms delay)</div>
  <div class="reveal">Third (120ms delay)</div>
  <div class="reveal">Fourth (180ms delay)</div>
</div>
```

### Hover Effects

**Card lift:** `.card-premium` -- translateY(-6px) + shadow bloom
**Image zoom:** `transition-transform duration-700 group-hover:scale-105`
**Button scale:** `hover:scale-[1.02] transition-transform duration-200`
**Border glow:** `hover:border-white/15` or `hover:border-gold/30`

### Reduced Motion

Always supported. The CSS includes:
```css
@media (prefers-reduced-motion: reduce) {
  * { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
}
```

---

## 5. Interactive Patterns

### Accordion (FAQ)

```html
<div class="border-b border-white/6">
  <button class="w-full flex items-center justify-between py-5 text-left group"
          onclick="toggleAccordion(this)" aria-expanded="false">
    <span class="font-medium text-[16px] text-white/80 group-hover:text-white transition-colors">Question</span>
    <svg class="accordion-chevron w-4 h-4 text-white/40 flex-shrink-0">...</svg>
  </button>
  <div class="accordion-content text-[14px] text-white/50 leading-relaxed">Answer text.</div>
</div>
```
- Only one open at a time
- Chevron rotates 180deg when open
- Gold chevron color when active

### Tabs

```html
<!-- Tab buttons -->
<div class="flex gap-1 border-b border-white/6 mb-10 overflow-x-auto scroll-hide">
  <button class="tab-btn active text-white border-gold px-5 py-3 text-[14px] font-medium border-b-2 whitespace-nowrap transition-all"
          data-tab="tab-name" onclick="switchTab(this)">Tab Label</button>
</div>

<!-- Tab panels -->
<div class="tab-panel active flex-col md:flex-row items-start gap-10" id="panel-tab-name">
  <div class="flex-1"><!-- text --></div>
  <div class="flex-1"><!-- image --></div>
</div>
```
- Gold underline on active tab
- Inactive: `text-white/40 border-transparent`

### Testimonial Carousel

```html
<div class="flex overflow-x-auto gap-6 snap-x snap-mandatory scroll-hide" id="testimonial-track">
  <div class="min-w-full snap-center px-4 md:px-20">
    <div class="font-display text-[80px] leading-none text-gold/15">&ldquo;</div>
    <p class="font-display text-[24px] md:text-[30px] leading-relaxed text-white/90 -mt-12">Quote text</p>
    <div class="mt-8 flex flex-col items-center gap-3">
      <img src="..." alt="Name" class="w-12 h-12 rounded-full object-cover border border-white/10">
      <div>
        <p class="font-medium text-[14px] text-white/70">Name</p>
        <p class="text-[13px] text-white/50 mt-0.5">Title, Company</p>
      </div>
    </div>
  </div>
</div>
<!-- Dot navigation -->
<div class="flex justify-center gap-2.5 mt-8" role="tablist" aria-label="Testimonial slides">
  <button class="w-2 h-2 rounded-full bg-gold" data-slide="0" role="tab" aria-label="Testimonial 1"></button>
  <button class="w-2 h-2 rounded-full bg-white/15" data-slide="1" role="tab" aria-label="Testimonial 2"></button>
</div>
```

### Stat Counter

```html
<div class="text-center">
  <div class="w-8 h-px bg-gold/40 mx-auto mb-4"></div>
  <div class="text-[52px] leading-none text-gold-gradient counter" data-target="63" data-suffix="%">0%</div>
  <div class="text-[11px] text-white/50 uppercase tracking-[0.2em] font-medium mt-3">Label</div>
</div>
```

---

## 6. SEO & Performance Checklist

Apply to every new page:

### Head Section (required)
- [ ] `<meta name="description" content="...">` (120-155 chars with CTA)
- [ ] `<meta name="robots" content="index, follow">`
- [ ] `<link rel="canonical" href="...">`
- [ ] Open Graph tags: og:type, og:title, og:description, og:url, og:image, og:image:width, og:image:height, og:site_name
- [ ] Twitter Card tags: twitter:card, twitter:title, twitter:description, twitter:image
- [ ] Font preconnect: `fonts.googleapis.com` + `fonts.gstatic.com`
- [ ] Preload critical above-fold image

### Structured Data
- [ ] Organization schema (JSON-LD) on homepage
- [ ] FAQPage schema if FAQ section exists
- [ ] Appropriate schema for page type (Article, LocalBusiness, etc.)

### Images
- [ ] `loading="lazy"` on all below-fold images
- [ ] `loading="eager"` on hero/above-fold images (or omit attribute)
- [ ] `decoding="async"` on lazy images
- [ ] Explicit `width` and `height` on every `<img>`
- [ ] Descriptive `alt` text (10-125 chars); `alt=""` for decorative images
- [ ] Use WebP format where possible

### Accessibility
- [ ] Single `<h1>` per page
- [ ] Sequential heading hierarchy (H1 > H2 > H3, no skips)
- [ ] `<main id="main-content">` wrapping page content
- [ ] Skip navigation link before header
- [ ] `aria-label` on all form inputs
- [ ] `aria-label` on icon-only buttons and links
- [ ] `aria-expanded` on toggle buttons
- [ ] `role="tablist"` / `role="tab"` on tab interfaces
- [ ] Touch targets minimum 48x48px
- [ ] All text meets WCAG AA contrast (4.5:1 normal, 3:1 large)
- [ ] `@media (prefers-reduced-motion: reduce)` disables animations

### Performance
- [ ] Tailwind via CDN for development; pre-built CSS for production
- [ ] No heavy JS frameworks -- vanilla JS only
- [ ] Font weights minimized (5 total across 2 families)
- [ ] `display=swap` on Google Fonts URL

---

## 7. Page Template

Starter template for any new page:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Page Title - Savoya</title>
  <meta name="description" content="Page description here (120-155 chars).">
  <meta name="robots" content="index, follow">
  <link rel="canonical" href="https://www.savoya.com/page-slug">

  <meta property="og:type" content="website">
  <meta property="og:title" content="Page Title - Savoya">
  <meta property="og:description" content="Description">
  <meta property="og:url" content="https://www.savoya.com/page-slug">
  <meta property="og:image" content="https://www.savoya.com/og-image.png">
  <meta property="og:image:width" content="1200">
  <meta property="og:image:height" content="630">
  <meta property="og:site_name" content="Savoya">

  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="Page Title - Savoya">
  <meta name="twitter:description" content="Description">
  <meta name="twitter:image" content="https://www.savoya.com/og-image.png">

  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@300;400;600&family=Inter:wght@400;500&display=swap" rel="stylesheet">

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

  <!-- Copy <style> block from index.html for animations, glass effects, etc. -->
</head>
<body class="min-h-screen">

  <a href="#main-content" class="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 focus:z-[100] focus:bg-gold focus:text-black focus:px-4 focus:py-2 focus:rounded-lg focus:font-semibold focus:text-sm">Skip to main content</a>

  <!-- HEADER (copy from index.html) -->

  <main id="main-content">

    <!-- HERO -->
    <section class="relative h-screen flex items-center overflow-hidden">
      <!-- hero content -->
    </section>

    <!-- CONTENT SECTIONS -->
    <section class="py-20 px-6">
      <div class="max-w-page mx-auto">
        <div class="text-center mb-14 reveal">
          <h2 class="font-display text-[44px] leading-tight font-light">Section Title</h2>
          <p class="text-white/50 text-[16px] mt-4">Description</p>
          <div class="w-10 h-px bg-gold mx-auto mt-5"></div>
        </div>
        <!-- section content -->
      </div>
    </section>

  </main>

  <!-- FOOTER (copy from index.html) -->

  <!-- SCRIPTS (copy scroll reveal, header glass, counter JS from index.html) -->

</body>
</html>
```

---

## 8. Mobile Responsive Rules

### Mobile-First Typography Scale

Never use a fixed text size without a mobile override. Use this responsive scale:

| Element | Mobile (default) | SM (640px+) | MD (768px+) | LG (1024px+) | Tailwind |
|---------|-----------------|-------------|-------------|---------------|----------|
| Hero H1 | 28px | 36px | 68px | -- | `text-[28px] sm:text-[36px] md:text-[68px]` |
| Section H2 | 28px | -- | 44px | -- | `text-[28px] md:text-[44px]` |
| CTA H2 | 26px | -- | 40px | -- | `text-[26px] md:text-[40px]` |
| Tab Title H3 | 22px | -- | 28px | -- | `text-[22px] md:text-[28px]` |
| Quote Text | 18px | -- | 24px | 30px | `text-[18px] md:text-[24px] lg:text-[30px]` |
| Quote Mark | 48px | -- | 80px | -- | `text-[48px] md:text-[80px]` |
| Stat Number | 32px | -- | 52px | -- | `text-[32px] md:text-[52px]` |
| Decorative Number | 32px | -- | 48px | -- | `text-[32px] md:text-[48px]` |
| Body, Nav, Button | No change needed | -- | -- | -- | Same across breakpoints |

### Mobile Spacing Rules

| Pattern | Mobile | Desktop | Tailwind |
|---------|--------|---------|----------|
| Hero gap | 24px | 48px | `gap-6 md:gap-12` |
| Stats grid gap | 24px | 48px | `gap-6 md:gap-12` |
| Section padding | py-20 px-6 | py-20 px-6 | Same (already works) |
| Hero top padding | 96px | 72px | `pt-24 md:pt-[72px]` |

### Mobile Image Heights

| Component | Mobile | Desktop | Tailwind |
|-----------|--------|---------|----------|
| Service card image | 180px | 220px | `h-[180px] md:h-[220px]` |
| Chauffeur card image | 250px | 300px | `h-[250px] md:h-[300px]` |
| City carousel card | 260px min-w | 300px min-w | `min-w-[260px] md:min-w-[300px]` |

### Mobile Layout Rules

- Use `min-h-screen` instead of `h-screen` for hero sections (allows content to overflow on short screens)
- Ambient orbs: always add mobile size `w-[300px] h-[300px] md:w-[600px] md:h-[600px]`
- Every page must have a mobile hamburger menu (visible below `md:` breakpoint)
- `flex-col md:flex-row` for all split layouts
- Booking form: `w-full md:w-[320px]` (full width on mobile)

### Mobile Navigation

Every page must include:
1. A hamburger button visible on mobile (`md:hidden`)
2. A slide-down mobile menu panel with all nav links
3. The desktop nav hidden on mobile (`hidden md:flex`)

---

## 9. File Reference

| File | Purpose |
|------|---------|
| `index.html` | Homepage -- all components, animations, and patterns |
| `robots.txt` | Crawler rules (allows GPTBot, ClaudeBot, PerplexityBot) |
| `sitemap.xml` | Site map for search engines |
| `performance-comparison.html` | Lighthouse comparison report |
| `DESIGN-SYSTEM.md` | This file |
