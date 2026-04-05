# City Carousel Marquee Effect

## Prompt

> Replace the existing "Global Coverage" city carousel section in `index.html` with an auto-scrolling marquee effect.

## Tech Stack Constraints

- Single HTML file with inline CSS and JS — no React, no npm, no build tools
- Tailwind CSS via CDN (`cdn.tailwindcss.com`) with custom config
- Vanilla JavaScript only — no libraries
- Dark luxury theme: background `#0a0a0a`, surface `#141414`, accent gold `#C5A55A`
- Fonts: Playfair Display (headings), Inter (body)

## Effect to Implement

1. An infinite auto-scrolling horizontal marquee of city cards that loops seamlessly
2. Cards scroll continuously left at a smooth pace using CSS `@keyframes marquee` with `translateX`
3. The card set is duplicated in the DOM so the loop appears infinite
4. **Pause on hover** — marquee stops when user hovers over any card
5. **Edge fade** — left and right edges have gradient overlays that fade cards into the dark background (`from-[#0a0a0a] to-transparent`)
6. **Grayscale to color** — city images are `grayscale` by default and transition to full color on hover (`grayscale transition-all duration-500 hover:grayscale-0`)
7. Each card has: city photo, gradient overlay at bottom, city name (Playfair Display), and subtitle (country/region)

## City Data

Use these exact Unsplash image URLs already in the codebase:

| City | Location | Unsplash Photo ID |
|------|----------|-------------------|
| San Francisco | California, USA | `photo-1501594907352-04cda38ebc29` |
| New York City | New York, USA | `photo-1496442226666-8d4d0e62e6e9` |
| Paris | France | `photo-1502602898657-3e91760cbb34` |
| London | United Kingdom | `photo-1513635269975-59663e0ac1ad` |
| Dubai | United Arab Emirates | `photo-1512453979798-5ea266f8880c` |
| Miami | Florida, USA | `photo-1533106497176-45ae19e68ba2` |
| Los Angeles | California, USA | `photo-1534190760961-74e8c1c5c3da` |

## CSS to Add

Add to the inline `<style>` block:

```css
@keyframes marquee {
  from { transform: translateX(0); }
  to { transform: translateX(-50%); }
}
.marquee-track {
  display: flex;
  gap: 20px;
  animation: marquee 30s linear infinite;
  width: max-content;
}
.marquee-track:hover { animation-play-state: paused; }
```

## HTML Structure

```html
<section class="py-20 px-6 bg-lux-surface border-t border-b border-white/6">
  <div class="max-w-page mx-auto">
    <!-- Section heading with reveal animation -->
  </div>
  <div class="relative mt-12 overflow-hidden">
    <!-- Left fade gradient -->
    <div class="pointer-events-none absolute top-0 left-0 z-10 h-full w-24 md:w-32 bg-gradient-to-r from-lux-surface to-transparent"></div>
    <!-- Right fade gradient -->
    <div class="pointer-events-none absolute top-0 right-0 z-10 h-full w-24 md:w-32 bg-gradient-to-l from-lux-surface to-transparent"></div>
    <!-- Marquee track (cards duplicated for seamless loop) -->
    <div class="marquee-track">
      <!-- 7 city cards + 7 duplicates = 14 total -->
    </div>
  </div>
</section>
```

## Card Structure (each city)

```html
<div class="min-w-[280px] md:min-w-[300px] rounded-2xl overflow-hidden relative group cursor-pointer flex-shrink-0">
  <img src="https://images.unsplash.com/photo-{id}?w=600&h=400&fit=crop&q=80"
       alt="City Name"
       class="w-full h-[320px] object-cover grayscale transition-all duration-500 group-hover:grayscale-0 group-hover:scale-105"
       loading="lazy" decoding="async" width="600" height="400">
  <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-black/20 to-transparent"></div>
  <div class="absolute bottom-0 left-0 p-6">
    <h3 class="font-display text-[24px] text-white">City Name</h3>
    <p class="text-white/50 text-[13px] mt-1">Country</p>
  </div>
</div>
```

## Design Rules

- Remove the existing scroll-snap carousel and its prev/next arrow buttons
- Remove the carousel JS (`scrollBy` handlers) — the marquee is CSS-only
- Keep the section heading ("Global Coverage", subtitle, gold divider) with `reveal` animation
- The marquee should be full-width (break out of `max-w-page` container)
- Respect `prefers-reduced-motion`: stop animation for users who prefer reduced motion
- All images must have `loading="lazy"`, `decoding="async"`, `width`, `height`, and `alt`
