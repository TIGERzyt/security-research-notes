# DVWA SQL Injection (Low) — Reproduction Steps

Reference for `01-normal-query.png` / `02-boolean-bypass.png` /
`03-union-extraction.png` (already captured 2026-05-13). Full write-up is
in [`../../notes/01-sql-injection-fundamentals.md`](../../notes/01-sql-injection-fundamentals.md).

## Pre-flight

- DVWA running: `docker run --rm -it -p 8080:80 vulnerables/web-dvwa`
- Logged in as `admin / password`.
- Database initialized (Setup / Reset DB).
- DVWA Security Level: **Low**.

## Step 1 — Baseline

1. Left nav → **SQL Injection**
   (URL: `http://localhost:8080/vulnerabilities/sqli/`).
2. Enter `1` in the User ID box. **Submit**.
3. Page shows one row: `ID: 1 / First name: admin / Surname: admin`.
4. **Screenshot** → `01-normal-query.png`.

## Step 2 — Tautology

5. Enter payload: `1' OR '1'='1`.
6. **Submit**.
7. Page shows all 5 DVWA users.
8. **Screenshot** → `02-boolean-bypass.png`.

## Step 3 — UNION-based extraction

9. Enter payload: `' UNION SELECT user, password FROM users #`.
10. **Submit**.
11. Page shows 5 rows, each `ID = ` (literal), with `First name = user`
    and `Surname = password hash`.
12. **Screenshot** → `03-union-extraction.png`.

## Comment-character gotcha

- The classic MySQL comment `--` requires a trailing whitespace in
  MariaDB; HTML form `<input>` trims trailing whitespace before
  submitting, which silently strips the space and breaks the payload.
- Workaround: use `#` (MySQL's other single-line comment marker), which
  has no whitespace requirement.
- This is the kind of detail that's invisible in the textbook write-up
  but trips up real reproduction — worth recording.

## Cleanup

- The `users` table is part of the bundled DVWA database — nothing was
  modified, only read. No cleanup needed.
- `Ctrl+C` in the Docker terminal stops and removes the container
  (`--rm`); the image stays cached for next launch.
