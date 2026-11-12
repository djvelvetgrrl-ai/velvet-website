# Decision record

Short records of decisions about the site and why they were made. Newest first. This folder is listed in `.assetsignore`, so it is not published on the website.

## 2026-11-12: Site check, fixes, and www (PR #9)

**Context.** The owner asked for a check of the live site and the Cloudflare project. The site was up and matched `main` (PR #8), but three problems turned up: the booking form's logo was broken, the site served its own `.git` folder and `wrangler.toml` to anyone, and `www.djvelvetgrrl.com` did not connect. A keyboard check also found controls on the booking form that a keyboard could not reach.

**Decisions.**

1. **Images stay inlined in the HTML, not stored as files.** Since 2026-08-08 every image is a `data:` URI inside the page, and the image files were deleted from the repository. `intake.html` still pointed at the deleted `mark-v.png`, so its logo was broken from August to November. It now uses the same inline V-mark as `index.html`. *Why:* one convention means a deleted file can't silently break a page again.

2. **`.assetsignore` lists every file in the repository that is not part of the website.** Cloudflare publishes the whole repository folder, so anything not listed is public. It now lists `CLAUDE.md`, `.git`, `wrangler.toml` and `docs`. *Why:* the live site was serving `/.git/config` and `/wrangler.toml`. Nothing private leaked: the GitHub repository is public, and a scan of all 63 commits found no keys. The fix was confirmed with `wrangler dev`, comparing the old and new lists.

3. **`wrangler.toml` lists both `djvelvetgrrl.com` and `www.djvelvetgrrl.com` as custom domains.** *Why:* `www` had nothing attached, so visitors who typed `www.` got an error. Both must stay listed, because listing only one can make a deploy detach the other. `www` shows the same site; it does not redirect. The owner approved this `wrangler.toml` change. After the merge, Cloudflare's build added `www` with a valid certificate.

4. **Clickable controls on the forms are real `<button>` elements that report their state** (`aria-checked` or `aria-pressed`), never clickable `<div>`s. *Why:* the "booking on behalf of someone else" checkbox and the six special-moment chips could not be reached by keyboard, which fails WCAG 2.2 AA. The data the form sends did not change.

**Checked after the merge.** On both domains, at 390px and 320px: no broken images, no sideways scrolling, keyboard access works, and the hidden files return 404. Both pages match `main` byte for byte.

**Not checked.**
- A real form submission (it would add a row to the Sheet and send an email).
- A screen-reader pass.
- The Cloudflare project's own settings and build history: no Cloudflare credentials have been given to the agent.

## Standing rules from the owner

- **No changes in Cloudflare without asking first.** Reading is fine once access is given.
- **Cloud sessions can reach `djvelvetgrrl.com` and `www.djvelvetgrrl.com`.** Both are on the Default cloud environment's allowed domains. There is no Cloudflare API access. If it is needed, it goes in as a read-only token saved as a network secret, never pasted in chat.

## Open questions

- **Cloudflare Web Analytics** adds a script (`static.cloudflareinsights.com`) to every page. It is switched on in Cloudflare, not in this repository. Confirm whether it is wanted.
- **`www` redirect:** should `www` redirect to `djvelvetgrrl.com` instead of showing a copy of the site? That would be a Cloudflare redirect rule.
