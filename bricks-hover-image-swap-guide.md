# Product Thumbnail Hover-Swap in Bricks Builder

*Showing the second product image on hover, on WooCommerce shop and category pages*

---

Short answer: this is not a native Bricks toggle, but you don't need a plugin either. The cleanest method is to build it inside your Bricks products loop using two stacked images and one small CSS rule. **Option A** is recommended. **Option B** is a plugin route if you'd rather not touch CSS at all.

Assumption: this is WooCommerce, and "the second image" means the first image in each product's **Product Gallery** (the images sitting next to the Featured image on the product edit screen).

## Prerequisite — do this first

This is the #1 reason a hover swap appears not to work: every product needs at least two images.

1. Go to **Products** and edit a product.
2. In the right sidebar, set **Product image** — this is the main thumbnail.
3. Just below it, in **Product gallery**, add at least one image. The first gallery image is what will show on hover.
4. Click **Update**. Repeat for your other products.

Products with no gallery image simply won't swap — nothing breaks.

## Option A — Bricks only, no plugin (recommended)

### 1. Create the loop card

1. **Bricks → Templates → Add New.** Set Template type to **Archive** and name it e.g. "Shop / Category Archive".
2. Open **Template Settings → Conditions** and add the condition **Archive Type → Product archives**. This covers both the shop page and all product category pages.
3. Click **Edit with Bricks**.
4. Add a **Container** — this is your product card.
5. With the container selected, switch on the **Query loop** toggle (the loop icon next to the element name). Set **Query → Type: WooCommerce Products** (some Bricks versions label this **Post type → Products**). Leave everything else at default so the loop inherits whichever category is being viewed.

### 2. Add the two images

6. Inside the loop container, add a plain **Div** and name it "Image wrap".
7. Inside that Div, add an **Image** element. For its source, click the dynamic data (lightning) icon and choose `{featured_image}`.
8. Add a **second Image** element directly beneath it, inside the same Div. For its source use the dynamic data tag:

```
{woo_product_gallery_images:1}
```

In the dynamic data dropdown, look for "Product gallery images". The `:1` means "just the first gallery image". If your Bricks version doesn't accept `:1`, simply pick the gallery tag as-is — the CSS below still works.

9. Add the rest of the card as normal: Product Title (`{post_title}`), Price (`{woo_product_price}`), and link the container to `{post_url}`.

### 3. Add the hover CSS (one paste, one time)

10. Select the **"Image wrap" Div** → **Style → CSS → Custom CSS** and paste:

```css
%root% {
  position: relative;
  overflow: hidden;
}
%root% img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
  aspect-ratio: 3 / 4; /* use 1 / 1 for square thumbnails */
}
%root% img:last-child {
  position: absolute;
  inset: 0;
  opacity: 0;
  transition: opacity .35s ease;
}
%root%:hover img:last-child {
  opacity: 1;
}
```

`%root%` is Bricks' shortcut for "this element", so the rule can never leak into other parts of the site. Save the template, then view a product category page and hover.

### 4. Mobile behaviour

Touch devices have no hover state, so they simply show the main image — which is what you want. Nothing further to configure.

## Option B — Plugin route (zero CSS)

Use this only if you're keeping WooCommerce's default product grid rather than a Bricks query loop.

1. Go to **Plugins → Add New** and search for **"WooCommerce Product Image Flipper"** — free, very lightweight, does exactly this one job.
2. **Install**, then **Activate**.
3. Make sure each product has a gallery image (see Prerequisite above).
4. Done — there's no settings screen to configure.

Caveat: it hooks into WooCommerce's own loop markup. If you later rebuild the archive with a Bricks query loop, the plugin stops applying and you'd move to Option A anyway. That's why Option A is the more future-proof choice.

## Which one to pick

- **Building the category page in Bricks** (very likely, since you're using Bricks): choose **Option A**. No extra plugin, nothing to maintain, full control over sizing and animation, and it survives theme and plugin changes.
- **Using stock WooCommerce templates** and want it done in two minutes: choose **Option B**.

## Optional tweaks for Option A

- **Slide instead of fade:** in the `:last-child` rule replace `opacity` with `transform: translateX(100%)`, and on hover use `transform: translateX(0)`.
- **Subtle zoom on hover:** add `%root%:hover img { transform: scale(1.04); }` and put `transition: transform .4s ease;` on `%root% img`.
- **Consistent grid:** keep the `aspect-ratio` value identical across all cards so rows never jump.

---

**Troubleshooting:** if the second image never appears, check that (a) the product actually has a gallery image, (b) the second Image element is the *last* child inside the Image wrap Div, and (c) you pasted the CSS on the **Div**, not on one of the Image elements.
