# B2Fleet — prototype

A clickable prototype of the B2Fleet B2B car rental marketplace **and** the admin content studio behind it.
Public site and studio are the same file. Nothing is hard-coded into the pages: every section, price, image
and label is rendered from one content document held in memory.

**This is a prototype to think with, not a product.** See _Not built_ below before wiring anything real to it.

---

## Files

| File | What it is |
|---|---|
| `index.html` | The app. Loads images from `assets/`. Use this one in the repo. |
| `standalone.html` | Identical app with every image inlined as base64. Open it straight off a phone with no server. |
| `admin/index.html` | Makes `/admin` a real URL. Redirects into the studio route. |
| `assets/` | Starter imagery, laid out as the asset-pack README describes. |
| `docs/website-design-reference.png` | The visual reference the public homepage was built against. |

## Deploy

Static. No build step, no dependencies, no package manager.

**GitHub web UI → Netlify**

1. Create a new repo.
2. **Add file → Upload files**, then drag the whole unzipped `b2fleet-prototype` folder in. GitHub keeps the folder paths.
3. Commit.
4. In Netlify: **Add new site → Import an existing project**, pick the repo.
5. Build command: leave empty. Publish directory: `/` (or the folder name if you nested it).

Routing is hash-based (`#/`, `#/admin`, `#/admin/vehicles`), so no redirect rules are needed and deep links survive a refresh.

## Using it

**Public routes** — every header, footer and card link goes somewhere real:

`#/` home · `#/cars` search results with working filters and sorting · `#/cars/:id` vehicle detail
`#/destinations/:slug` · `#/suppliers` · `#/bookings` · `#/help` · `#/page/:slug` for footer pages

**Studio** — the button bottom right of the public site, or `/admin` directly, or `#/admin/vehicles`,
`#/admin/media`, `#/admin/branding`.

Sign-in is fake — any email and password work. Pick a role to see permissions change:

| Role | Sees |
|---|---|
| Owner / Administrator | Everything |
| Content editor | Content and marketplace, no users or settings |
| Booking manager | Dashboard and bookings only |
| Read-only viewer | Everything, all editing disabled |

**Sidebar** — group headers collapse on click. Hover a header for arrows, or drag a whole group, to reorder
them so what you use most sits at the top. **Hide unbuilt** strips the SOON sections out of the list.
Order and collapse state are in memory only and reset on refresh.

**Worth trying**

- *Homepage* — edit the hero headline. The preview redraws live; the public site stays on the last published version until you press **Publish**. That draft/published split is the spine of the whole thing.
- *Homepage* — drag a section, or hide one with the eye, then publish.
- *Media library* — drop in any badly proportioned photo. It is added at its real dimensions; dragging the focal marker re-crops it live across 16:7, 4:3, 16:10 and 1:1 simultaneously. The original is never altered.
- *Branding* — change the primary colour. Everything retints, studio included, because it is all one set of CSS variables.
- *Vehicles* — edit or duplicate one. Duplicates land as drafts.

## How the content model works

Two copies of the same document:

- `S.published` — what the public site renders.
- `S.draft` — what the studio edits.

**Publish** copies draft over published. One render function, `renderPublic(site)`, paints both the live site and the studio preview pane, so the preview cannot drift from reality.

Images are referenced by key, never copied. A focal point is stored as normalised `{x, y}` in the range 0–1 and applied as `object-position`, so one marker governs every crop and every responsive size.

Vehicle photography uses `object-fit: contain` on a pale surface — wheels and edges are never cropped. Supplier logos use `contain` inside identical 3:1 boxes. Destination and hero photography uses `cover` with the focal point.

## Not built

Seven studio sections are marked **SOON** and open an honest empty state rather than a fake form:
vehicle categories, locations, insurance plans, extras, offers, customer agencies, testimonials.

Also absent, deliberately:

- **Persistence.** Refresh and it resets to seed content. There is no database and no localStorage.
- **Real authentication.** Roles are cosmetic. In the built product every permission is enforced again server-side — hiding a button is not security.
- **Server-side image derivatives.** Cropping here is CSS on the original. Production needs generated WebP/AVIF variants at several widths.
- **Transactional email, PDF vouchers, payments, revision history, scheduled publishing.**

## Assets

The imagery is AI-generated starter material. The vehicle renders carry recognisable BMW, Volvo and Fiat design
cues, and `assets/branding/logo-concept-v2.png` is a raster concept, not an approved identity. Both need
resolving before anything commercial. The logo currently drawn in the app is a coded placeholder so the layout
is truthful — it is not the final mark.

Dates are day-first (DD/MM/YYYY) everywhere they are shown, including exports and file names.
