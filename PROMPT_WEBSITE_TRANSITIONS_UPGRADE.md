# Website Transitions & Professional Polish — Upgrade Prompt

Use this prompt to upgrade the **entire** Dealer Software website (Next.js `dealeros` app + root `styles.css` if applicable). The goal is to eliminate stagnant, slow, and heavy transitions; introduce a **snappy, professional, premium** feel; and apply **massive attention to detail**—even the slightest interaction must feel intentional and refined.

---

## 1. Core principles

- **Snappy over slow**: Micro-interactions (hover, focus, tap) should read as **instant but not jarring**. Prefer **80–120ms** for color/opacity, **120–180ms** for transforms (scale, translate). Avoid 250ms+ for simple hovers.
- **Easing over linear**: Replace generic `ease` / `cubic-bezier(0.4, 0, 0.2, 1)` with **purpose-built curves**:
  - **Enter / appear**: Slightly overshoot then settle (e.g. `cubic-bezier(0.34, 1.56, 0.64, 1)` or `cubic-bezier(0.16, 1, 0.3, 1)`).
  - **Exit / dismiss**: Quick deceleration (e.g. `cubic-bezier(0.4, 0, 1, 1)`).
  - **Hover / focus**: Subtle ease-out (e.g. `cubic-bezier(0.25, 0, 0.2, 1)` or `cubic-bezier(0.33, 1, 0.68, 1)`).
- **Specific over broad**: Never use `transition: all` for interactive elements. Always transition **only** the properties that change (e.g. `opacity`, `color`, `background-color`, `transform`, `box-shadow`, `border-color`). This reduces layout thrashing and keeps animations light.
- **GPU-friendly**: Use `transform` and `opacity` for motion where possible. Add `will-change: transform` or `will-change: opacity` **only** during the transition (or via hover/focus), then remove to avoid memory overhead.
- **Consistency**: Define a **single timing scale** (e.g. 80ms, 120ms, 180ms, 250ms) and use it everywhere. Same for easing curves—expose them as CSS variables and reuse.

---

## 2. Global CSS (`dealeros/app/globals.css`)

- **Add CSS variables** for timing and easing, e.g.:
  - `--ease-out-expo`: `cubic-bezier(0.16, 1, 0.3, 1)`
  - `--ease-out-snappy`: `cubic-bezier(0.25, 0, 0.2, 1)`
  - `--ease-enter`: `cubic-bezier(0.34, 1.56, 0.64, 1)` (subtle overshoot)
  - `--duration-instant`: `80ms`
  - `--duration-fast`: `120ms`
  - `--duration-normal`: `180ms`
  - `--duration-slow`: `250ms` (reserved for bigger UI reveals, e.g. modals, drawers)
- **Replace** all `transition: all 0.2s cubic-bezier(...)` and `0.3s` / `0.6s` usage with variable-based, **property-specific** transitions (e.g. `transition: transform var(--duration-fast) var(--ease-out-snappy), box-shadow var(--duration-fast) var(--ease-out-snappy)`).
- **`.btn-primary`**, **`.card-premium`**, **`.btn-premium`**:
  - Shorten durations to **120ms** for hover transform/opacity; **180ms** max for shadow.
  - **Remove** or drastically shorten the `.btn-premium::before` ripple (currently `0.6s`)—it contributes to a “heavy” feel. If kept, use **≤ 250ms** and a snappy ease-out.
- **`fadeInUp`**: Reduce from `0.6s` to **0.4s** and use `var(--ease-enter)`. Consider `opacity` + `transform` only; avoid animating layout properties.
- **Smooth scroll**: Keep `scroll-behavior: smooth` but ensure it doesn’t conflict with any custom scroll-driven animations. Prefer `scroll-behavior: smooth` only on `html`; avoid on nested scrollable areas unless intentional.

---

## 3. Tailwind / component-level transitions

Audit **every** `transition-*` and `duration-*` class across the app. Apply these rules:

### 3.1 Buttons, links, icon buttons

- **Primary & secondary CTAs** (e.g. `app/page.tsx`, `stock/[id]/page.tsx`, `contact/page.tsx`, `Footer`, `TopNav`):
  - Use `transition-[transform,box-shadow,background-color,border-color] duration-150` (or **120ms** if you add a custom Tailwind duration) with a custom `ease-out` curve.
  - Hover: `-translate-y-0.5` or `-translate-y-1` with a **subtle** shadow increase. **Never** use `duration-300` for simple button hovers.
- **Text links** (e.g. “View all”, footer links): `transition-colors duration-100` or `duration-150`. No transform unless it’s a clear “action” link (e.g. with arrow).
- **Icon-only buttons** (nav, gallery arrows, chatbot): `transition-colors duration-100`; if they have transform (e.g. scale on hover), use **120ms** and same easing.

### 3.2 Nav (`TopNav`)

- **Desktop nav links**: Replace `transition-all duration-150` with **property-specific** transitions: `transition-[color,opacity] duration-150` and, if you animate the underline, `transition-[width,transform] duration-150` with a snappy ease.
- **Mobile drawer**: Use **180–250ms** for open/close. Prefer `transform: translateX(...)` + `opacity` rather than `translateY`. Use `var(--ease-enter)` for open and `var(--ease-out-expo)` for close. Ensure **no** `transition: all`.
- **Hamburger ↔ X**: Icon transition **≤ 120ms**. Consider subtle rotation or morph if it fits the rest of the UI.

### 3.3 Cards & lists

- **CarCard** (grid + list):
  - Image hover: `transition-opacity duration-150` (replace `duration-300`). Opacity change **0.95** is fine; keep it subtle.
  - If you add a slight lift on hover (e.g. `translateY(-2px)`), use **120ms** and `ease-out`.
- **CarImageGallery** (thumbnails, arrows): Thumbnail border/ring transition **120ms**; arrow hover **100ms** for background/opacity.
- **Stock listing**: When filters change and the list updates, consider a **very short** stagger (e.g. 30–50ms per item) for new results—only if it doesn’t feel gimmicky. Keep filter UI transitions (dropdowns, chips) **≤ 150ms**.

### 3.4 Forms & inputs

- **Contact, ChatGate, ChatPanel, CarsList** (inputs, textareas, submit buttons):
  - `transition-colors duration-100` or `duration-150` for border/focus ring. **No** `transition: all`.
  - Focus: immediate or **80ms** shift to focus ring color; avoid slow fade.

### 3.5 Chatbot & floating UI

- **SalesChatbot** trigger button: `transition-[transform,background-color,box-shadow] duration-150`. If it expands to a panel, use **180–250ms** for panel open/close with `var(--ease-enter)` / `var(--ease-out-expo)`.
- **ChatPanel**: Same timing as chatbot; ensure open/close feels responsive, not sluggish.

### 3.6 Footer, trust signals, miscellaneous

- Footer links, **TrustSignals**, **ExpandableDescription** “Read more”: **100–150ms** `transition-colors` only, unless you add a minimal transform (e.g. underline width). Be consistent with other text links.

---

## 4. Page transitions & loading

- **Next.js navigation**: There are no view transitions by default. If you add them (e.g. `view-transitions` API or a lightweight lib), use **180–250ms** and `var(--ease-out-expo)`. Avoid long cross-fades; prefer quick fade + slight scale or slide.
- **Loading states** (`LoadingSpinner`, `dashboard/loading.tsx`): Spinner should feel **crisp**. Ensure `animate-spin` uses a smooth rotation; no extra “heavy” keyframes. If you use a skeleton, keep transitions **≤ 120ms** for placeholder → content.
- **Stock listing empty state**: No need for long animations; a simple **150ms** opacity-in is enough.

---

## 5. “Heavy” feel — what to remove or shorten

- **`transition-all`** anywhere: **Remove.** Replace with explicit properties.
- **`duration-300`** on hover/focus for buttons, links, nav, cards: **Reduce** to **120–150ms**.
- **`duration-300`** on images (e.g. CarCard): **Reduce** to **150ms**.
- **`0.6s`** anywhere (e.g. `fadeInUp`, `.btn-premium::before`): **Shorten** to **≤ 0.4s** (animations) or **≤ 0.25s** (ripples/hovers).
- **Generic `ease` or `ease-in-out`**: Replace with the **custom ease variables** above.

---

## 6. Attention-to-detail checklist

Apply these **everywhere** transitions exist:

- [ ] **Focus states**: Every interactive element has a visible focus style. Transitions for focus ring **≤ 80–100ms**.
- [ ] **Active states**: Buttons/links use `:active` (e.g. `scale(0.98)` or `translateY(0)`) with **≤ 80ms**.
- [ ] **Reduced motion**: Respect `@media (prefers-reduced-motion: reduce)`: disable or drastically shorten non-essential transitions; keep only essential feedback (e.g. focus).
- [ ] **Dark mode**: All transition timings and easings behave identically in dark mode; no lingering “light” feels.
- [ ] **Consistent timing scale**: Only use **80, 120, 180, 250** ms (or your chosen scale) across the app. No one-off `duration-[137ms]` unless strictly necessary.
- [ ] **No layout thrash**: Avoid transitioning `width`, `height`, `top`, `left` where `transform` can be used instead.

---

## 7. Files to touch (dealeros)

- `app/globals.css` — variables, `.btn-*`, `.card-premium`, `fadeInUp`, `html` scroll.
- `app/page.tsx` — hero CTAs, links, any decorative animations.
- `app/contact/page.tsx` — form inputs, buttons, links.
- `app/stock/page.tsx` — “View stock” CTA, filters if any.
- `app/stock/[id]/page.tsx` — buttons, links, gallery, **FinanceTeaser** (if it has interactivity).
- `components/site/TopNav.tsx` — nav links, drawer, logo, CTAs.
- `components/Footer.tsx` — links, CTAs.
- `components/CarCard.tsx` — image hover, any card hover.
- `components/CarImageGallery.tsx` — thumbnails, arrows, main image transition.
- `components/StockFilters.tsx` — dropdowns, chips, buttons.
- `components/CarsList.tsx` — buttons, links.
- `components/ChatPanel.tsx` — inputs, send button, open/close.
- `components/ChatGateForm.tsx` — inputs, submit.
- `components/SalesChatbot.tsx` — trigger button, panel.
- `components/MobileActionBar.tsx` — buttons.
- `components/ExpandableDescription.tsx` — “Read more” link.
- `components/TrustSignals.tsx` — any hover/focus.
- `components/FinanceTeaser.tsx` — if interactive.

If **Tailwind config** is extended (e.g. `tailwind.config.*`), add **custom durations** (e.g. `80`, `120`, `180`, `250`) and **custom cubic-bezier** utilities so you can use `duration-120` and `ease-out-snappy` in markup. If you use **Tailwind v4** `@theme` in `globals.css`, define them there instead.

---

## 8. Root `styles.css` (if still in use)

- Apply the **same** principles: CSS variables for timing/easing, **property-specific** transitions, **≤ 150ms** for hovers, **≤ 250ms** for mobile nav open/close.
- Replace `transition: color 0.2s`, `transform 0.2s`, etc. with variable-based, explicit transitions. Use `var(--ease-out-snappy)` (or equivalent) for nav links, buttons, feature cards, form focus.

---

## 9. Outcome

When done, **every** transition on the site should feel:

- **Snappy** — no sluggish hovers or slow fades.
- **Intentional** — easing and duration consistent and purpose-built.
- **Light** — no `transition-all`, no long-running effects.
- **Professional** — cohesive timing scale, accessible focus/active and reduced-motion support, and pixel-perfect attention to micro-interactions.

Execute the changes **incrementally** (e.g. globals first, then nav, then cards/forms, then chatbot/footer). Test each section on desktop and mobile, and with `prefers-reduced-motion: reduce` enabled, before moving on.
