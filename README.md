# security-research-notes

Personal research notes by **TIGERzyt** — an independent security
researcher focused on web application and API vulnerabilities.

This repository is a working notebook, not a polished publication. Each
entry pairs a short defensive write-up of a vulnerability class with a
reproducible lab section against a local, intentionally-vulnerable
target. Notes are revised as my understanding deepens.

---

## About me

- Independent security researcher, based in China.
- Focus: web application and API security — OWASP Top 10, authentication
  / authorization flaws, business logic, information disclosure.
- Active platforms:
  - [Bugcrowd](https://bugcrowd.com/TIGERzyt) — identity-verified
    researcher (Persona-verified, May 2026). Public profile activates
    after first accepted submission, per Bugcrowd policy.
  - [补天 (butian.net)](https://www.butian.net/user/TIGERzyt) —
    real-name verified.
- Contact: same email across GitHub, Bugcrowd, 补天, and my Claude
  account, to give reviewers a single identity to verify against.

## Methodology and authorization

I only test systems I am explicitly authorized to test. In practice
this means:

1. **Self-hosted lab environments** that I run on my own laptop —
   DVWA, Vulhub, and similar published vulnerable apps. These are
   purpose-built for hands-on learning and are the only systems
   exercised in the `labs/` write-ups in this repo.
2. **Public bug bounty programs** I am enrolled on, strictly within
   each program's published scope and rules of engagement.

I do **not** conduct testing against any third-party system without
written authorization. The notes here are framed for *defenders* —
the goal of each write-up is to be able to explain to a developer the
one change in the code that makes a class of vulnerability impossible.

## Lab environment

All lab reproductions in this repo use:

- **Host**: Windows 11 + Docker Desktop (WSL 2 backend)
- **Target**: [`vulnerables/web-dvwa`](https://hub.docker.com/r/vulnerables/web-dvwa)
  on `localhost:8080`
- **Network**: no port forwarding, no reverse proxy — the container is
  reachable from my own machine only
- **Launch**: `docker run --rm -it -p 8080:80 vulnerables/web-dvwa`

Each lab folder contains a `STEPS.md` with the exact reproduction
sequence, plus the screenshots referenced from the corresponding note.

## Repository layout

```
.
├── README.md                         — this file
├── LICENSE                           — MIT
├── notes/                            — defensive write-ups, one per vuln class
│   ├── 01-sql-injection-fundamentals.md
│   ├── 02-xss-fundamentals.md
│   ├── 03-csrf-fundamentals.md
│   └── 04-command-injection-fundamentals.md
└── labs/                             — reproduction artifacts (steps + screenshots)
    ├── dvwa-sql-injection-low/
    ├── dvwa-xss-low/
    ├── dvwa-csrf-low/
    └── dvwa-command-injection-low/
```

## Current notes

| # | Class | Note | Lab |
|---|---|---|---|
| 01 | SQL Injection | [`notes/01-sql-injection-fundamentals.md`](notes/01-sql-injection-fundamentals.md) | DVWA Low — boolean bypass + UNION extraction |
| 02 | Cross-Site Scripting | [`notes/02-xss-fundamentals.md`](notes/02-xss-fundamentals.md) | DVWA Low — Reflected + Stored |
| 03 | Cross-Site Request Forgery | [`notes/03-csrf-fundamentals.md`](notes/03-csrf-fundamentals.md) | DVWA Low — GET-based password change PoC |
| 04 | OS Command Injection | [`notes/04-command-injection-fundamentals.md`](notes/04-command-injection-fundamentals.md) | DVWA Low — shell metacharacter chaining |

## Roadmap (short-term)

- DVWA Medium / High walkthroughs for each of the four classes above,
  showing how each level's added defense is bypassed and what the
  defender *should* have done instead.
- A separate note on Blind SQL injection (boolean + time-based) using
  DVWA's SQL Injection (Blind) page.
- A note on Server-Side Request Forgery (SSRF) using a Vulhub
  environment.
- One CVE walkthrough per class — pick a recent public advisory, read
  the patch diff, write a one-page note linking the original code
  pattern to the class.

## License

MIT — see [LICENSE](LICENSE).
