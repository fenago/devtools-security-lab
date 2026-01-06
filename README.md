# DevTools Security Lab

A hands-on learning environment for mastering Chrome DevTools and understanding frontend security vulnerabilities.

## Live Demo

Once deployed, your pages will be available at:
- **Demo App:** `https://YOUR-USERNAME.github.io/devtools-security-lab/vulnerable-demo.html`
- **Walkthrough:** `https://YOUR-USERNAME.github.io/devtools-security-lab/WALKTHROUGH.html`

## What's Included

| File | Description |
|------|-------------|
| `vulnerable-demo.html` | A fake "SecureChat Pro" app loaded with 15+ intentionally exposed secrets |
| `WALKTHROUGH.md` | Step-by-step guide to finding every hidden secret using DevTools |
| `index.html` | Landing page linking to both resources |

## Deployment to GitHub Pages

### Step 1: Create a New Repository

1. Go to [github.com/new](https://github.com/new)
2. Name your repository `devtools-security-lab`
3. Set visibility to **Public** (required for free GitHub Pages)
4. Check "Add a README file" (we will replace it)
5. Click **Create repository**

### Step 2: Upload the Files

**Option A: Using GitHub Web Interface**

1. In your new repository, click **Add file** > **Upload files**
2. Drag and drop these files:
   - `index.html`
   - `vulnerable-demo.html`
   - `WALKTHROUGH.md`
3. Add commit message: "Add DevTools security lab files"
4. Click **Commit changes**

**Option B: Using Git Command Line**

```bash
# Clone your new repository
git clone https://github.com/YOUR-USERNAME/devtools-security-lab.git
cd devtools-security-lab

# Copy the files into this folder, then:
git add .
git commit -m "Add DevTools security lab files"
git push origin main
```

### Step 3: Enable GitHub Pages

1. Go to your repository on GitHub
2. Click **Settings** (tab at the top)
3. Scroll down to **Pages** in the left sidebar (under "Code and automation")
4. Under "Source", select **Deploy from a branch**
5. Under "Branch", select **main** and **/ (root)**
6. Click **Save**

### Step 4: Wait for Deployment

1. GitHub will build your site (usually takes 1-2 minutes)
2. Refresh the Settings > Pages section
3. You will see a green box with your live URL:
   ```
   Your site is live at https://YOUR-USERNAME.github.io/devtools-security-lab/
   ```

### Step 5: Share Your Links

Your demo is now live at these URLs:

- **Landing Page:** `https://YOUR-USERNAME.github.io/devtools-security-lab/`
- **Vulnerable Demo:** `https://YOUR-USERNAME.github.io/devtools-security-lab/vulnerable-demo.html`
- **Walkthrough Guide:** `https://YOUR-USERNAME.github.io/devtools-security-lab/WALKTHROUGH.html`

## For Article Readers

If you are here from the Medium article, start with the walkthrough:

1. Open [vulnerable-demo.html](./vulnerable-demo.html) in Chrome
2. Follow the [WALKTHROUGH.md](./WALKTHROUGH.md) to find all 15+ hidden secrets
3. Practice using every DevTools panel covered in the article

## Secrets Cheat Sheet

For instructors or those who want to verify they found everything:

| Category | Count | Examples |
|----------|-------|----------|
| HTML Comments | 8 | Firebase key, admin password, SSH paths, S3 credentials |
| Data Attributes | 3 | Admin key, debug token, feature flags |
| JavaScript ENV | 25+ | OpenAI, Stripe, AWS, Twilio, MongoDB, Redis, etc. |
| Base64 Encoded | 4 | Admin password, master key, backdoor flag, secret endpoint |
| ROT13 Encoded | 3 | Three username/password pairs |
| Console Functions | 3 | revealSecrets(), getConfig(), debugMode() |
| localStorage | 4 | JWT token, API backup, customer ID, preferences |
| sessionStorage | 2 | Temp auth, debug info |
| Network Requests | 5+ | Authorization headers, API keys in payloads |
| Easter Eggs | 1 | Konami code unlocks admin panel |

## Educational Purpose

**All credentials in this demo are completely fake.** They follow realistic patterns to help learners recognize exposed secrets in real applications.

This lab teaches:
- How to use Chrome DevTools effectively
- Why frontend code cannot hide secrets
- Common mistakes developers make with API keys
- Proper architecture for keeping secrets secure

## License

MIT License - Use freely for education and training.

## Author

Created by [Dr. Ernesto Lee](https://drlee.io) as a companion to the Medium article on frontend security.
