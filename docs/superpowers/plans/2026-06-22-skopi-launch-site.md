# skopi.github.io Launch Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a minimal Jekyll GitHub Pages site for skopi with a "We are launching…" landing page and a privacy policy page.

**Architecture:** Static Jekyll site served from the repo root on `main`. Two shared layouts (`default` for chrome, `page` for long-form content) feed a config-driven landing page (`index.html`) and a Markdown privacy policy (`privacy.md`). One shared stylesheet uses the logo's teal/cyan palette. No JavaScript.

**Tech Stack:** Jekyll (via the `github-pages` gem), HTML, Markdown, CSS, Liquid templating.

## Global Constraints

- Brand display name is **skopi.ai** (header + `<title>`), copied verbatim — not "skopi".
- Privacy contact email is **privacy@skopi.com** — do not change it.
- "Last Updated: June 21, 2026" line preserved in the privacy policy.
- Palette: dark teal background `#0e2a2a`, cyan accent `#5eead4`, light text.
- No JavaScript anywhere. No external font/CDN dependencies (system font stack).
- No CNAME / custom domain — stay on `skopi.github.io`.
- Logo is the local asset `assets/skopi-mark.png` (already in the repo).
- Site builds from repository root on the `main` branch.

---

### Task 1: Jekyll scaffolding (config, Gemfile, gitignore)

**Files:**
- Create: `_config.yml`
- Create: `Gemfile`
- Create: `.gitignore`

**Interfaces:**
- Consumes: nothing.
- Produces: `site.title` (`skopi.ai`), `site.tagline`, `site.notify_link`, `site.description` available to all templates; a buildable Jekyll project.

- [ ] **Step 1: Create `.gitignore`**

```gitignore
_site/
.jekyll-cache/
.jekyll-metadata
.sass-cache/
.bundle/
vendor/
Gemfile.lock
```

- [ ] **Step 2: Create `Gemfile`**

```ruby
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins
```

- [ ] **Step 3: Create `_config.yml`**

```yaml
# Brand / display
title: skopi.ai
description: "skopi — monitoring service. Launching soon."

# Landing page copy (config-driven subtext under the headline)
tagline: "Monitoring, reimagined. Something new is on the way."

# Optional notify link: a mailto: address or URL.
# Leave empty ("") to hide the button.
notify_link: ""

# Build settings
exclude:
  - Gemfile
  - Gemfile.lock
  - README.md
  - docs/
```

- [ ] **Step 4: Install dependencies and build**

Run: `bundle install && bundle exec jekyll build`
Expected: Build completes; `_site/` directory is generated with no errors.

> If `bundle install` fails on the local Ruby setup, fall back to a directly
> installed Jekyll: `gem install jekyll` then `jekyll build`. The site uses no
> Pages-only plugins, so plain Jekyll builds it identically.

- [ ] **Step 5: Commit**

```bash
git add _config.yml Gemfile .gitignore
git commit -m "chore: scaffold Jekyll site config"
```

---

### Task 2: Shared layouts and stylesheet

**Files:**
- Create: `_layouts/default.html`
- Create: `_layouts/page.html`
- Create: `assets/css/style.css`

**Interfaces:**
- Consumes: `site.title`, `site.description` (from Task 1); `assets/skopi-mark.png`.
- Produces: `layout: default` (full-page chrome with header + footer) and `layout: page` (default + `.container` wrapper) for later tasks; CSS classes `.launch`, `.container`, `.important`, `.highlight`, `.contact-info`, `.site-header`, `.site-footer`.

- [ ] **Step 1: Create `assets/css/style.css`**

```css
:root {
  --bg: #0e2a2a;
  --bg-soft: #103535;
  --accent: #5eead4;
  --text: #e7f5f3;
  --muted: #9fc4bf;
  --content-bg: #ffffff;
  --content-text: #2d3a39;
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto,
    'Helvetica Neue', Arial, sans-serif;
  line-height: 1.6;
  color: var(--text);
  background-color: var(--bg);
}

a { color: var(--accent); text-decoration: none; }
a:hover { text-decoration: underline; }

/* Header */
.site-header {
  display: flex;
  align-items: center;
  gap: 12px;
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
}
.site-header .logo { height: 36px; width: auto; }
.site-header .brand { font-size: 22px; font-weight: 600; color: var(--text); }

/* Footer */
.site-footer {
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  margin-top: 60px;
  padding: 30px 20px;
  text-align: center;
  color: var(--muted);
  font-size: 14px;
}

/* Landing page */
.launch {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 24px;
  gap: 20px;
}
.launch .logo { width: 140px; height: 140px; }
.launch h1 { font-size: 40px; font-weight: 700; color: var(--text); }
.launch .tagline { font-size: 18px; color: var(--muted); max-width: 520px; }
.launch .notify {
  display: inline-block;
  margin-top: 8px;
  padding: 12px 28px;
  border: 1px solid var(--accent);
  border-radius: 999px;
  color: var(--accent);
  font-weight: 600;
}
.launch .notify:hover { background: var(--accent); color: var(--bg); text-decoration: none; }
.launch .policy-link { margin-top: 16px; font-size: 14px; color: var(--muted); }

/* Content container (privacy policy) */
.container {
  max-width: 900px;
  margin: 0 auto;
  padding: 40px 20px;
  background: var(--content-bg);
  color: var(--content-text);
  border-radius: 8px;
}
.container h1 {
  font-size: 28px; font-weight: 700; color: #1a1a1a;
  border-bottom: 2px solid #0066cc; padding-bottom: 12px; margin-bottom: 20px;
}
.container h2 { font-size: 22px; font-weight: 600; margin: 28px 0 14px; color: #1a1a1a; }
.container h3 { font-size: 16px; font-weight: 600; margin: 18px 0 10px; color: #333; }
.container p { margin-bottom: 14px; color: #444; }
.container ul, .container ol { margin: 0 0 16px 24px; }
.container li { margin-bottom: 8px; color: #444; }
.container .last-updated { font-size: 14px; color: #666; font-style: italic; margin-bottom: 30px; }
.container a { color: #0066cc; }

/* Callout boxes */
.important {
  background: #ffe0e0; border-left: 4px solid #d32f2f;
  padding: 16px; margin: 20px 0; border-radius: 4px; font-weight: 500;
}
.important strong { color: #c62828; }
.highlight {
  background: #fff3cd; border-left: 4px solid #ff9800;
  padding: 16px; margin: 20px 0; border-radius: 4px;
}
.highlight strong { color: #e65100; }
.contact-info {
  background: #e3f2fd; border-left: 4px solid #1976d2;
  padding: 20px; margin: 20px 0; border-radius: 4px;
}
.contact-info h3 { margin-top: 0; }

@media (max-width: 768px) {
  .launch h1 { font-size: 30px; }
  .launch .logo { width: 110px; height: 110px; }
  .container { padding: 24px 16px; }
  .container h1 { font-size: 22px; }
  .container h2 { font-size: 18px; }
}
```

- [ ] **Step 2: Create `_layouts/default.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="{{ page.description | default: site.description }}">
  <title>{% if page.title %}{{ page.title }} — {% endif %}{{ site.title }}</title>
  <link rel="icon" type="image/png" href="{{ '/assets/skopi-mark.png' | relative_url }}">
  <link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">
</head>
<body>
  <header class="site-header">
    <img class="logo" src="{{ '/assets/skopi-mark.png' | relative_url }}" alt="skopi logo">
    <span class="brand">{{ site.title }}</span>
  </header>

  {{ content }}

  <footer class="site-footer">
    <p>&copy; 2026 Skopi. All rights reserved. ·
      <a href="{{ '/privacy/' | relative_url }}">Privacy Policy</a></p>
  </footer>
</body>
</html>
```

- [ ] **Step 3: Create `_layouts/page.html`**

```html
---
layout: default
---
<div class="container">
  <h1>{{ page.title }}</h1>
  {{ content }}
</div>
```

- [ ] **Step 4: Build and verify layouts compile**

Run: `bundle exec jekyll build`
Expected: Build succeeds with no Liquid errors. (No rendered output to check yet — pages are added in Tasks 3–4.)

- [ ] **Step 5: Commit**

```bash
git add _layouts/default.html _layouts/page.html assets/css/style.css
git commit -m "feat: add shared layouts and stylesheet"
```

---

### Task 3: Launch landing page

**Files:**
- Create: `index.html`

**Interfaces:**
- Consumes: `site.title`, `site.tagline`, `site.notify_link` (Task 1); CSS class `.launch` and friends (Task 2).
- Produces: the site's root page at `/`.

> Note: `index.html` defines its own full document (it does **not** use the
> `default` layout) so the landing page can be a centered full-screen hero
> without the standard header/footer chrome. It still links the shared
> stylesheet and includes its own footer policy link.

- [ ] **Step 1: Create `index.html`**

```html
---
layout: null
---
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="{{ site.description }}">
  <title>{{ site.title }}</title>
  <link rel="icon" type="image/png" href="{{ '/assets/skopi-mark.png' | relative_url }}">
  <link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">
</head>
<body>
  <main class="launch">
    <img class="logo" src="{{ '/assets/skopi-mark.png' | relative_url }}" alt="skopi logo">
    <h1>We are launching…</h1>
    <p class="tagline">{{ site.tagline }}</p>
    {% if site.notify_link and site.notify_link != "" %}
    <a class="notify" href="{{ site.notify_link }}">Notify me</a>
    {% endif %}
    <a class="policy-link" href="{{ '/privacy/' | relative_url }}">Privacy Policy</a>
  </main>
</body>
</html>
```

- [ ] **Step 2: Build the site**

Run: `bundle exec jekyll build`
Expected: Build succeeds; `_site/index.html` is created.

- [ ] **Step 3: Verify rendered landing page content**

Run: `grep -o "We are launching" _site/index.html && grep -o "Monitoring, reimagined" _site/index.html && grep -o "skopi-mark.png" _site/index.html`
Expected: All three strings print (headline, tagline from config, logo reference present).

- [ ] **Step 4: Verify the notify button is hidden when `notify_link` is empty**

Run: `grep -c "class=\"notify\"" _site/index.html`
Expected: `0` (button omitted because `notify_link` is `""` in `_config.yml`).

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add launch landing page"
```

---

### Task 4: Privacy policy page

**Files:**
- Create: `privacy.md`

**Interfaces:**
- Consumes: `layout: page` (Task 2); callout CSS classes `.important`, `.highlight`, `.contact-info` (Task 2).
- Produces: the privacy policy at `/privacy/`.

> The policy is Markdown with embedded HTML for the callout boxes and table of
> contents. The `page` layout already renders the `<h1>Privacy Policy</h1>`
> title, so the body starts at the "Last Updated" line. Email stays
> `privacy@skopi.com`.

- [ ] **Step 1: Create `privacy.md`**

````markdown
---
layout: page
title: Privacy Policy
description: "Skopi Privacy Policy — how we protect your data and mobile number."
permalink: /privacy/
---

<p class="last-updated">Last Updated: June 21, 2026</p>

## 1. Introduction

Skopi ("we," "us," "our," or "Company") is committed to protecting your privacy. This Privacy Policy explains how we collect, use, disclose, and safeguard your information when you use our monitoring service and related SMS alert features.

Please read this Privacy Policy carefully. If you do not agree with our policies and practices, please do not use our services.

## 2. Information We Collect

### 2.1 Information You Provide

- **Account Information:** Name, email address, password, and billing details
- **Mobile Number:** Your phone number to receive SMS alerts
- **Service Information:** Details about the systems or assets you wish to monitor
- **Communication Data:** Content of messages, inquiries, and support requests

### 2.2 Information Collected Automatically

- **Device Information:** Device type, operating system, and unique identifiers
- **Usage Data:** How you interact with our service, including login times and feature usage
- **Location Data:** Approximate location (if permissions are granted)
- **Log Data:** IP address, browser type, and access times

## 3. Mobile Number Protection and Non-Sharing Policy

<div class="important">
<strong>We do not share, sell, rent, or disclose your mobile number to third parties under any circumstances.</strong> Your mobile number is used exclusively to deliver SMS alerts related to your monitoring service with Skopi.
</div>

Your phone number is stored securely and treated with the highest level of confidentiality. Third-party service providers who handle SMS delivery on our behalf are bound by strict confidentiality agreements and may only use your number for the purpose of delivering your alerts.

## 4. SMS Message Practices and Frequency

### 4.1 Message Frequency

Your account may receive SMS alerts based on the monitoring rules and events you configure:

- **Alert Frequency:** Messages are sent only when monitored events are triggered
- **Typical Volume:** Frequency varies depending on your monitoring settings and system activity
- **Opt-Out Options:** You can modify alert settings or disable SMS notifications at any time through your account dashboard

### 4.2 Message Content

SMS messages contain alert information only, such as:

- Alert type and severity
- System or asset identification
- Timestamp of the event
- Link to view full details (data charges may apply)

## 5. Message and Data Rates Disclosure

<div class="highlight">
<strong>MESSAGE AND DATA RATES MAY APPLY.</strong> Standard SMS rates charged by your mobile service provider apply to all alerts sent to your mobile number. Skopi does not charge additional fees for SMS alerts; however, your carrier's standard message and data rates will be assessed per message received. Please contact your mobile service provider for details regarding your messaging plan and applicable charges.
</div>

Frequency of messages varies based on system activity and your configured alert settings.

## 6. How We Use Your Information

We use collected information to:

- Deliver monitoring alerts and notifications via SMS
- Maintain and improve our service
- Process transactions and send billing information
- Respond to customer inquiries and support requests
- Detect, prevent, and address fraud and security issues
- Comply with legal obligations
- Send service announcements and updates

## 7. Data Security

We implement industry-standard security measures to protect your information, including:

- Encryption in transit (SSL/TLS)
- Encryption at rest for sensitive data
- Secure authentication protocols
- Regular security audits and monitoring
- Access controls limiting employee access to personal data

However, no method of transmission over the internet is 100% secure. We cannot guarantee absolute security.

## 8. Data Retention

We retain your information for as long as your account is active. After account termination, we retain data as required by law or for legitimate business purposes (such as fraud prevention) and then securely delete it.

## 9. Your Rights and Choices

You have the right to:

- **Access:** Request a copy of your personal information
- **Correction:** Update or correct inaccurate data
- **Deletion:** Request deletion of your account and associated data
- **Opt-Out:** Disable SMS alerts or modify notification preferences
- **Portability:** Request your data in a portable format

To exercise these rights, contact us at the information provided below.

## 10. Third-Party Services

We may use third-party service providers for:

- SMS delivery and telecommunications services
- Cloud hosting and data storage
- Payment processing
- Analytics

These providers are contractually obligated to use your information only as necessary to provide services to Skopi and are prohibited from sharing your data with other parties.

## 11. Children's Privacy

Skopi does not knowingly collect information from children under 13. If we become aware that a child under 13 has provided us with personal information, we will promptly delete such information and terminate the child's account.

## 12. Changes to This Privacy Policy

We may update this Privacy Policy periodically to reflect changes in our practices, technology, legal requirements, or other factors. We will notify you of material changes by posting the updated policy on our website and updating the "Last Updated" date.

Your continued use of Skopi following the posting of revised Privacy Policy means you accept and agree to the changes.

## 13. Contact Us

If you have questions about this Privacy Policy or our privacy practices, please contact us at:

<div class="contact-info">
<h3>Skopi Privacy Team</h3>
<p><strong>Email:</strong> <a href="mailto:privacy@skopi.com">privacy@skopi.com</a></p>
</div>

<div class="highlight" style="text-align: center; margin: 40px 0;">
<strong>Your privacy is important to us. Thank you for trusting Skopi with your monitoring needs.</strong>
</div>
````

- [ ] **Step 2: Build the site**

Run: `bundle exec jekyll build`
Expected: Build succeeds; `_site/privacy/index.html` is created.

- [ ] **Step 3: Verify rendered policy content and email**

Run: `grep -o "privacy@skopi.com" _site/privacy/index.html && grep -o "Last Updated: June 21, 2026" _site/privacy/index.html && grep -o "class=\"important\"" _site/privacy/index.html`
Expected: All three print (email intact, last-updated preserved, callout box rendered).

- [ ] **Step 4: Verify all 13 sections rendered**

Run: `grep -c "<h2" _site/privacy/index.html`
Expected: `13`

- [ ] **Step 5: Commit**

```bash
git add privacy.md
git commit -m "feat: add privacy policy page"
```

---

## Final Verification

- [ ] **Run the site locally and eyeball both pages**

Run: `bundle exec jekyll serve` then open `http://localhost:4000/` and `http://localhost:4000/privacy/`
Expected: Landing page shows centered logo + "We are launching…" + tagline on the teal background; privacy page shows the full policy with styled callout boxes and the brand reading "skopi.ai".

- [ ] **Confirm config-driven values work**

Temporarily set `notify_link: "mailto:hello@skopi.com"` in `_config.yml`, rebuild, and confirm a "Notify me" button appears on the landing page. Revert to `""` afterward.
