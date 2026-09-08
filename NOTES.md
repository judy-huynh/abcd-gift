# ABCD working notes

State of play for whoever picks this up next, human or agent. Read this before
touching anything. `BLOG-SYSTEM.md` holds the blog pipeline design.
Last updated 8 Sep 2026.

## Where things are

| | |
|---|---|
| Local repo | `~/Documents/Github/ABCD Shopify Theme` (never move it back to Downloads, macOS TCC blocks reads there) |
| GitHub | `judy-huynh/abcd-gift`, branch `judy-huynh`. There is no `main` |
| Store | `abcdgiftshop.myshopify.com` / abcd.gift |
| Live theme | `eyezystarter`, id `147505447164` |
| Preview theme | `Size guide`, id `161113080060`. Mirror of live, used for phone checks |

`git push` fails 403 unless you run `gh auth switch -u judy-huynh` first. The
active `gh` account defaults to `rebuildbydesign`, which cannot write here.

## Working rhythm

1. `CI=1 shopify theme pull --store abcdgiftshop.myshopify.com --theme 147505447164`
2. `git diff` to see whatever Judy changed in the admin code editor
3. Commit that before writing anything new
4. Push to the preview theme, Judy checks on her phone, then live with `--allow-live`
5. Before any live push, pull live into a scratch dir and `diff -rq` against the
   working copy. Confirms nobody edited in the admin mid-session

Preview cookies stick. Once a browser or phone visits a `?preview_theme_id=` URL
it keeps getting that theme on later plain visits. Pin both sides explicitly when
comparing, and send `?preview_theme_id=147505447164` to get back to live.

## Shipped and live

**Cart currency.** Cart total states the currency (`35 USD`, `35 CAD`). Prices are
bare everywhere else, which is fine while browsing and too vague right before
paying.

**Size guide.** `snippets/size-chart.liquid`, called from one line in
`sections/product-main.liquid`. Live on 11 of 14 products.

- Four charts, garment measured flat, stored in cm. Inches convert in JS at 2.54.
  Default view is inches
- A `chart-<key>` product tag picks the chart, else the handle lists in the
  snippet. No match means no link at all. Deliberate: a wrong chart causes
  returns, a missing one causes a question
- Diagrams are held in `data-src` until the modal opens. Callout numbers run in
  the same order as `row_labels`, so the measure list numbers itself. **Reorder
  the rows and the numbers stop matching the picture**
- Diagram width is per chart. `diagram_max` and `diagram_flex` default to 170px /
  34%; the hoodie sets 250px / 52% because its drawing is landscape where the
  others are portrait, and at 170px the callout numbers were unreadable

**Hoodie diagram is ours as of 8 Sep 2026** (`assets/abcd-size-hoodie.png`, commit
`5fd60a3`). It replaced Tapstitch's own JPEG, which reverse image searched straight
back to them. It also settled an open question: the drawing is a set-in shoulder
pullover, not the zip-up their page shows, so "seam to seam" is now a measurement
someone can actually take.

`assets/size-guide-hoodie.jpg` is orphaned by this and can be deleted.

Still uncovered, all need Judy:

- **`size-guide-tshirt.jpg` and `size-guide-crop.png` are still Tapstitch's own
  artwork.** These are the remaining reverse-image matches
- **Warehouse Cargo Pant.** No measurements at all, and a pant needs waist, rise
  and inseam rows the tops do not have
- **"2025" T-Shirt.** Unconfirmed blank. Its sizes read `2XL` where the other tees
  read `XXL`, which suggests a different blank
- **Crop tee Length reads `38,38,39,40`.** S and M identical. Could be real, could
  be a copy error. Needs checking against a garment
- Blox Logo Cap needs no chart. It correctly shows no link

## Blog: built, not launched

Goal is a curated streetwear and culture blog for SEO and brand exposure, in the
spirit of The Hundreds. It lives at `/blogs/news`, is **not** in the main menu,
and is already in `sitemap_blogs_1.xml`.

### Decisions Judy made

| Question | Answer |
|---|---|
| Autonomy | Agent drafts, **Judy approves and publishes**. Never auto-publish |
| Discoverability | Sitemap **plus one quiet footer link**. Not fully orphan |
| Content | Streetwear and fashion news, and culture around the brand |
| Layouts | **Grid** galleries, **Cards** shop block |
| Posting method | **Drive the logged-in Shopify admin in Chrome.** No API token |

### What is built and verified

`templates/article.liquid` and `templates/blog.liquid`. Verified on a real post,
not just in theory.

- **Galleries.** A post writes `<figure class="article-gallery">` with two or three
  `<img>`; add `--3` for three across, which falls back to two on narrow screens.
  Gallery images open the article lightbox
- **Shop this post.** Reads `shop:<handle>` article tags, looks each product up
  live so price and sold-out state are never stale. Renders nothing if no tags
  match. `shop:` tags are filtered out of the tag pills and the blog card label

### Why there is no API token

Shopify retired the store-admin custom app screen for this store. The Dev
Dashboard replaces it, and it only issues a Client ID and Secret. Turning those
into an Admin API token needs a hosted OAuth callback, which is a real project.
Hence the browser-driven approach above, which suits draft-for-approval anyway.
There is a stray unused app called `ABCD Blog` in the dev dashboard, safe to delete.

### The pipeline design

Agreed 30 Aug 2026, **not built**. Full design in `BLOG-SYSTEM.md`. Short version:
RSS in, scored, shortlisted, two approval gates, posted through her browser, with a
Search Console loop feeding back into scoring. Free replacement for the paid
MonitoRSS setup. Cross-feed corroboration is the only trend signal that genuinely
works; nothing predicts conversion before publishing. Hard cadence cap of about one
post a week, enforced in code, because volume is the demotion risk.

### Blocked on

- **Judy's voice.** No copy gets drafted until we have real samples. Instagram
  captions are the most likely source
- **Photography.** Every layout leans on images and we cannot use other brands'
  photos. Realistic sources are her own shots, licensed press images, or posts
  built from her own imagery. Grid survives on two photos per post
- **The footer link.** Agreed but not built. `sections/footer.liquid` is disabled;
  the visible footer is `sections/sticky-footer.liquid`, an image bar, so a text
  link needs thought

### Loose end

`TEMPLATE TEST — delete me` is sitting in Blog posts, Hidden. It was the render
test. Safe to delete.

## Open brief: making the size charts read as ours

Paused 8 Sep 2026 to talk through rather than decide alone. Not a task list.

**The question.** Someone screenshots our size guide and runs it through an AI, or
just eyeballs it. What makes it read as a supplier spec sheet, and what would make
it read as ABCD?

**What we know so far.**

The measurements themselves are the fingerprint and we cannot change them, because
changing them means misdescribing the garment and that causes returns. So the goal
is that it stops reading as a supplier chart at a glance, not that it becomes
untraceable. Worth being honest about that ceiling up front.

Everything below is a lever we have actually confirmed, not a plan:

| Lever | Note |
|---|---|
| Round cm to whole numbers | The tee shoulder row reads `54.7, 56.2, 57.7, 59.5, 61.3, 63.1`. Exact decimals are what a match keys on. Rounding is also more honest: we state 1 to 3 cm variance, so one decimal is false precision. Precision is currently inconsistent row to row, Length whole and Shoulder to one decimal, which is itself the tell that these were copied rather than measured |
| Drop the orange and the numbered discs | The visual signature, read before any number is. Our new hoodie diagram still has it |
| Replace the tee and crop diagrams | Still literally their files |
| Add a fit note per garment | No supplier chart has one. Needs Judy's words, not drafted copy |
| Reorder the rows | Theirs is Length, Shoulder, Chest, Sleeve. Chest is what people check first |
| Rewrite the note | "Garment measured flat, not body measurements" is generic supplier phrasing |
| Settle `2XL` vs `XXL` | Inconsistent between the tee and the hoodie inside one store |

**The more interesting version of the question**, and the reason this is paused
rather than actioned: every lever above is subtractive, removing things that look
like someone else. Nobody has yet asked what an ABCD size guide would have that no
supplier chart would. A fit note is the first inch of that. The reference pages
below are the visual thinking so far.


## Reference pages made along the way

- Size guide directions: https://claude.ai/code/artifact/c177563e-c015-45d4-bc34-b90f9f63abbd
- Blog layout mockups, galleries and shop block: https://claude.ai/code/artifact/873c462b-47fc-4857-ace2-74636fe99413
- The ABCD Desk, blog pipeline design: https://claude.ai/code/artifact/47e50d9f-8d6c-43a5-9428-6f84246ea421
- Measured Flat, four directions for a size-chart drawing that is ours: https://claude.ai/code/artifact/fecef45f-66ff-48e8-827a-a615545e1116

## Two SEO constraints that shaped all of the above

1. **Unlinked and SEO pull against each other.** The sitemap gets Google there, but
   a page with zero internal links is crawled slowly and inherits no authority.
   Hence the one footer link
2. **Auto-publishing AI posts is the real risk.** Google's scaled content abuse
   policy targets mass-produced content made mainly to rank, and a demotion hits
   the whole domain, product pages included. This is why the loop drafts rather
   than publishes

## Theme quirks worth knowing before editing

- Base is the third-party EYECEY `eyezystarter` theme, heavily customised. Despite
  the README, only `index`, `product`, `page`, `page.contact` and `password` are
  JSON templates. The rest are legacy `.liquid`
- Variant selection, add-to-cart and cart count JS live in `layout/theme.liquid`,
  not in the product section. `sections/product-main.liquid` has a second script
  reading the classes theme.liquid sets, ordered with `setTimeout(..., 0)`.
  Editing variant behaviour means touching both
- `sections/footer.liquid` is disabled in `settings_data.json`. The real footer is
  `sections/sticky-footer.liquid`, fixed at `z-index: 999`. Anything overlaying the
  page must clear that
- The colour swatch hex palette is duplicated in `product-main.liquid` and
  `featured-collection.liquid`. Change one and the other drifts
- `assets/main.js` is an empty 0-byte file. All JS is inline in sections

## Two pre-existing JS errors, both live, neither introduced by us

1. `sections/header.liquid` calls `burgerMenu.addEventListener`, but `.burger-menu`
   and `.header-menu` exist only in that file's CSS and JS, never in the markup.
   Throws on every page and kills the handlers below it
2. `layout/theme.liquid` emits `{{ product.variants | json }}`, which renders
   `null` off product pages, so `initializeSelections()` throws on the homepage,
   collection and cart. No visible symptom because `#CartCount` is server-rendered

## Testing traps

- `resize_window` does not change the viewport in the automation browser. To test
  mobile: `fetch()` the page, inject `<base href="https://abcd.gift/">` into
  `<head>`, set it as an iframe `srcdoc` at 390px
- `loading="lazy"` images never load in the automation browser because the tab is
  hidden and the observer never fires. Force `loading='eager'` before measuring, or
  you will chase a bug that is not there
- Hidden blog articles return the 404 page, so a post has to be Visible to be
  checked on the storefront
