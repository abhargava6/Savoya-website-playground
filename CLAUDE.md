# Savoya Website Project

## Creating a New Page

When the user asks to create a new page and provides a Savoya.com URL (e.g. `https://www.savoya.com/about`):

### 1. Plan
- Read `DESIGN-SYSTEM.md` for visual patterns, color tokens, typography, components
- Read `TECH-STACK.md` for technical setup, JS patterns, SEO requirements
- Read `VALIDATION-GUIDE.md` for evaluation criteria template
- Screenshot the Savoya.com reference page (full page + key sections) using Playwright
- Create a build plan that includes an **Evaluation Criteria** section (use the template from VALIDATION-GUIDE.md)

### 2. Build
- Copy the page template from `DESIGN-SYSTEM.md` section 7 as a starting point
- Reuse the header and footer from `index.html`
- Follow `TECH-STACK.md` for Tailwind config, font loading, JS patterns, image handling
- Use components from `DESIGN-SYSTEM.md` (buttons, cards, forms, section layouts)
- Adapt the Savoya.com content to our dark luxury theme (gold accent, dark backgrounds)

### 3. Validate (screenshot-compare loop)
- Follow `VALIDATION-GUIDE.md` Phase 2: screenshot our page, compare against reference
- Check spacing (~3px tolerance), typography, colors, alignment, responsive layout
- Document mismatches with specific measurements
- Fix and re-screenshot — **minimum 2 comparison rounds**
- Stop only when no visible differences remain

### 3.5. Mobile Validation (MANDATORY)
- Screenshot the page at **375px width** (iPhone SE) using Playwright `browser_resize({ width: 375, height: 812 })`
- Check for: text overflow, image clipping, form fields fully visible, no horizontal scroll
- Verify mobile hamburger menu works (opens/closes)
- Verify all touch targets are at least 48x48px
- Check text sizes follow mobile scale from `DESIGN-SYSTEM.md` section 8
- Fix any issues and re-screenshot at 375px

### 4. Final Audit
- Follow `VALIDATION-GUIDE.md` Phase 3: run automated checks via Playwright
- Verify design system compliance (tokens, fonts, components)
- Verify SEO (meta tags, structured data, sitemap entry)
- Verify accessibility (labels, headings, contrast, skip nav)
- Verify performance (lazy loading, image dimensions, font preconnect)
- Compare side-by-side at **1440px desktop AND 375px mobile**
- Verify no horizontal scroll on mobile

### 5. Deploy
- Add the page URL to `sitemap.xml`
- Commit and push
- After GitHub Pages deploys, run PageSpeed Insights
- Target: Performance 90+, SEO 100, Accessibility 90+, Best Practices 100

## Reference Documents

| Document | Purpose |
|----------|---------|
| `DESIGN-SYSTEM.md` | Colors, typography, spacing, components, animations, page template |
| `TECH-STACK.md` | Tailwind setup, JS patterns, image handling, SEO, hosting |
| `VALIDATION-GUIDE.md` | Evaluation criteria, screenshot-compare loop, automated audits |

## Workflow (from reference image)

When the user provides a reference image (screenshot) instead of a URL:

1. **Generate** a single `.html` file using Tailwind CSS (via CDN). Include all content inline.
2. **Screenshot** the rendered page using Playwright.
3. **Compare** against the reference image. Check for mismatches in spacing, fonts, colors, alignment, borders, shadows, responsive behavior, and image sizing.
4. **Fix** every mismatch found.
5. **Re-screenshot** and compare again.
6. **Repeat** steps 3-5 until within ~2-3px of the reference everywhere.

Do NOT stop after one pass. Always do at least 2 comparison rounds.

## Technical Defaults

- Use Tailwind CSS via CDN (`<script src="https://cdn.tailwindcss.com"></script>`)
- Use placeholder images from `https://placehold.co/` when source images aren't provided
- Mobile-first responsive design
- Single `.html` file per page

## Rules

- Create a todo list before executing tasks
- Do not add features, sections, or content not present in the reference
- Match the reference exactly — do not "improve" the design (unless user asks)
- If the user provides CSS classes or style tokens, use them verbatim
- Keep code clean but don't over-abstract — inline Tailwind classes are fine
- When comparing screenshots, be specific (e.g., "heading is 32px but reference shows 24px")
