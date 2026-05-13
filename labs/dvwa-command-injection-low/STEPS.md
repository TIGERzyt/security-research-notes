# DVWA Command Injection (Low) — Reproduction Steps

Captures `01-baseline.png` through `03-passwd-read.png`. Full write-up
is in [`../../notes/04-command-injection-fundamentals.md`](../../notes/04-command-injection-fundamentals.md).

## Pre-flight

- DVWA running: `docker run --rm -it -p 8080:80 vulnerables/web-dvwa`
- Logged in.
- DVWA Security Level: **Low**.

## Step 1 — Baseline

1. Left nav → **Command Injection**
   (URL: `http://localhost:8080/vulnerabilities/exec/`).
2. Input: `127.0.0.1`.
3. Click **Submit**.
4. The page shows standard `ping` output: 4 packets sent / received.
5. **Screenshot 01** — capture the full page showing the ping output.
   Save as `01-baseline.png`.

## Step 2 — Chain `id`

6. Clear input. Payload: `127.0.0.1; id`.
7. Click **Submit**.
8. Page renders ping output followed by:

   ```
   uid=33(www-data) gid=33(www-data) groups=33(www-data)
   ```

9. **Screenshot 02** — capture the page showing both ping output and
   the `id` line. Save as `02-chained-id.png`.

## Step 3 — Read /etc/passwd

10. Clear input. Payload: `127.0.0.1 && cat /etc/passwd`.
11. Click **Submit**.
12. Page renders ping output followed by the contents of `/etc/passwd`.
13. **Screenshot 03** — capture the full page. Save as
    `03-passwd-read.png`.

## Important boundaries

- **Stop at proof of impact.** `id` and `cat /etc/passwd` are textbook
  evidence; do not run destructive commands, do not pull large files,
  do not attempt to pivot to the host. The point of the lab is to
  understand the bug and the defense, not to "win" the box.
- **Don't change the input class.** Stick with the `ip` field; do not
  attempt to retarget at any other host or any other service. The
  container is isolated; keep it that way.

## Cleanup

- Nothing persistent to clean — the container is `--rm` and these
  commands didn't write anywhere. When you're done, `Ctrl+C` in the
  Docker terminal removes the container; the image stays for next time.
