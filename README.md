# pepforge.app

This is the static website for PepForge. It holds the landing page, the privacy policy (required by Google Play), and the web page for requesting account deletion (also required by Google Play). It is plain HTML and CSS, with no JavaScript, no build step, no external fonts and no trackers.

| File | Purpose |
|---|---|
| `index.html` | Landing page |
| `privacy.html` | Privacy policy, effective 2026-11-21. Its URL goes into Play Console and the app (`EXPO_PUBLIC_PRIVACY_URL`). |
| `delete-account.html` | Account-deletion request page. Its URL goes into Play Console (Data safety → Data deletion). |
| `style.css` | Shared styles; light/dark follows the device's `prefers-color-scheme` setting. |
| `CNAME` | `pepforge.app`, the custom domain GitHub Pages serves |
| `.nojekyll` | Tells GitHub Pages to serve the files as they are, without running Jekyll |

## Before publishing: fill the placeholders and clear the flags

The pages must not go live while either of these commands prints anything:

```sh
grep -n '{{' *.html          # placeholders not yet filled
grep -n 'LEAD:' *.html       # open issues, shown on the page in a yellow box
```

Placeholders:
- `{{CONTACT_EMAIL}}`: the address that handles privacy and deletion requests. It must be monitored, because Play reviewers and users will write to it.
- `{{CONTROLLER_NAME}}`: the legal name of the data controller (a person or a company).

`LEAD:` flags (each is a visible note on the page; remove it once resolved):
1. `privacy.html` §5 and `delete-account.html`: confirm the data-processing terms and retention with Railway, Anthropic and Google Cloud.
2. `privacy.html` §6: confirm the transfer safeguards (for example, SCCs), or delete that sentence.
3. `privacy.html` §7 and `delete-account.html`: confirm Railway backup retention, and whether expired session rows are cleaned up.
4. `privacy.html` §8: "clear my data" exists only on the web; export is admin-only; confirm the 30-day reply commitment.
5. `privacy.html` §9 and `delete-account.html`: **build the in-app "Delete account" screen**. The server route exists but the app has no button. Google Play requires the in-app path.

## Publishing (GitHub Pages from the default branch root)

1. Create a new **public** repository, for example `pepforge-site`, and push these files to the root of its default branch (`main`). `.nojekyll` and `CNAME` must be at the root.
2. In the repository, open **Settings → Pages**. Under **Build and deployment**, set **Source** to "Deploy from a branch", choose branch `main` and folder `/ (root)`, then click **Save**.
3. Under **Custom domain**, enter `pepforge.app` and save. The `CNAME` file already contains it.
4. Set the DNS records below. When the DNS check passes, tick **Enforce HTTPS**. The certificate can take up to an hour to issue.
5. Recommended: verify `pepforge.app` for your account (**GitHub profile Settings → Pages → Add a domain**). This stops anyone else from claiming the domain on GitHub Pages.

## DNS records for pepforge.app

These are from GitHub's documentation, "Managing a custom domain for your GitHub Pages site" (docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site). The IPs were checked against that page on 2026-11-21.

Apex domain `pepforge.app` (host `@`):

| Type | Host | Value |
|---|---|---|
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |
| AAAA | @ | 2606:50c0:8000::153 |
| AAAA | @ | 2606:50c0:8001::153 |
| AAAA | @ | 2606:50c0:8002::153 |
| AAAA | @ | 2606:50c0:8003::153 |

`www` subdomain:

| Type | Host | Value |
|---|---|---|
| CNAME | www | `<github-user-or-org>.github.io` (the account that owns the site repository) |

Notes:
- Remove any other A, AAAA or ALIAS records on `@`, and any wildcard (`*`) record. A wildcard record lets anyone take over a subdomain.
- `.app` is on the HSTS preload list, so browsers only load it over HTTPS. The site will not open until GitHub has issued the certificate and **Enforce HTTPS** is ticked.
- With `pepforge.app` set as the custom domain and `www` pointing at GitHub, GitHub redirects `www.pepforge.app` to `pepforge.app`.
- Check the records: `dig pepforge.app +noall +answer -t A`, `dig pepforge.app +noall +answer -t AAAA`, `dig www.pepforge.app +noall +answer`.

## URLs to enter once live

- Play Console → App content → Privacy policy: `https://pepforge.app/privacy.html`
- Play Console → Data safety → Data deletion → account deletion URL: `https://pepforge.app/delete-account.html`
- App build: `EXPO_PUBLIC_PRIVACY_URL=https://pepforge.app/privacy.html`. This makes the "Privacy policy" row appear under More (`mobile/src/app/(tabs)/more/index.tsx`).

## Keeping the policy true

The privacy policy describes the code as of `origin/main` on 2026-11-21. Update it, and its effective date, **before** shipping any change that adds a data type, a third party (for example Play Billing, push notifications, crash reporting or photo cloud storage), or a change to retention.
