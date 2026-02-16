---
id: rule-security-001
kind: rule
version: 1
applies_to: [ "src/**" ]
---

# Security Policy

---

## Authentication & Tokens

- Moodle WS tokens stored **only** via `CoreSite` internal storage (SQLite)
- Never log, print, or expose WS tokens in UI, logs, or error messages
- Token refresh handled by `CoreSite` — never implement custom token logic
- SSO: use `CoreLoginHelper` — never implement OAuth/SAML manually

---

## Data Storage

| Data             | Allowed                                     | Prohibited                    |
|------------------|---------------------------------------------|-------------------------------|
| User credentials | `CoreSite` (encrypted SQLite)               | `localStorage`, plain files   |
| Preferences      | `CoreConfig` (Cordova Preferences)          | Cookies, `sessionStorage`     |
| Files            | `CoreFile` (Cordova File plugin, sandboxed) | External URIs written to disk |
| PII              | Never stored locally                        | —                             |

---

## Network & API

- All Moodle API calls over **HTTPS only**
- Never pass raw user input into WS params without validation
- URL validation before `CoreSite.checkSite()`: use `CoreUrlUtils.isValidMoodleUrl()`

---

## WebView & XSS

- HTML from Moodle content rendered only via `CoreFormatTextDirective`
- User-generated content must pass through `CoreTextUtils.filterHtml()` before display
- Never use `element.innerHTML = userContent`
- Never use `eval()` or `new Function()`
- External links opened via `CoreOpenerUtils.openInBrowser()` — never `window.open()`

---

## Cordova-Specific

- Request native permissions only when needed, with rationale shown before system dialog
- iOS: update `NSPhotoLibraryUsageDescription` and similar in `config.xml` / `Info.plist`
- Android: permissions declared in `AndroidManifest.xml` via `config.xml`
- `google-services.json` and `GoogleService-Info.plist` are **not committed** — injected by CI

---

## Dependency Security

- Run `npm audit` before adding any new package
- No packages with GPL license (incompatible with app stores)
- New Cordova plugins must have active maintenance and no critical CVEs
- `package-lock.json` must be committed — never gitignored

---

## Prohibited

```typescript
element.innerHTML = userContent       // XSS
localStorage.setItem('token', token)  // insecure storage
console.log('token:', site.token)     // token leak in logs
fetch('http://...')                   // HTTP, not HTTPS
eval(dynamicCode)                     // code injection
window.open(url)                      // use CoreOpenerUtils.openInBrowser()
```
