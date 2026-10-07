# Domain Sale Landing Pages

Five standalone static sites — one folder per domain. Each has an English "make an offer"
form that emails submissions to **15018647951@163.com** via [FormSubmit](https://formsubmit.co).

| Folder | Domain |
|---|---|
| `ai-product-photos.com/` | ai-product-photos.com |
| `getrecommendedbyai.net/` | getrecommendedbyai.net |
| `idstudio.space/` | idstudio.space |
| `rendervault.co/` | rendervault.co |
| `rendra.co/` | rendra.co |

Each folder is self-contained — deploy it as the site root for that domain.

```
<domain>/
├── index.html          # landing page + offer form
├── thanks.html         # shown after a successful submission
└── assets/style.css    # shared stylesheet (identical in every folder)
```

## Before you deploy — 2 required steps

### 1. Activate FormSubmit (one time only)

FormSubmit requires the recipient to confirm the address before it will forward anything.
This is per-address, not per-site, so **you only do it once for all five domains.**

1. Deploy any one site and load it in a browser.
2. Submit the form once with test data.
3. Open `15018647951@163.com` — you'll get an activation email from FormSubmit with a link.
4. Click the link. From then on, every submission from all five sites lands in your inbox.

> Until you click that link, submissions go nowhere. Do this first.

### 2. Check your spam folder

163.com filters aggressively against foreign senders, and FormSubmit sends from abroad.
After the first real submission, check the spam folder and mark it "not spam" /
add the sender to your contacts. If it keeps landing in spam, switch to the Resend
option described at the bottom.

## Deploying

### Option A — Cloudflare Pages (recommended, free)

1. Dash → **Workers & Pages** → **Create** → **Pages** → **Upload assets**.
2. Project name: e.g. `ai-product-photos`. Upload the **contents** of that domain's folder
   (`index.html`, `thanks.html`, `assets/`) — not the folder itself.
3. Deploy. Repeat for the other four, each as its own Pages project.
4. For each project: **Custom domains** → **Set up a custom domain** → enter the domain.
5. Cloudflare gives you a CNAME target. In **Spaceship** → your domain → **DNS**,
   delete the existing parking records and add that CNAME.

Repeat steps 4–5 for all five domains. HTTPS is provisioned automatically, usually in a few minutes.

### Option B — Netlify

Same idea: **Add new site** → **Deploy manually** → drag in one domain's folder contents.
Then **Domain management** → add custom domain → point DNS in Spaceship at Netlify's target.

### Option C — any existing hosting

If these domains already have hosting, just upload each folder's contents to that
domain's web root. There is no server-side code and no build step — plain HTML/CSS/JS.

## DNS note (Spaceship)

Your DNS stays at Spaceship — you're only adding CNAME records that point at the host.
Spaceship doesn't provide nameservers you must use; the CNAME approach works fine as long
as you're using Spaceship's default DNS. Don't touch the MX records.

## Spam protection

Each form has a honeypot field (`_honey`) — invisible to humans, bots fill it in and
FormSubmit discards those submissions. That's usually enough for a low-traffic page.

Note that FormSubmit's reCAPTCHA is disabled (`_captcha = false`) so buyers see no
Google widget, which keeps the page clean and the conversion rate higher. If you start
getting bot spam, delete this line from `index.html` in each folder:

```html
<input type="hidden" name="_captcha" value="false">
```

## Editing content

Everything you'd normally want to change is near the top of each `index.html`:

- **Headline / copy** — the `<h1>` and `<p class="lede">` blocks.
- **Email subject line** — the `_subject` hidden input, so you can tell at a glance
  which domain an offer is for.
- **Trust bullets** — the `<ul class="facts">` list (`Escrow-friendly`, etc.).
  Edit these to match what you actually offer.
- **Brand colors** — the `:root` block at the top of `assets/style.css`.
  `--gold` is the accent; `--bg` is the page background.

If you change `assets/style.css`, copy it into all five folders to keep them in sync.

## Upgrading to better deliverability (optional)

If 163 keeps filtering FormSubmit's mail, replace the form's `action` with a
serverless function that sends through [Resend](https://resend.com) from an address
on one of your own domains (e.g. `offers@rendra.co`). This requires adding SPF and DKIM
DNS records at Spaceship, but mail sent from your own authenticated domain is far less
likely to be flagged. Ask me and I'll wire it up.
