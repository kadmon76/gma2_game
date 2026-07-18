# Deploying FX Trainer

Live site: **https://yaronl.com/fx/** — served by Apache from `/srv/www/fx_trainer`
(a git clone of `origin/main`). No build step: the files in the repo ARE the site.

## Normal deploy (every update)

On the Linode:

```bash
cd /srv/www/fx_trainer
git pull
```

That's it — the new files are live immediately.

## IMPORTANT: bump the service-worker cache when game files change

`sw.js` is **cache-first**, so returning visitors keep serving the *old* cached
copy until the cache name changes. Whenever a commit changes `index.html`,
`sw.js`, `manifest.json`, or an icon, bump the version string in `sw.js`
**in that same commit**, before deploying:

```js
const CACHE = 'fxtrainer-v1.0.0';   // -> v1.0.1, v1.1.0, etc.
```

On the next load the new service worker installs, the `activate` handler deletes
the old cache, and clients pick up the update. (A pure `DEPLOY.md`/docs change
needs no bump — it doesn't affect what the browser runs.)

## Verifying a deploy from anywhere

```bash
curl -sS https://yaronl.com/fx/index.html | sha256sum   # compare to local HEAD
```
