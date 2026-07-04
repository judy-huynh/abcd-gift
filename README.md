# ABCD GIFT SHOP — Shopify Theme

Custom Shopify theme for [ABCD GIFT SHOP](https://abcd.gift), a streetwear lifestyle brand rooted in electronic music and club culture.

## About the brand

ABCD GIFT SHOP is a streetwear label whose visual identity draws from children's ABC blocks — bold primary colors, block letters, playful typography — remixed for adult club culture. Product drops center on tees, hoodies, cargo pants, and caps inspired by house, techno, and warehouse party aesthetics.

The theme is designed to reflect this: minimal, bold, playful without being loud. The homepage leads with product, not marketing copy.

## Key features

- **Sticky header + fixed footer** — the ABCD block logo stays visible top and bottom while products scroll between them.
- **Dynamic auto-fit typography** — product titles resize to always fit on one line at any viewport width, on both product listings and detail pages.
- **Colored variant swatches** — color options render as filled color pills on product pages and as small swatches above product names on the homepage, matching the actual product colorway.
- **Variant image switching** — clicking a color instantly swaps the product image to that colorway, including for sold-out variants so users can browse the full lineup.
- **Sold-out friendly** — sold-out items stay clickable for image browsing; SOLD OUT label is bold and uppercase across listings.
- **Inline newsletter and restock forms** — email input and submit button sit side-by-side, no stacked layouts.
- **Instagram icon in the header nav** — direct social link with hover state.
- **Whole-number pricing** — no decimals on any customer-facing surface (via Shopify currency settings).
- **Responsive nav** — header links stay on one line at every viewport width via progressive font scaling.
- **Homepage-only sticky footer image block** — configurable via Shopify's theme customizer, ready for promo/drop messages.

## Structure

Standard Shopify OS 2.0 layout:

```
assets/       CSS, JS, images
blocks/       Reusable content blocks (footer image, etc.)
config/       Theme settings schema and saved data
layout/       theme.liquid, password.liquid
locales/      Translations
sections/     Header, footer, product-main, featured-collection, newsletter, etc.
snippets/     Shared partials
templates/    Homepage (index.json), product, collection, cart, blog, etc.
```

## Local development

Uses [Shopify CLI](https://shopify.dev/docs/themes/tools/cli):

```bash
# Pull latest from the live theme
shopify theme pull --store abcdgiftshop.myshopify.com

# Preview locally
shopify theme dev --store abcdgiftshop.myshopify.com

# Push to an unpublished theme for review
shopify theme push --unpublished --store abcdgiftshop.myshopify.com

# Push directly to the live theme
shopify theme push --store abcdgiftshop.myshopify.com --theme eyezystarter
```

## Contact

Judy Huynh
- judy.h@nyu.edu
- [judyhuynh.ca](https://judyhuynh.ca)
