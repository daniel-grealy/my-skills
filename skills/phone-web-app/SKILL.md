---
name: "phone-web-app"
description: "Use when planning, designing or building a small phone-app-like website (PWA) on Cloudflare Pages + D1 with QR-code key access, from Claude Design mock-up to deployed site."
---

# Phone-app-like website

Design, build and deployment workflow for a small phone-app-like website that doesn't contain sensitive data.

Strongly flag if (and only if) the stack, access or workflow are insufficient to safely or effectively implement the user's idea. For example, they wish to store personal details, when an access key is the only line of defense.

## Stack (default, free tier)
- GitHub repo connected to Cloudflare Pages (deploys when you push).
- Static front end in `public/`: plain HTML, CSS and JS.
- Pages Functions for the API and the access check. D1 (SQLite) for data.
- Installable PWA.
- Keep settings (prices, limits, the access key) in a D1 `settings` table, not in code.

## Access: QR code + key
- The QR encodes `https://<site>/?k=<KEY>`.
- Renew the cookie on every valid visit (sliding expiry) so a Home Screen app doesn't lock people out after 30 days.
- No valid cookie: show only "Scan the QR code". All `/api/*` routes need the cookie.
- The key is rotated by changing the setting and printing a new QR. Include a `make-qr` script.
- Rotating the key must end access for everyone until they scan the new QR, including people with a still-valid cookie.
- Test that access still works after Add to Home Screen on iOS. The standalone app can have its own cookie storage, which is different from Safari's.
- No accounts or passwords. State on screen what is stored.

## Workflow
1. **Brief.** Get the goal, the rules, who will use it, and the data that will be stored.
2. **Mock-up in Claude Design.** A clickable mock-up of every screen and sheet with real strings. Iterate with the owner until approved.
3. **Build brief.** Write a short brief to the project (decisions, screens, rules, data model, access, open points). Update it as decisions change, and add a dated "changelog" section for later rounds.
4. **Implement.** Match the mock-up closely, validate everything on the server, and ensure safety and security when possible.
5. **Test.** At a 390px phone viewport, with local D1 (`wrangler pages dev`). Check there are no console or CSP errors and that the PWA installs.
6. **Hand over.** A README with the owner's deploy steps and phone tests.

## Design considerations
- Design for various phone sizes. The design and layout should be app-like to allow for PWA, although fully usable in a phone web browser.
- No sideways scrolling or wobble at any width.
- Favour no vertical scrolling, unless there is need for it.
- Optimize button size and spacing, which should auto-adjust for different phone screen sizes.
- Tapping a form field must not zoom the page.
- Make the UI and UX really good.
- If there is more than one language, the layout must be identical in all of them (reserve room for the longer text).
- Keep colours, radii and spacing as CSS variables in `theme.css` so the owner can restyle later.
- On an iPhone, the background should fill the whole screen, including behind the status bar and Safari's bottom toolbar. To achieve this, add a strip of background above and below the main screen. The page opens already scrolled so the main screen fits the view, and scrolling is blocked except inside sheets.