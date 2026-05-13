# DVWA XSS (Low) — Reproduction Steps

These are the exact steps I use to capture screenshots
`01-reflected-baseline.png` through `04-stored-alert.png`. The full
write-up is in [`../../notes/02-xss-fundamentals.md`](../../notes/02-xss-fundamentals.md).

## Pre-flight

- DVWA running: `docker run --rm -it -p 8080:80 vulnerables/web-dvwa`
- Logged in as `admin / password`
- Database initialized (Setup / Reset DB → Create Database)
- **DVWA Security → Security Level: Low → Submit**

## Part A — Reflected XSS

1. Navigate to **Vulnerability: XSS (Reflected)** in the left nav
   (URL: `http://localhost:8080/vulnerabilities/xss_r/`).
2. **Screenshot 01** — type `Alice` in the **What's your name?** box,
   click **Submit**. Capture the full page showing `Hello Alice` in the
   output box. Save as `01-reflected-baseline.png`.
3. Clear the input. Paste payload:
   ```
   <script>alert('XSS-reflected')</script>
   ```
   Click **Submit**. The browser will fire an `alert()` dialog with
   text `XSS-reflected`.
4. **Screenshot 02** — capture the page **while the alert is visible**
   (so the screenshot shows both the DVWA page and the alert popup).
   Save as `02-reflected-alert.png`. Dismiss the alert.

## Part B — Stored XSS

5. Navigate to **Vulnerability: XSS (Stored)** in the left nav
   (URL: `http://localhost:8080/vulnerabilities/xss_s/`).
6. Fill in Name `Alice`, Message `hello world`. Click **Sign Guestbook**.
7. **Screenshot 03** — capture the page showing Alice's benign entry
   appended below the default guestbook entry. Save as
   `03-stored-baseline.png`.
8. The Message field has `maxlength="50"` (client-side). Either:
   - Open DevTools (F12) → Elements → find the `<textarea name="mtxMessage">` → remove the `maxlength` attribute, OR
   - Use `curl`:
     ```bash
     curl -X POST http://localhost:8080/vulnerabilities/xss_s/ \
       -b "PHPSESSID=<your-session-id>; security=low" \
       --data "txtName=evil&mtxMessage=<script>alert('XSS-stored')</script>&btnSign=Sign+Guestbook"
     ```
     (Grab `PHPSESSID` from DevTools → Application → Cookies.)
9. Submit. The page reloads; the `<script>` payload executes and the
   `alert('XSS-stored')` popup fires.
10. **Screenshot 04** — capture the page **while the alert is visible**.
    Save as `04-stored-alert.png`.

## Cleanup

- Click **Setup / Reset DB → Create / Reset Database** to wipe the
  stored payload before moving on to the next lab. (Stored XSS is
  *persistent* — leaving it in place will cause every subsequent visit
  to that page to fire the alert.)

## Common gotchas

- If the script doesn't fire, double-check that Security Level is
  **Low** — at Medium / High DVWA applies a filter.
- Browser XSS auditors are mostly gone in modern Chrome/Edge/Firefox,
  but if you've installed something like NoScript or uBlock with
  aggressive rules, the reflected payload may be blocked on the
  *browser* side. That's a useful defense-in-depth observation but
  should not be confused with the application being patched.
