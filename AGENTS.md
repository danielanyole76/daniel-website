# Repository notes

## What this project is

A single static page: `index.html` at the repository root. No package manifest,
no build step, no backend, no test suite, no linter. Nothing to install.

The README holds an **older, shorter copy** of the same page pasted inline, and
`Daniel_Website.zip` holds the version that is actually deployed. `index.html`
at the root is that version. When editing the page, edit the root file — the
README copy is stale and is not served.

## Running it in the sandbox

```
docker compose -f docker-compose.base44.yml up -d
```

nginx serves the bind-mounted checkout on host port 3000, so `index.html` edits
appear on the next page load. There is no hot-reload dev server and no watcher:
after changing the page, reload the preview (`reload_preview`) to see it.

`.base44/nginx.conf` is mounted as the server block. It exists only for the
sandbox: because the repository root is served as the document root, it refuses
dot-paths so `.git/` and `.base44/` are not reachable at the public preview URL.
Do not copy this config into a real deployment — serve a directory containing
`index.html` instead.

## Things that are expected to look broken in a headless preview

- **Camera face capture** needs `getUserMedia`. The automation browser has no
  camera, so the webcam path cannot be exercised there; the page's "upload a
  photo" fallback is the part to test. This is an environment limit, not a bug.
- **Font Awesome and TensorFlow.js load from public CDNs**, so the icons and the
  face-detection model need outbound network access to render.
- `alert`/`prompt` dialogs are used by the password-recovery links.

## Optional remote integration

`GOOGLE_SHEETS_SCRIPT_URL` near the top of the page's script is a placeholder
(`YOUR_GOOGLE_APPS_SCRIPT_WEB_APP_URL_HERE`). The submit handler explicitly
skips the POST while it is unset, so the page is fully functional without it.
Filling in a real Google Apps Script Web App URL makes registration attempts
forward to the user's sheet. It is a public endpoint, not a secret.

## Verifying a change

```
curl -sS http://localhost:3000/ | grep -c "<title>"
docker compose -f docker-compose.base44.yml ps
```
