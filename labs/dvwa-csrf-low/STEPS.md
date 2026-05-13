# DVWA CSRF (Low) — Reproduction Steps

These are the exact steps I use to capture screenshots
`01-exploit-page.png` and `02-password-changed.png`. The full write-up is
in [`../../notes/03-csrf-fundamentals.md`](../../notes/03-csrf-fundamentals.md).

## Pre-flight

- DVWA running: `docker run --rm -it -p 8080:80 vulnerables/web-dvwa`
- Logged in as `admin / password` in **Tab A**.
- DVWA Security Level set to **Low**.

## Step 1 — Create the exploit page

Save the following as `attacker.html` somewhere on your local disk
(e.g., `C:\Users\30293\Desktop\cc\CVP申请\github-content\labs\dvwa-csrf-low\attacker.html` —
it's checked into this repo as a working artifact):

```html
<!doctype html>
<html>
  <body>
    <h1>Cute Cat Pictures</h1>
    <img src="http://localhost:8080/vulnerabilities/csrf/?password_new=pwned&password_conf=pwned&Change=Change"
         width="1" height="1" alt="">
    <p>(Nothing to see here.)</p>
  </body>
</html>
```

## Step 2 — Trigger

In the **same browser window** (so the DVWA session cookie is in
scope), open a **new tab** (Tab B) and load `attacker.html` via
`file:///` or by double-clicking it from File Explorer.

The page loads. Visually it looks like a single h1 — but the `<img>`
fires the GET to DVWA in the background.

## Step 3 — Confirm

1. **Screenshot 01** — capture Tab B showing `attacker.html` (the
   innocuous "Cute Cat Pictures" page). Save as
   `01-exploit-page.png`. Open DevTools → Network and capture the
   network log showing the GET to `localhost:8080/vulnerabilities/csrf/`
   succeeded.
2. Return to Tab A → DVWA CSRF page. You'll see DVWA's confirmation
   message: *"Password Changed."* (DVWA's response is just text on the
   same page, so reloading the page or re-submitting any change will
   show this state).
3. **Screenshot 02** — capture the DVWA page state confirming the
   password was changed. Save as `02-password-changed.png`.
4. To verify it actually worked: log out (top-right of DVWA), then try
   to log in as `admin / password` — it will fail. Log in as `admin /
   pwned` — it succeeds. (Optional bonus screenshot.)

## Cleanup

- Log back in with `admin / pwned`.
- Go to **Vulnerability: CSRF** and use the legitimate form to change
  the password back to `password` so DVWA is in a known state for the
  next lab.

## What to note in the write-up

- The exploit page is plain HTML on `file:///` — no JavaScript, no
  network of its own, just an `<img>` tag. This is the minimum
  possible CSRF.
- The browser attaches the DVWA `PHPSESSID` cookie automatically
  because the request is to the cookie's origin.
- There is no anti-CSRF token in the URL; that's the bug.
- This is also a `GET` request performing a state change, which
  doubles the badness.
