# HYK Tax — Backend Setup Guide

This document tells you exactly what to do before launching the site so real forms, real bookings, and real logins work. Everything is already wired — you just need to paste in the endpoints from the services you sign up for.

---

## 1. Contact Form (index.html)

**What it does:** The "Request your free consultation" form on the home page.

**Service:** [Formspree](https://formspree.io/) — free tier allows 50 submissions/month, which is plenty to start.

**Setup:**
1. Sign up at [formspree.io](https://formspree.io/) using `hello@hyktax.com`
2. Click "New Form" → name it "HYK Contact"
3. Copy the form endpoint (looks like `https://formspree.io/f/abc12345`)
4. Open `index.html`
5. Find the line: `const FORMSPREE_ENDPOINT = 'https://formspree.io/f/YOUR_FORM_ID';`
6. Replace `YOUR_FORM_ID` with your actual form ID
7. Save, upload to your host, done

**Where submissions go:** To whichever email you used to sign up at Formspree. Log into Formspree to view/export all submissions.

---

## 2. Booking Form (book.html)

**What it does:** The step-2 details form after picking a date/time.

**Service:** Formspree (same account, different form) — *or* Calendly if you want actual calendar sync.

### Option A: Formspree only (simpler)
1. In Formspree, create a second form called "HYK Bookings"
2. Copy the endpoint
3. Open `book.html`, find `const BOOKING_ENDPOINT = 'https://formspree.io/f/YOUR_BOOKING_FORM_ID';`
4. Replace with your endpoint

Every booking emails you with date, time, client details, and service interest. You manually send a calendar invite in reply.

### Option B: Formspree + Calendly (best experience)
1. Do Option A above
2. Sign up at [calendly.com](https://calendly.com/) (free) or [cal.com](https://cal.com/)
3. Create a 30-minute consultation event, copy the URL (like `https://calendly.com/hyktax/30min`)
4. In `book.html`, find `const CALENDLY_URL = '';`
5. Set it to your Calendly URL

After the form submits, Calendly opens in a new tab so the client can pick an exact slot that syncs with your calendar. You still get the Formspree email as a lead record.

---

## 3. Newsletter Signup (insights.html)

**What it does:** "One thoughtful tax insight in your inbox" email capture on the insights page.

**Service:** Start with Formspree (simplest). Upgrade to ConvertKit or Mailchimp once you're actually sending emails.

### Simple setup (Formspree)
1. Create a Formspree form called "HYK Newsletter"
2. Copy endpoint
3. Open `insights.html`, find `const NEWSLETTER_ENDPOINT = 'https://formspree.io/f/YOUR_NEWSLETTER_FORM_ID';`
4. Replace with your endpoint

Each signup emails you. You manually add to your email tool when you're ready to start sending.

### Production setup (ConvertKit / Mailchimp)
When ready to actually send newsletters, swap the endpoint:

**ConvertKit:**
```javascript
const NEWSLETTER_ENDPOINT = 'https://api.convertkit.com/v3/forms/YOUR_FORM_ID/subscribe?api_key=YOUR_API_KEY';
```

**Mailchimp:** Create an audience, copy the signup form action URL.

---

## 4. Client Portal Sign-In (portal.html)

**What it does:** The main sign-in card on the portal page.

**Service:** You have three options.

### Option A: TaxDome (recommended for a CPA firm)
TaxDome already handles auth, portal, documents, e-sign, messaging, and invoicing. Point your sign-in button at your TaxDome workspace.

1. Sign up for TaxDome
2. Get your workspace URL (like `https://hyktax.taxdome.com/login`)
3. Open `portal.html`, find `const PORTAL_URL = '';`
4. Set `PORTAL_URL = 'https://hyktax.taxdome.com/login';`
5. Decide what to do with the on-site demo dashboard:
   - Delete `dashboard.html` and all portal pages (documents, returns, etc.)
   - **OR** keep them as a preview for prospective clients

### Option B: Auth0 / Clerk (custom portal)
If you're building your own portal (not using TaxDome):
1. Sign up at [auth0.com](https://auth0.com/) or [clerk.com](https://clerk.com/)
2. Create a new application
3. Set `PORTAL_URL` to your Auth0/Clerk login URL
4. On the authenticated redirect, send users to `dashboard.html` with their session attached

### Option C: Box for file exchange
If you just want document exchange (no full portal):
1. Sign up at [box.com](https://www.box.com/)
2. Create a shared folder per client
3. Set `PORTAL_URL = 'https://app.box.com/login';`

---

## 5. Test Everything Before Launch

After wiring endpoints, test each form:

| Form | Test procedure |
|---|---|
| Contact (index) | Fill out and submit with your own email; confirm Formspree sends you the email |
| Booking (book) | Pick a date, fill the form, confirm booking email arrives |
| Newsletter (insights) | Enter your email, confirm you receive the Formspree notification |
| Sign-in (portal) | Enter email/password, confirm redirect to TaxDome (or dashboard.html if demo) |

**Pro tip:** Formspree's first submission from an email address may require you to click a verification link. Do the first submission from *your* email so you click it, not a client.

---

## 6. Spam Protection

Formspree has built-in spam filtering (reCAPTCHA + honeypot). If you get spam, log in and enable "Spam Protection" in the form settings.

If spam becomes a problem, consider:
- Upgrading to Formspree's paid plan ($10/mo, unlimited forms + better spam filters)
- Adding a custom honeypot field (a hidden field spam bots fill but humans don't)
- Adding hCaptcha or Cloudflare Turnstile

---

## 7. What to Remove Before Going Live

Search the codebase for these placeholders and replace:

| Placeholder | Replace with |
|---|---|
| `hello@hyktax.com` | Your real email |
| `+1 (305) 555-0199` | Your real phone |
| `AC-XXXXXX` | Your real Florida CPA license # |
| `Harshad Y. Khanna` | Real founder name (used in about.html and book.html) |
| `Miranda Reyes / Northwind Studios` | Leave as-is — it's the dashboard demo persona |
| 4 testimonials on index.html | Real client quotes (with permission) |
| Team bios on about.html | Real team members |

Use find-and-replace across all HTML files at once. On Mac/Linux:
```bash
grep -rl "hello@hyktax.com" *.html | xargs sed -i '' 's/hello@hyktax.com/REAL_EMAIL/g'
```

---

## 8. Analytics (Recommended)

Once live, you'll want to know which pages are working. Easiest options:

### Plausible (privacy-friendly, paid ~$9/mo)
Add before `</head>` on every page:
```html
<script defer data-domain="hyktax.com" src="https://plausible.io/js/script.js"></script>
```

### Google Analytics 4 (free)
Sign up at [analytics.google.com](https://analytics.google.com/), create a property, get your GA4 tag, add before `</head>`:
```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

---

## Summary

Four endpoints to paste in, then you're live. Total setup time: about 20 minutes if you've already got a Formspree account.

| File | Line to edit | What to replace |
|---|---|---|
| `index.html` | `const FORMSPREE_ENDPOINT` | Formspree form ID |
| `book.html` | `const BOOKING_ENDPOINT` (and optionally `CALENDLY_URL`) | Formspree + Calendly |
| `insights.html` | `const NEWSLETTER_ENDPOINT` | Formspree form ID |
| `portal.html` | `const PORTAL_URL` | TaxDome / Auth0 URL |

If an endpoint is left as the placeholder value, the site falls back to demo mode — so you can deploy safely even if you haven't finished configuring everything. Just don't leave it in demo mode after launch.
