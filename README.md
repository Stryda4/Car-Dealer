# Dealer Software — Website

A modern, responsive single-page website built from scratch with HTML, CSS, and JavaScript.

## What's included

- **index.html** — Structure: hero, features, about, contact, footer
- **styles.css** — Dark theme, responsive layout, custom typography (DM Sans, Fraunces)
- **script.js** — Mobile nav, footer year, contact form placeholder handling

## Run locally

1. Open the project folder in your editor.
2. Use **Live Server** (VS Code / Cursor) and open the suggested URL,  
   **or** double-click `index.html` to open it in your browser.
3. For a simple HTTP server (optional):

   ```bash
   npx serve .
   ```

   Then open `http://localhost:3000` (or the URL shown).

## Customize

- **Content**: Edit `index.html` — headings, copy, feature cards, contact section.
- **Colors / fonts**: Update CSS variables in `styles.css` (`:root`).
- **Form**: The contact form is client-side only. Connect it to your backend or a form service (e.g. Formspree, Netlify Forms) when you’re ready.

## Browser support

Works in modern browsers (Chrome, Firefox, Safari, Edge). Uses `backdrop-filter` for the header; older browsers may show a solid background instead.
