# DevTools Treasure Hunt: Uncover 15+ Hidden Secrets

Welcome to the hands-on companion for the article **"Your API Key Is Public: How Chrome DevTools Exposes Frontend Secrets in 30 Seconds"**

This walkthrough teaches you to use Chrome DevTools by hunting for intentionally exposed secrets in a demo application. Every vulnerability you find here exists in real production applications across the web.

**Demo Page:** [vulnerable-demo.html](./vulnerable-demo.html)

---

## Before You Start

1. Open the [vulnerable-demo.html](./vulnerable-demo.html) page in Google Chrome
2. Right-click anywhere and select "Inspect" (or press `Ctrl+Shift+I` on Windows, `Cmd+Option+I` on Mac)
3. Follow each step below to discover hidden secrets

---

## Step 1: Check HTML Comments (Elements Panel)

**What to do:**
- Click the **Elements** tab in DevTools
- Look at the very top of the HTML inside the `<head>` tag
- Scroll to the very bottom, just before the closing `</html>` tag

**What you will find:**

In the head section:
```html
<!-- TODO: Remove before production - Config for staging environment -->
<!-- FIREBASE_CONFIG: AIzaSyDOCAbC123dEf456GhI789jKl_MnsT321_rwXyz -->
<!-- ADMIN_PASSWORD: SuperSecure2024! -->
```

At the bottom:
```
Staging Server: staging.securechat.internal
SSH Key Location: /home/deploy/.ssh/id_rsa
S3 Access Key: AKIAIOSFODNN7EXAMPLE
Grafana Login: admin / Grafana2024!
GitHub Deploy Token: ghp_DeployToken123456789abcdefghijklmn
```

**Lesson:** HTML comments ship to every user. They are not private.

---

## Step 2: Inspect Hidden Elements (Elements Panel)

**What to do:**
- Press `Ctrl+F` (or `Cmd+F`) to open search in the Elements panel
- Search for `hidden-admin-panel`
- Click on the div to select it
- Look at its attributes in the panel

**What you will find:**
```
data-admin-key="sk-admin-Xk9Lm3Np7Qr2St5Uv8Wx1Yz4Ab6Cd9Ef"
data-debug-token="dbg_tk_2024_internal_use_only"
data-feature-flags="beta,experimental,admin-override"
```

**Lesson:** `display: none` hides elements visually but not from the DOM. Data attributes are fully visible.

---

## Step 3: Global Search for API Keys (Sources Panel)

**What to do:**
- Click the **Sources** tab
- Press `Ctrl+Shift+F` (or `Cmd+Option+F`) to open global search
- Search for `sk-` (common OpenAI key prefix)

**What you will find:**
```
OPENAI_API_KEY: "sk-proj-Abc123Def456Ghi789Jkl012Mno345Pqr678Stu901Vwx234Yz"
```

**Try these other searches:**
- `sk_live` (finds Stripe secret key)
- `password` (finds multiple passwords)
- `secret` (finds JWT secret, internal API secret)
- `mongodb` (finds database connection string with credentials)
- `AKIA` (finds AWS access key)

**Lesson:** Bundled JavaScript is fully searchable. Minification does not hide values.

---

## Step 4: Find the Complete ENV Object (Sources Panel)

**What to do:**
- In the Sources tab, expand the file tree on the left
- Click on the HTML file to view its source
- Scroll to find the `const ENV = {` section

**What you will find:**

25+ exposed credentials including:
- OpenAI API key and org ID
- Stripe publishable AND secret keys
- Firebase configuration
- MongoDB connection string with username/password
- Redis URL with password
- AWS access key and secret key
- Twilio account SID and auth token
- SendGrid API key
- GitHub personal access token
- Slack bot token
- JWT signing secret
- Internal encryption key

**Lesson:** Environment variables bundled at build time become static strings in your JavaScript.

---

## Step 5: Decode the "Obfuscated" Config (Console)

**What to do:**
- Click the **Console** tab
- Look at the startup messages already printed
- Type this command and press Enter:

```javascript
atob("eyJhZG1pbl9wYXNzd29yZCI6IkFkbWluQDEyMyIsIm1hc3Rlcl9rZXkiOiJtc3RyX2tleV8yMDI0X3VsdHJhX3NlY3JldCIsImJhY2tkb29yX2VuYWJsZWQiOnRydWUsInNlY3JldF9lbmRwb2ludCI6Ii9hcGkvdjEvYWRtaW4vc2VjcmV0In0=")
```

**What you will find:**
```json
{
  "admin_password": "Admin@123",
  "master_key": "mstr_key_2024_ultra_secret",
  "backdoor_enabled": true,
  "secret_endpoint": "/api/v1/admin/secret"
}
```

**Lesson:** Base64 is encoding, not encryption. Anyone can decode it instantly.

---

## Step 6: Crack the "Encrypted" Users (Console)

**What to do:**
- In the Console, type:

```javascript
rot13("hfre1:cnffjbeq123,hfre2:frpher456,nqzva:FhcreNqzva2024!")
```

**What you will find:**
```
user1:password123,user2:secure456,admin:SuperAdmin2024!
```

**Lesson:** ROT13 and simple ciphers provide zero security. Real encryption requires proper cryptographic libraries.

---

## Step 7: Use the Hidden Console Commands (Console)

**What to do:**
- In the Console, type each command:

```javascript
revealSecrets()
```
This dumps all API keys, decoded configs, and decrypted users.

```javascript
getConfig()
```
This returns the complete ENV object. Expand it to see all values.

```javascript
debugMode()
```
This shows a formatted table of all config plus localStorage and sessionStorage contents.

**Lesson:** Developers often leave debug functions in production code. Check the global scope.

---

## Step 8: Inspect Browser Storage (Application Panel)

**What to do:**
- Click the **Application** tab
- Expand "Local Storage" in the left sidebar
- Click on your file's origin

**What you will find:**
- `user_auth_token`: A JWT token (decodable)
- `api_key_backup`: The OpenAI API key again
- `stripe_customer_id`: Customer identifier
- `user_preferences`: JSON with access level set to "admin"

**Now check Session Storage:**
- Expand "Session Storage"
- Click on your file's origin

**What you will find:**
- `temp_auth`: Temporary authentication token
- `debug_info`: JSON with secret endpoint path

**Lesson:** Browser storage is not secure storage. Anyone can read it.

---

## Step 9: Decode the JWT Token (External Tool)

**What to do:**
- Copy the `user_auth_token` value from localStorage
- Go to [https://jwt.io](https://jwt.io)
- Paste the token in the "Encoded" field

**What you will find:**
```json
{
  "userId": "12345",
  "email": "admin@securechat.com",
  "role": "admin",
  "iat": 1709512000,
  "exp": 1741048000
}
```

**Lesson:** JWT tokens are encoded, not encrypted. The payload is readable by anyone. Only the signature provides integrity.

---

## Step 10: Watch Network Requests (Network Panel)

**What to do:**
- Click the **Network** tab
- Refresh the page to capture all requests
- Wait 1-2 seconds for the delayed requests to fire
- Click on the request to `httpbin.org/headers`
- Click the "Headers" tab in the detail panel

**What you will find in Request Headers:**
```
Authorization: Bearer sk-proj-Abc123Def456Ghi789Jkl012Mno345Pqr678Stu901Vwx234Yz
X-API-Key: int_sec_Qm8Kp3Lx7Nv2Ry5Tw9Uz
X-Firebase-Key: AIzaSyDOCAbC123dEf456GhI789jKl
```

**Now check the POST request:**
- Click on the request to `httpbin.org/post`
- Click the "Payload" tab

**What you will find:**
```json
{
  "event": "page_load",
  "session": "sess_...",
  "stripe_key": "pk_live_51ABC123DEF456GHI789jkl"
}
```

**Lesson:** Every request header and body is visible in DevTools. API keys sent from the browser are exposed.

---

## Step 11: Try the Konami Code Easter Egg

**What to do:**
- Click somewhere on the page (not in DevTools)
- Type this sequence on your keyboard:
  - Up, Up, Down, Down, Left, Right, Left, Right, B, A

**What happens:**
- An alert appears welcoming you as Admin
- The hidden admin panel becomes visible
- The Console shows the decoded admin credentials

**Lesson:** Easter eggs in production code can expose sensitive functionality. Always review what ships.

---

## Step 12: Check for Inline Event Handlers (Elements Panel)

**What to do:**
- In the Elements panel, find the Send button
- Look at its attributes

**What you will find:**
```
onclick="sendMessage()"
```

- Now search for `sendMessage` in Sources to see the full function
- Notice it constructs API calls with exposed keys

**Lesson:** Inline handlers point you to JavaScript functions worth investigating.

---

## Step 13: Review Console Startup Messages (Console)

**What to do:**
- Look at the Console output that appeared when the page loaded

**What you will find:**
```
SecureChat Pro v2.3.1
Debug Mode: ENABLED
Initialized with session: sess_...
API Endpoint: https://api.openai.com/v1
Tip: Type revealSecrets() to see all configuration
```

**Lesson:** Console logs in production reveal application internals and sometimes hint at debug features.

---

## Step 14: Attempt to Send a Message (Network Panel)

**What to do:**
- Keep the Network tab open
- Type a message in the chat input
- Click Send
- Watch the Network tab for a new request to `api.openai.com`

**What you will find:**

The request will fail (invalid key), but you can see:
- The exact endpoint being called
- The Authorization header with the API key
- The request body structure
- The organization ID

**Lesson:** Even failed requests expose your API structure and credentials.

---

## Summary: What We Found

| Location | Secrets Discovered |
|----------|-------------------|
| HTML Comments | Firebase key, admin password, deployment notes, SSH paths, S3 keys, Grafana login |
| Data Attributes | Admin key, debug token, feature flags |
| JavaScript ENV | 25+ API keys (OpenAI, Stripe, AWS, Twilio, etc.) |
| Base64 Config | Admin password, master key, backdoor endpoint |
| ROT13 String | Three username/password pairs |
| Console Functions | Full config dump, debug mode |
| localStorage | JWT token, API key backup, user preferences |
| sessionStorage | Temp auth, debug endpoints |
| Network Headers | API keys in Authorization and custom headers |
| Network Payload | Analytics data with Stripe key |

---

## The Fix

None of these secrets should ever appear in frontend code. The correct architecture:

1. Store all secrets on your backend server
2. Frontend calls YOUR backend API
3. Backend adds authentication and calls third-party APIs
4. Responses flow back through your backend

Services like Vercel Edge Functions, Cloudflare Workers, or a simple Express server can act as this secure proxy layer.

---

## About This Demo

This demo was created as a companion to the Medium article on frontend security and Chrome DevTools mastery.

**All credentials in this demo are fake.** They follow realistic patterns to help you recognize exposed secrets in real applications.

Created by [Dr. Ernesto Lee](https://drlee.io)

---

## Quick DevTools Reference

| Panel | Primary Use |
|-------|-------------|
| Elements | Inspect HTML/CSS, find hidden elements, check data attributes |
| Console | View errors, run JavaScript, access global objects |
| Sources | Search code, set breakpoints, debug step-by-step |
| Network | Monitor requests, inspect headers and payloads |
| Application | View cookies, localStorage, sessionStorage, service workers |
| Performance | Profile load times, find bottlenecks |
| Lighthouse | Automated audits for performance, accessibility, SEO |
| Recorder | Record and replay user flows |

---

Happy hunting!
