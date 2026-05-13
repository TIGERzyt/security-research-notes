# SQL Injection Fundamentals

> Personal learning notes — Week 1
> Lab environment: DVWA (local Docker)

## What is SQL Injection?

SQL Injection (SQLi) is a vulnerability class where untrusted user input is concatenated directly into SQL queries, allowing an attacker to alter the query's logic. It remains one of the most critical web application vulnerabilities, consistently ranked in the OWASP Top 10.

The root cause is almost always the same pattern: **the application treats user input as part of the query structure rather than as data**.

## A Minimal Vulnerable Example

Consider a login query built by string concatenation:

```sql
SELECT * FROM users WHERE username = '$user' AND password = '$pass';
```

If `$user` comes directly from a form field, an attacker can submit:

```
username: admin' --
password: anything
```

The query becomes:

```sql
SELECT * FROM users WHERE username = 'admin' --' AND password = 'anything';
```

The `--` comments out the rest, and the attacker authenticates as `admin` without knowing the password.

## Main Categories

SQL Injection is typically classified by how the attacker extracts information:

### 1. In-band SQLi

The attacker uses the same channel to inject and receive results.

- **Error-based**: rely on database error messages leaking data
- **UNION-based**: append a `UNION SELECT` to retrieve data from other tables

### 2. Blind SQLi

No direct output is visible. The attacker infers data from application behavior.

- **Boolean-based**: inject conditions and observe true/false page differences
- **Time-based**: use `SLEEP()` or equivalents to measure response delays

### 3. Out-of-band SQLi

The attacker triggers the database to make a network request (DNS, HTTP) to an attacker-controlled server. Useful when no direct or inferential feedback is available.

## Why It Matters for Defenders

Understanding how SQLi works at this level is essential for:

- Writing meaningful test cases when reviewing authentication, search, and reporting features
- Recognizing risky patterns during code review (any string concatenation involving user input near a query)
- Evaluating whether a WAF rule or input validation actually closes the underlying issue, or just masks the symptom

## Defense Principles

The widely-agreed-upon defenses, in order of effectiveness:

1. **Parameterized queries / prepared statements** — the input can never change the query structure
2. **ORM with safe defaults** — most modern ORMs parameterize by default, but raw query escape hatches still exist
3. **Least privilege at the database layer** — the application's DB user should not have permissions it doesn't need (e.g., no `DROP`, no access to unrelated tables)
4. **Input validation** — useful as defense-in-depth, not as the primary mitigation

Input filtering and WAF rules are **not** primary defenses. They're worth having, but a determined attacker will find bypasses.

## Lab Work (To Be Added)

Next session: reproduce DVWA's SQL Injection module (Low, Medium, High difficulty levels), document the technique that bypasses each, and add screenshots here.

## References

- OWASP — SQL Injection Prevention Cheat Sheet
- PortSwigger Web Security Academy — SQL Injection learning path
- DVWA — Damn Vulnerable Web Application (local lab)
