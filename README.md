# digitizer-status

The source of <https://status.digitizer.dev>.

This repo is a thin static wrapper. It does not compute or report any status
itself — the status page is produced by [Robotalp](https://digitizer.robotalp.com/)
and embedded here in a full-page iframe, so that it is served from a Digitizer
domain and covered by the Digitizer GTM container.

## Contents

| File | Purpose |
| --- | --- |
| `index.html` | The entire page: GTM snippet, page styling, and the Robotalp iframe. |
| `_headers` | Response headers applied by Cloudflare Pages. |

That is the whole project. There is no build step, no dependencies, no
package manager, and no server-side code.

## Hosting

Cloudflare Pages, connected to this repo:

- Production branch: `main`
- Framework preset: **None**
- Build command: *(empty)*
- Output directory: `/`
- Custom domain: `status.digitizer.dev`

Every push to `main` deploys. Pull requests get a preview deployment.

GitHub Pages is intentionally **not** used — it was disabled once Cloudflare
Pages took over, to avoid a second copy of the page at
`digitizers.github.io/digitizer-status/`.

## Local preview

Open `index.html` in a browser, or serve the directory over HTTP so the
iframe and GTM behave as they do in production:

```sh
python3 -m http.server 8000
# then open http://localhost:8000
```

`_headers` is a Cloudflare Pages feature and has no effect locally.

## Things worth knowing before changing this

- **Keep the page static.** It used to be `index.php`, which contained no PHP
  at all. Static hosts do not execute PHP and do not serve `.php` as
  `text/html`, so the extension was the only thing standing between this file
  and a working deploy. Do not reintroduce a server-side extension.
- **The page depends on a third party.** If `digitizer.robotalp.com` is down,
  changes its domain, or starts sending `X-Frame-Options` / a
  `frame-ancestors` CSP that excludes us, this page breaks and nothing in this
  repo can fix it. Check Robotalp first when the page renders blank.
- **`index.html` reveals the iframe after a 3-second `setTimeout`.** The iframe
  starts hidden and zero-sized so that visitors do not watch the Robotalp app
  paint itself in stages. The delay is a fixed guess, not a load signal.
- **Response headers live in `_headers`, not in the HTML.** HSTS is set at the
  Cloudflare zone level and is deliberately absent from that file, so it is not
  sent twice.
- **The GTM container is `GTM-N2RB6P2X`**, the same one used across Digitizer
  properties. The `<noscript>` fallback must stay immediately after `<body>`.

## Contributing

Branch, open a pull request, let the automated review run, then merge — no
direct pushes to `main`.
