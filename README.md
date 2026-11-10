# EMC Technical RFQ Portal — public page (GitHub Pages)

Static page only. It holds **no secret**: every rule is enforced by the Supabase backend (database RLS, portal
functions, the per-request access-code check, the `customer-access` Edge Function).

| File | What it is |
|---|---|
| `index.html` | The accepted Step 03 **v1.4.1** page (`app/index.html`, SHA-256 `f97257b9…`), unchanged, placed by `scripts/build_page.py` inside a complete document head: `<!doctype html>`, charset, **mobile viewport**, Content-Security-Policy, `noindex`, `no-referrer`. |
| `config.json` | Server address + the **public** publishable key (`sb_publishable_…`) + the page address. **Empty until the Staging project exists.** Never the secret key. |
| `scripts/build_page.py` | Builds `index.html`. The policy allows API calls to ONE address: the Staging project URL. |
| `scripts/check_no_secrets.sh` | Refuses any secret-looking content. Run before every push; also runs on GitHub (`.github/workflows/secret-check.yml`). |

## Before publishing (after the Staging project exists)
```bash
python3 scripts/build_page.py <v1.4.1>/app/index.html f97257b9eac9563afd02c4011977294cd1ee1de113b7805bfe04731336117d3e https://<project-ref>.supabase.co index.html
# config.json: supabase_url = https://<project-ref>.supabase.co, anon_key = sb_publishable_…, portal_url = https://<user>.github.io/<repo>/
bash scripts/check_no_secrets.sh .
```
The `index.html` kept here now allows any `https://*.supabase.co` address; it is rebuilt with the exact address above.

Status: prepared locally. **Not pushed; GitHub Pages not enabled**: both need the owner's approval.
Limits of GitHub Pages: no HTTP headers (the security policy is a `<meta>` tag, so `frame-ancestors` cannot be set);
with a free account the repository must be public (the page source is then readable; it contains nothing secret).

## Tested locally (2026-11-06)
- The full staff + Seara multi-device flow (29 checks) through this build served from its own address, against the
  local stack: 29/29.
- Phone layout (iPhone 13, Pixel 5): standards mode, real phone width, no sideways scrolling, code sign-in, the policy
  blocks other API addresses: 14/14.
- The v1.3 build of this repo had no doctype or viewport: phones showed it at 980 px wide. Fixed by this build.
