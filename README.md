# GhostLimit — license activation page

Static page where a [GhostLimit](https://github.com/KevssG/GhostLimit) buyer activates their license
right after paying. It stores nothing and needs no cookies: it talks to the sales API by `fetch` and
shows the license for the buyer's computer.

## The flow it belongs to

```
Stripe Payment Link ──pay──▶ this page (?s=checkout_session)
        │                         │ fetch ?api=confirmar
        │                         ▼
        │                Google Apps Script (sales API)
        │                 ├─ asks Stripe (restricted read-only key) whether the session is paid
        │                 ├─ writes the sale to a Google Sheet (referral + commission)
        │                 └─ emails the buyer: download link + "activate your license"
        │
        └─ every 5 min the same script sweeps recent sessions (OXXO, closed tabs)

buyer pastes the machine ID ──▶ ?api=activar ──▶ RSA-signed license for that machine (2 activations)
```

Why a static page: Apps Script web apps break when the browser has several Google accounts signed in
(Google rewrites the URL to `/macros/u/N/…`). A page on another origin that calls the API without
credentials avoids that entirely.

No webhook on purpose: an Apps Script web app answers POSTs with a 302, which Stripe treats as a failed
delivery. Redirect + periodic sweep is enough for a one-off digital product.

---

**Español.** Página estática donde el comprador de GhostLimit activa su licencia al terminar de pagar.
No guarda datos ni usa cookies: consulta la API de venta (Google Apps Script) y muestra la licencia
firmada para su computadora. Forma parte de un flujo sin servidor: enlace de pago de Stripe → Apps
Script que verifica el pago, registra la venta y la comisión del promotor y manda el correo con la
descarga → esta página, que emite la licencia (2 activaciones por compra).
