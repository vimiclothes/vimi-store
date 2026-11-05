# ViMi Official — Project Master Guide & Current State Snapshot

> **Brand:** ViMi Official (Monochromatic Heavyweight Streetwear & 450 GSM Drop-Shoulder Hoodies)  
> **Architecture:** Shopify Online Store 2.0 Theme + Standalone Web/Pages Previews  
> **Status:** All core systems, animations, grids, navigation, and collections tested & pushed to GitHub.

---

## 1. Repository & Deployment Infrastructure

### Git Remotes
| Remote Name | Repository URL | Purpose |
| :--- | :--- | :--- |
| **`origin`** *(Primary)* | `https://github.com/vimiclothes/vimi-store.git` | **Live Shopify Theme Repository** (connected via Shopify Admin > Themes > Connect from GitHub) |
| **`storefront`** | `https://github.com/blazecodeprakhar/vimi-storefront.git` | Standalone client preview & GitHub Pages archive |

* **Active Branch:** `main` (tracked to `origin/main`).
* **Authentication:** Handled through GitHub collaborator access under account `blazecodeprakhar`.
* **Packaged Theme Archive:** `vimi-theme.zip` (5.74 MB, self-contained and ready for direct zip upload).

---

## 2. Codebase Directory Structure

```
Shopify Liquid/
├── assets/
│   ├── base.css                  # Core global stylesheet (monochrome luxury, 4-col grid, compact cards, shimmer)
│   ├── theme.js                  # Master JS orchestrator (Buying window, mobile menu, hero sliders, image loader)
│   ├── cart-drawer.js            # Slide-out AJAX cart drawer controller
│   ├── vimi-logo.png             # Brand logo badge
│   ├── vimi-logo-transparent.png # Transparent brand watermark
│   ├── vimi-hoodie-01.jpg .. 27  # 27 Web-optimized progressive 450 GSM hoodie photos
│   └── hoodies/                  # Relative asset mirror for static HTML preview fallbacks
├── config/
│   ├── settings_schema.json      # Shopify Visual Theme Customizer schema
│   └── settings_data.json        # Active theme configuration & default typography/colors
├── layout/
│   └── theme.liquid              # Master Shopify HTML layout, viewport isolation, and critical CSS overrides
├── locales/
│   └── en.default.json           # Store translations & typography labels
├── sections/
│   ├── hero-banner.liquid        # Desktop showcase & dedicated ViMi mobile hero
│   ├── featured-collection.liquid# Homepage 4-column archive (all 27 hoodies)
│   ├── main-collection.liquid    # Dedicated collection/products page archive with dark sort toolbar
│   ├── header.liquid             # Fixed glass header & bulletproof mobile navigation drawer
│   ├── footer.liquid             # Streetwear branding, legal links, newsletter, payment badges
│   ├── hoodie-anatomy.liquid     # 450 GSM luxury fabric breakdown section
│   └── main-product.liquid       # Product page layout with variant picker
├── snippets/
│   ├── product-card.liquid       # Universal hoodie card component with blur-up reveal & quick buy
│   ├── buying-window.liquid      # Dedicated rapid Quick Buy size/checkout modal
│   ├── cart-drawer.liquid        # Slide-over cart drawer markup
│   └── icon-*.liquid             # Clean monochrome inline SVG icons
├── templates/                    # OS 2.0 JSON route templates (index, product, collection, cart, 404)
├── index.html                    # Root web entrypoint for browser preview
├── preview-store.html            # Standalone visual testing suite
├── PROJECT_GUIDE.md              # This repository documentation file
└── .gitignore                    # Excludes zip archives and temp files; tracks all Shopify Liquid folders
```

---

## 3. Log of Completed Work & Resolved Issues

### Fix 1: Phantom 4-Column PC Grid Bug
* **Issue:** Row 1 only displayed 2 product cards on PC, leaving empty black gaps between cards.
* **Root Cause:** Malformed attribute quotes (`data-product-image="vimi-hoodie-XX.jpg'>`) caused Chromium to hoist empty phantom anchor tags outside their parent, populating alternating CSS grid tracks.
* **Resolution:** Cleaned quotes across all 27 cards in `preview-store.html`, `index.html`, and `sections/featured-collection.liquid`. Enforced strict 4-column CSS grid rules (`repeat(4, 1fr) !important; gap: 20px;`). Cards now sit side-by-side ($X = 28\text{px},\ 369\text{px},\ 710\text{px},\ 1051\text{px}$).

### Fix 2: Dedicated ViMi Mobile Hero
* **Implementation:** Integrated user's exact mobile hero specification (`.vimi-m-hero`, `#vimiMobileHero`) for phones ($\le 767\text{px}$):
  * Top animated announcement marquee ticker.
  * Hot pill badges and sideways swipeable category chips.
  * Bold minimal typography (*"Crazzzy silent luxury. 450 GSM. Engineered in India."*).
  * Product card with scroll-snap swipe carousel, active dot pill indicators, and live slide metadata.
  * Sticky thumb-zone `.m-cta` bar (`⚡ QUICK BUY` and `ADD TO BAG`).
  * Desktop hero isolated with `.vimi-desktop-hero` (`display: none !important` on mobile).

### Fix 3: Mobile Carousel Image Loading Error
* **Issue:** Mobile hero card displayed a solid grey `#1b1b1b` placeholder box.
* **Resolution:** Replaced CSS background placeholder `hoodie-1.jpg` with dedicated `<img class="m-slide-img">` elements configured with real assets (`vimi-hoodie-21.jpg`, `02`, `03`, `04`, `06`) and automatic `onerror` fallback handlers.

### Fix 4: High-Performance Image Optimization & Compression
* **Implementation:** Processed all 27 hoodie catalog images and logos through a Python Lanczos optimization pipeline:
  * Dimension capping at max-width 750px (preserving 2x Retina clarity for 360px cards while eliminating bloated 1200–1600px dimensions).
  * Progressive JPEG encoding with Huffman table optimization at quality 75.
  * **Result:** Total asset size dropped from 12.58 MB to 8.51 MB (**-32.3% bandwidth saved**), shrinking the theme zip from 8.84 MB to **5.74 MB**.

### Fix 5: Ultra-Luxury Image Loading & Metallic Shimmer System
* **Implementation:**
  * **Shimmer Skeleton:** Animated dark brushed metallic gradient (`vimiLuxuryShimmer`) with a pulsating `ViMi ARCHIVE` luxury watermark.
  * **Cinematic Blur-Up:** Images decode at `opacity: 0; filter: blur(14px) brightness(0.85); transform: scale(1.05);` and smoothly reveal with Apple cubic-bezier(0.16, 1, 0.3, 1) to `opacity: 1; filter: blur(0); transform: scale(1);`.
  * **Zero-Lag Cache Decoder:** Inline `vimiImgLoaded(this)` script in `<head>` ensures instant rendering for cached images with zero flicker.

### Fix 6: Multi-Remote Git Synchronization
* **Implementation:**
  * Re-pointed `origin` to `https://github.com/vimiclothes/vimi-store.git`.
  * Preserved the previous repo as `storefront`.
  * Cleaned `.gitignore` so that all Shopify Liquid architecture folders (`layout/`, `sections/`, `snippets/`, `templates/`, `config/`, `locales/`) are tracked.
  * Pushed initial theme codebase cleanly to branch `main`.

### Fix 7: Compact Product Card Proportions ("Boxes Are Too Long")
* **Issue:** Product cards on PC stretched vertically to 599px in height, taking up almost the entire screen.
* **Resolution:**
  * Transitioned image container from tall 4:5 portrait ratio to **1:1 square ratio** (`aspect-ratio: 1 / 1 !important; object-fit: cover;`).
  * Constrained titles to 2 lines max with `-webkit-line-clamp: 2`.
  * Tightened info padding and size chip heights.
  * **Result:** Card height decreased from **599px down to 486.8px** (cutting **over 112px** of excessive height per card).

### Fix 8: Mobile Navigation Menu Overlap Bug
* **Issue:** Tapping the mobile hamburger button opened a menu that was transparent, allowing the hero text (*"CRAZZZY SILENT LUXURY..."*) to bleed through the navigation links in an unreadable collision.
* **Resolution:**
  * Enforced 100% solid pitch black background (`background: #000000 !important;`) on `.mobile-nav-content`.
  * Elevated stacking hierarchy: `.mobile-nav-drawer` (`z-index: 9999999 !important`), `.mobile-nav-overlay` (`z-index: 9999998 !important`), `.mobile-nav-content` (`z-index: 9999999 !important`).
  * Enlarged close button (`#MobileNavClose`) with touch-friendly circular hit target.

### Fix 9: Hero Carousel Stage Arrow Buttons Not Working
* **Issue:** Clicking `<` and `>` arrows on the desktop hero card did nothing.
* **Root Cause:** A duplicate pair of `#HeroStagePrev` and `#HeroStageNext` buttons was inadvertently nested inside inactive Slide 5 in `sections/hero-banner.liquid`. JavaScript bound to the first matching element in the DOM (the hidden button inside Slide 5), leaving the visible buttons without listeners.
* **Resolution:**
  * Removed duplicate markup from `sections/hero-banner.liquid`.
  * Updated `assets/theme.js` to bind all matching arrow buttons using `document.querySelectorAll()`.
  * Added touch event listeners (`touchend`) alongside `click` for immediate responsiveness on mobile/tablet.

### Fix 10: "All Images Are Same" on Collection Page & Toolbar UI
* **Issue:** Navigating to the Products/Collections page showed every card having the identical Skeleton Spine image (`vimi-hoodie-01.jpg`), displayed an unstyled raw white `<select>` box overlapping cards, and output `"0 Items"`.
* **Resolution:**
  * Populated `sections/main-collection.liquid` with **all 27 unique hoodies** (`vimi-hoodie-01.jpg` to `27`), each with its distinct image, title, pricing, and badge.
  * Styled `.collection-toolbar` and `.sort-select` in luxury matte black (`#141414`) with a custom SVG chevron arrow and a 30px bottom margin.
  * Updated counter logic to display `"27 Exclusive Grails"` instead of `"0 Items"`.

---

## 4. Current State & Configuration Checklist

| Component | Status | Verification Detail |
| :--- | :--- | :--- |
| **PC Product Grid** | Verified | 4 cards per row, strictly aligned, 486.8px compact height |
| **Mobile Hero** | Verified | Marquee, chips, swipe carousel, sticky CTA bar active |
| **Mobile Nav Drawer** | Verified | Solid black background (`rgb(0, 0, 0)`), zero bleed-through, z-index 9999999 |
| **Hero Slider Arrows** | Verified | `<` and `>` cycle seamlessly through slides 1 to 5 with wrap-around |
| **Collection Page** | Verified | 27 unique hoodies with distinct images, custom dark sort dropdown |
| **Image Loading** | Verified | Shimmer skeleton + progressive blur-up active across all cards & modals |
| **Git Remote** | Clean | Pushed up to commit `c6ccfa0` on `vimiclothes/vimi-store` |
| **Theme Zip Package** | Ready | `vimi-theme.zip` (5.74 MB) updated with all latest assets & templates |

---

## 5. How to Continue Work from This Point

1. **Working with Code:**
   * Edit files directly in the repository root (`assets/`, `sections/`, `snippets/`, etc.).
   * After making changes, rebuild the theme zip:
     ```powershell
     python -c "import os, zipfile; [os.remove('vimi-theme.zip') if os.path.exists('vimi-theme.zip') else None]; z=zipfile.ZipFile('vimi-theme.zip','w',zipfile.ZIP_DEFLATED); [z.write(os.path.join(r,f),os.path.relpath(os.path.join(r,f),'.')) for d in ['assets','config','layout','locales','sections','snippets','templates'] for r,_,fs in os.walk(d) for f in fs]; print('Zip updated!')"
     ```
2. **Pushing Updates to Shopify:**
   * Simply commit and push:
     ```bash
     git add .
     git commit -m "your update message"
     git push origin main
     ```
   * Since Shopify is connected to `vimiclothes/vimi-store`, any push to `origin main` automatically updates your live store.
3. **If You Need to Update the GitHub Pages Preview:**
   * Push to the secondary remote:
     ```bash
     git push storefront main
     ```
---

## 6. Classic Customer Account System (ViMi Syndicate)

### Architecture & Capabilities:
* **Native Shopify Form Tags**:
  - Sign In: {% form 'customer_login' %}
  - Registration: {% form 'create_customer' %}
  - Password Reset: {% form 'recover_customer_password' %}
  - Address Management: {% form 'customer_address', ... %}
  - Password Change: {% form 'reset_customer_password' %}
  - Invitation Activation: {% form 'activate_customer_password' %}
* **Templates**:
  - 	emplates/customers/account.liquid: VIP member dashboard, order archive table, address summary card, and syndicate perks.
  - 	emplates/customers/login.liquid: Standalone auth portal for direct /account/login visits with forgot-password inline toggle.
  - 	emplates/customers/register.liquid: Member onboarding page for /account/register.
  - 	emplates/customers/addresses.liquid: Full CRUD address book with modals to add, edit, and delete shipping addresses.
  - 	emplates/customers/order.liquid: Line-item order breakdown receipt with billing/shipping address and live fulfillment status.
  - 	emplates/customers/reset_password.liquid: Encrypted password reset confirmation page.
  - 	emplates/customers/activate_account.liquid: Account invitation setup page.
* **Header Integration**:
  - Dynamic detection: If customer is logged in, displays Hi, [Name] linking directly to /account. If logged out, displays modal trigger.
* **Dedicated Stylesheet**:
  - ssets/customer.css: Luxury dark-monochrome styling for all account views, status badges, forms, and modals.

