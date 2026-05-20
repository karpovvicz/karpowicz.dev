# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal portfolio site for Kacper Karpowicz — a full-stack web developer. This is a production-ready, single-page static site designed for deployment on Vercel.

**Tech Stack:** Vanilla HTML/CSS/JavaScript (no build system, no dependencies)
**Deployment:** Vercel
**Domain:** www.kacperkarpowicz.dev

## Architecture

### Single-Page Design

The entire site is a monolithic `index.html` with:
- Inline CSS (3000+ lines) using custom properties for theming
- Inline vanilla JavaScript for scroll-based reveal animations
- No external dependencies except Google Fonts (Instrument Serif, Fraunces, JetBrains Mono)

### Design System

Custom properties defined in `:root` at index.html:39-49:
- `--paper`, `--paper-2`: background colors
- `--ink`, `--ink-2`: text colors
- `--muted`: secondary text
- `--rule`: border colors
- `--accent`, `--accent-2`: brand colors (#c9442a, #e8a37a)
- `--max`: 1320px max-width

Dark mode automatically switches these values via `@media (prefers-color-scheme: dark)` at index.html:388-402.

### Key Features

1. **Sticky Navigation** (index.html:83-116)
   - Backdrop blur effect
   - Underline hover animations
   - Hides on mobile (<720px)

2. **Scroll-Reveal Animations** (index.html:766-774)
   - IntersectionObserver-based
   - Elements with `.reveal` class fade in on scroll
   - Respects `prefers-reduced-motion` (index.html:365-374)

3. **Accessibility** (index.html:352-363)
   - Skip link to main content
   - Focus-visible states
   - ARIA attributes
   - Print stylesheet (index.html:377-385)

4. **SEO & Meta**
   - Structured JSON-LD data (index.html:406-434)
   - Open Graph tags (index.html:19-24)
   - Twitter Card tags (index.html:26-30)
   - sitemap.xml and robots.txt

5. **Security Headers**
   - Configured in vercel.json
   - X-Content-Type-Options, X-Frame-Options, X-XSS-Protection, Referrer-Policy, Permissions-Policy

## Project Structure

```
/
├── index.html              # Main (only) page - complete site
├── vercel.json            # Security headers config
├── manifest.json          # PWA manifest
├── sitemap.xml           # SEO sitemap
├── robots.txt            # Search engine directives
├── favicon.svg           # Vector icon
├── DEPLOYMENT_CHECKLIST.md  # Pre-deployment tasks
└── [missing assets:]
    ├── favicon.ico       # TODO: 16x16, 32x32, 48x48
    ├── apple-touch-icon.png  # TODO: 180x180
    └── og-image.jpg      # TODO: 1200x630 social preview
```

## Common Tasks

### Testing Locally

```bash
# Simple HTTP server (Python 3)
python3 -m http.server 8000

# Or use any static file server:
npx serve
```

Then visit http://localhost:8000

### Deploying to Vercel

```bash
# First deployment
vercel

# Production deployment
vercel --prod
```

Vercel auto-detects this as a static site. No build command needed.

### Making Style Changes

All CSS is inline in index.html starting at line 38. The stylesheet uses:
- Mobile-first breakpoints at 720px and 860px
- Clamp() for fluid typography
- Grid and flexbox layouts
- CSS custom properties for theming

**Important:** When editing CSS:
1. Maintain the custom property system for light/dark mode compatibility
2. Test both light and dark modes
3. Verify responsive breakpoints at 720px, 860px
4. Check `prefers-reduced-motion` doesn't break animations

### Adding New Projects

Featured projects are in the `.feat` section starting at index.html:510. Each project follows this structure:

```html
<article class="feat-row">
  <div class="feat-num">01</div>
  <h3 class="feat-name">Project Name <em>Emphasized</em></h3>
  <div>
    <p class="feat-desc">Description...</p>
    <div class="feat-stack">
      <span>Tech</span><span>Stack</span><span>Items</span>
    </div>
  </div>
  <div class="feat-year">2025</div>
</article>
```

Smaller projects in `.grid` section starting at index.html:592 use `.card` components.

### Updating Content

Key sections to edit:
- **Hero intro**: index.html:473-474 (main description)
- **About section**: index.html:485-500
- **Featured work**: index.html:510-583
- **Tech stack**: index.html:648-689
- **Experience**: index.html:699-735
- **Contact**: index.html:739-757

### SEO Updates

After content changes:
1. Update sitemap.xml lastmod dates
2. Verify structured data (JSON-LD) reflects current info at index.html:406-434
3. Update meta descriptions if hero changes

## Visual Design Language

### Typography
- Headings: Instrument Serif (serif, variable font-style for italics)
- Body: Fraunces (serif, variable font with optical sizing)
- UI/Labels: JetBrains Mono (monospace, uppercase + letter-spacing)

### Animation Patterns
- Hero title: Staggered rise animation (index.html:345-350)
- Sections: Fade-in on scroll via IntersectionObserver
- Buttons: Translate + box-shadow on hover
- Nav links: Expanding underline from left to right

### Color Usage
- `--accent` (#c9442a): Primary brand color for emphasis, italics in headings, hover states
- `--muted` (#7a7166): Labels, metadata, secondary information
- `--ink-2` (#3b352e): Body text
- `--ink` (#1a1714): Headings, primary text

## Critical Implementation Notes

### Do NOT Install Dependencies
This is intentionally dependency-free. Do not add:
- npm/package.json
- Build tools (webpack, vite, parcel)
- CSS preprocessors (sass, postcss)
- JS frameworks (React, Vue, etc.)

The site must remain a single HTML file deployable to any static host.

### Respect the Aesthetic
The design has a deliberate "paper and ink" metaphor with:
- Grain texture overlay (SVG noise filter at index.html:73)
- Serif typography
- Subtle animations
- High contrast in dark mode

Changes should maintain this aesthetic consistency.

### Performance Constraints
- Fonts are preloaded (index.html:35)
- No JavaScript dependencies
- Minimal DOM manipulation (only IntersectionObserver)
- All styles inlined (no render-blocking CSS)

Keep the site fast and dependency-free.

## Known Issues & TODOs

From DEPLOYMENT_CHECKLIST.md:

1. **Missing Assets**
   - favicon.ico (16x16, 32x32, 48x48)
   - apple-touch-icon.png (180x180)
   - og-image.jpg (1200x630) — required for social media previews

2. **Missing Project Links**
   - Most featured projects lack live links or GitHub repos
   - When adding links, wrap `.feat-row` in `<a>` tags

3. **Email Exposure**
   - Email address visible in HTML (potential spam target)
   - Consider obfuscation or contact form if spam becomes an issue

## Testing Checklist

Before deploying changes:
- [ ] Test in both light and dark modes
- [ ] Verify responsive behavior at 720px and 860px breakpoints
- [ ] Check all internal anchor links (#work, #stack, etc.)
- [ ] Test skip link with Tab key
- [ ] Enable "Reduce motion" in system settings and verify animations are disabled
- [ ] Test print view (Cmd/Ctrl + P)
- [ ] Validate HTML at validator.w3.org
- [ ] Check social preview at metatags.io

## Deployment Notes

**Vercel Configuration:**
- Security headers in vercel.json are critical — do not remove
- No build command needed
- No output directory config needed
- Automatic HTTPS and CDN

**Post-Deployment:**
- Submit sitemap to Google Search Console
- Test og-image.jpg displays correctly on Twitter, LinkedIn, Slack
- Verify custom domain DNS settings