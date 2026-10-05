# 🎟️ HobbyCon Website

This repository contains the official website for **HobbyCon**, a curated, community-driven convention designed to help people discover, explore, and deepen their engagement with niche hobbies.

The site is static **HTML, Tailwind CSS, and JavaScript**. There is no build step and no database.

Code lives in GitHub and deploys automatically to **Vercel** on every push to `main`.

> **Procedures for adding and retiring event cards are in `CLAUDE.md`, not here.**
> That file is the working reference for day-to-day content edits.

---

## 🌐 Live Site

```text
https://hobbycon.com
```

GitHub repository:

```text
https://github.com/HobbyCon/hobby-con
```

Vercel preview (always current, useful for checking whether a change deployed):

```text
https://hobby-con.vercel.app
```

---

## 🧱 Project Structure

```text
index.html
events.html
retreats.html
tickets.html
vendors.html
community.html
contact.html
news.html
privacy.html
terms.html
refund-policy.html
shipping-policy.html
404.html
vercel.json          routing and redirects
CLAUDE.md            event card procedures
/images
/forms               waiver PDF
/js
└── main.js
style.css
```

**Keep the full website folder together.** The image folders, PDFs and logo files are required for the site to display correctly.

---

## ⚙️ Built With

- HTML
- Tailwind CSS (CDN)
- Feather Icons
- Vanilla JavaScript
- GitHub
- **Vercel** (hosting and deployment)
- **Sucuri** (WAF / CDN, sits in front of Vercel)
- GoDaddy (domain registrar and DNS)
- **Google Apps Script** (all form handling)
- Google Sheets (form submissions)
- Stripe Payment Links
- Google Analytics 4

Recommended VS Code extensions: Live Server, Prettier, Auto Rename Tag, Image Preview.

---

## 🧠 Key Features

- Fully responsive layout
- Shared navigation with active page highlighting
- Mobile-friendly menu toggle
- Dynamic footer year
- Modular JavaScript in `main.js`
- Clean URLs (`/retreats`, not `/retreats.html`) via `vercel.json`
- 90 vanity and typo redirects via `vercel.json`
- Apps Script form handling writing to Google Sheets
- Stripe payment link with a reference tying each booking to its sheet row
- Canonical URLs on every page

---

## 🚀 How the Site Is Served

```text
visitor
  → DNS (GoDaddy, ns31/ns32.domaincontrol.com)
  → 192.124.249.20   Sucuri WAF / CDN
  → 216.198.79.1     Vercel  (origin)
```

- **Vercel project:** `hobby-con`, under the `hellohobbycon@gmail.com` account
- **`vercel.json`** handles clean URLs and all redirects. It replaced the old `.htaccess`.
- **`.vercelignore`** keeps `README.md`, `CLAUDE.md` and internal folders out of the public deploy.

---

## 💻 How to Edit and Publish

### 1. Edit locally

Open the folder in VS Code, or use Claude Code from inside it.

Preview with Live Server: right-click `index.html` → **Open with Live Server**.

### 2. Commit and push

```bash
cd ~/Documents/GitHub/hobby-con
git add -A && git commit -m "clear message" && git push
```

Or use GitHub Desktop: review changes → commit to `main` → **Push origin**.

### 3. That's it

Vercel builds automatically on push. Usually live in under a minute. **There is no cPanel step any more.**

### If the change doesn't appear on hobbycon.com

The deploy almost certainly worked — Sucuri is caching. It has been seen holding pages for ~21 hours.

1. Check `https://hobby-con.vercel.app/<page>`. If the change is there, Vercel is fine.
2. Bypass Sucuri's cache with any query string: `https://hobbycon.com/js/main.js?v=1`
3. Clear the cache: GoDaddy → Website Security → Performance.

**Browser cache clearing and Incognito do not help** — the stale copy is at Sucuri's edge, not on your machine.

### What to test after a change

Homepage, navigation, mobile menu, images, forms, buttons, ticket links.

---

## 🧾 Forms, Tickets & Integrations

### Google Apps Script — all forms

Forms post directly to Google Apps Script web app endpoints, which write to Google Sheets and send notification emails. **Formspree is no longer used.**

| Form | Page |
|---|---|
| Retreat booking | retreats.html |
| Retreat email interest | retreats.html |
| Community email signup | community.html |
| Contact | contact.html |
| Vendor application | vendors.html |
| General partnership | vendors.html |

Each form's `action` points at a `script.google.com/macros/s/.../exec` URL, and carries a shared token in a hidden field.

**Two things that will catch you out:**

1. **Saving `Code.gs` does not deploy it.** You must use Deploy → **Manage deployments** → pencil → Version: **New version**. Choosing "New deployment" instead creates a *different* `/exec` URL and silently breaks the form pointing at the old one.

2. **Sheets are matched by header name, not column position.** Reordering columns is safe. Renaming or blanking a header makes that field silently vanish. A sheet whose header row does not contain both "Name" and "Email" in the first 3 rows will reject every submission — this once went unnoticed for weeks, because the failure notification emails looked like confirmations.

### Stripe

Ticketing and the retreat deposit use **Stripe Payment Links**.

The retreat form mints a reference (`HC-XXXXXXXX`) in the browser, sends it to Apps Script to be written into the Guest List row, and passes the same value to Stripe as `client_reference_id`. That links a payment to a signup.

**Known gap:** no Stripe webhook exists yet, so payments do not mark themselves "Paid" in the sheet. Reconciliation is manual against Stripe.

### Google Analytics

Google Analytics 4, tag `G-5ZGXHXVQK5`.

---

## 🔐 Access Checklist

The team should know where to find access for:

- GitHub repository (`HobbyCon/hobby-con`)
- Vercel account (`hellohobbycon@gmail.com`)
- Google account holding the Sheets and Apps Script (`hellohobbycon@gmail.com`)
- GoDaddy — domain, DNS, and Website Security (Sucuri)
- GoDaddy hosting / cPanel — **still active, used for email only**
- Website email account
- Stripe
- Canva, Adobe, logo files, brand assets
- Google Drive folders with images, flyers, PDFs

**Do not store passwords in this README.** They belong in the internal HobbyCon Drive or a password manager.

### Git access — one key per person

Everyone pushes with their own GitHub account and their own SSH key. Keys are never shared.

On a machine with more than one GitHub account, use an SSH host alias with `IdentitiesOnly yes` and point the repo's remote at it. An HTTPS remote authenticates with whatever single credential macOS has cached, which is shared across every repo — that can push or commit as the wrong account.

---

## 📦 Backup & Recovery

### Backup priority

**1. GitHub repository** — the source of truth. Full history, every file, every version.

```text
https://github.com/HobbyCon/hobby-con
```

**2. Full local website folder** — a complete copy from the website manager's laptop, including images and assets.

**3. Drive backups** — `Google Drive > BACKUPS`, including the full cPanel archive taken 2026-10-02 (`backup-10.2.2026_08-13-36_v7ayp6aj7xyb.tar.gz`). That archive holds the site as it existed on cPanel, including pages since removed from the repo.

### Making a backup

1. Duplicate the entire local website folder, images and assets included
2. Rename with the date, e.g. `hobbycon-website-backup-2026-10-05`
3. Compress to `.zip`
4. Upload to the shared HobbyCon backup folder
5. Open the ZIP to confirm it is complete

Do not rename internal folders (`images`, `js`, `forms`) unless the code is updated to match.

### Checking a backup is complete

Should contain `index.html`, all other page files, `style.css`, `js/main.js`, `images/`, `forms/`, and `vercel.json`.

If the site opens but images are missing, the image folder is missing or paths were changed.

---

# 🚨 Emergency Restore

### Option 1: Roll back in Vercel — fastest, start here

Vercel keeps every previous deployment.

1. Go to the `hobby-con` project → **Deployments**
2. Find the last known-good deployment
3. **Promote to Production**

Live within seconds, no git involved. This is the fastest way to undo a bad change.

### Option 2: Revert the commit

```bash
cd ~/Documents/GitHub/hobby-con
git revert <bad-commit-sha>
git push
```

Vercel redeploys automatically.

### Option 3: Restore from GitHub

1. Go to `https://github.com/HobbyCon/hobby-con`
2. **Code** → **Download ZIP**
3. Unzip and open `index.html` to check it locally

### Option 4: Rebuild the Vercel project

If the Vercel project is lost entirely:

1. Create a new project in Vercel, import `HobbyCon/hobby-con`
2. Framework preset: **Other**. No build command, no output directory.
3. Add `hobbycon.com` under Domains
4. Point the apex A record at the IP Vercel gives you

**Rollback IP if the site must go back to cPanel:** set the apex A record to `192.124.249.20`.

### After restoring, test

Homepage, navigation, images, flyers and PDFs, all six forms, ticket buttons, Stripe links, mobile and desktop layout, footer and social links.

If forms fail, check the Apps Script deployment is on the current version and that the sheet headers are intact.

---

## Current Workflow Summary

```text
Local folder
→ git push to main
→ Vercel builds automatically
→ Sucuri (may need a cache clear)
→ hobbycon.com
```

---

## 🧑‍💻 Contributors

Primary Maintainer: HobbyCon website manager
Organization: HobbyCon

---

## 🛡 License

© HobbyCon. All rights reserved.
