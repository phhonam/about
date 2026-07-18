# Deploying p-nam.com (DigitalOcean App Platform + GoDaddy)

The site is static: `index.html` + the `assets/` folder. No build step. Only external
dependency is Google Fonts (loaded over their CDN).

## 1. Put the code on GitHub
From this folder:

```sh
git add -A
git commit -m "Personal site"
git branch -M main
# create an empty repo on github.com first (e.g. "personal-site"), then:
git remote add origin https://github.com/<your-username>/<repo>.git
git push -u origin main
```

## 2. Create the static site on DigitalOcean
1. DigitalOcean → **Create → App Platform**.
2. Source: **GitHub** → authorize → pick the repo → branch `main`.
3. DO auto-detects a **Static Site**. Confirm:
   - Build command: *(leave empty)*
   - Output directory: `/`
4. Plan: **Starter (free)** for static sites. Create.
5. You'll get a `…ondigitalocean.app` URL — open it and confirm the site looks right.

Every `git push` to `main` re-deploys automatically.

## 3. Point p-nam.com at it (apex domain → move DNS to DigitalOcean)
Apex/root domains can't use a CNAME and GoDaddy has no ALIAS record, so let DO host the DNS.

1. **DO → Networking → Domains → add** `p-nam.com`.
2. **DO → your App → Settings → Domains → Add Domain** `p-nam.com` (and `www.p-nam.com`).
   DO creates the records automatically and issues a free auto-renewing HTTPS cert.
3. **GoDaddy → p-nam.com → DNS → Nameservers → Change → "I'll use my own nameservers":**
   ```
   ns1.digitalocean.com
   ns2.digitalocean.com
   ns3.digitalocean.com
   ```
4. Wait for propagation (minutes to a few hours). Once it flips, `p-nam.com` serves the new
   site over HTTPS and the old Cargo site stops resolving.

### ⚠️ Before switching nameservers
Moving nameservers hands **all** of p-nam.com's DNS to DigitalOcean, so any records that live
at GoDaddy today will stop working unless you recreate them in DO first. Check GoDaddy's DNS
for:
- **MX / email** records (custom email on the domain) — recreate in DO if present.
- Any **TXT** (SPF/DKIM/verification) or other subdomain records you rely on.

Contact email on the site is `phhonam@gmail.com`, so if you don't run custom email on
p-nam.com there's likely nothing to migrate — but verify before you flip.
