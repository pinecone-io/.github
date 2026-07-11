# Security Fast-Patch Process

**Owner:** Founding Developer Experience Engineer (security quality owner)
**Applies to:** all public repositories in the [`pinecone-io`](https://github.com/pinecone-io) org
**Status:** active — v1 (2026-07-11)

Security fixes ship ahead of everything else. This document defines how we detect,
patch, release, and communicate security fixes, and the time-to-ship targets we
hold ourselves to.

---

## 1. Time-to-ship targets (SLA)

Measured from **confirmation** (we have reproduced/validated the issue and agreed
on severity) to a **fixed version published** to the relevant registry
(PyPI / npm / Go module proxy / Homebrew / GitHub Release).

| Severity (CVSS v3.1) | Triage start | Fix released | Advisory published |
| --- | --- | --- | --- |
| **Critical** (9.0–10.0) | immediately, same day | **≤ 3 business days** | with the fix |
| **High** (7.0–8.9) | ≤ 1 business day | **≤ 7 business days** | with the fix |
| **Medium** (4.0–6.9) | ≤ 2 business days | **next scheduled release, ≤ 30 days** | at maintainer discretion |
| **Low** (0.1–3.9) | ≤ 5 business days | next scheduled release | optional |

A dependency advisory inherits the severity of its impact on our software, not
the upstream CVSS score in isolation (a critical CVE in an unreachable code path
may be High or lower for us).

If a target cannot be met, the security owner escalates to the CEO **before** the
deadline with the reason and a revised date.

---

## 2. Detect

Monitoring enabled on every public repo:

- **Dependabot vulnerability alerts** — advisory feed for vulnerable dependencies.
  Enabled org-wide (default for public repos).
- **Dependabot security updates** — automatically opens patch PRs for vulnerable
  dependencies. Enabled on the highest-impact repos (SDKs, CLI, MCP, examples).
- **Dependabot version updates** — scheduled dependency bumps
  (`.github/dependabot.yml`) so we are rarely far behind a patched release. Rolled
  out to the SDKs, CLI, and MCP server.
- **GitHub Private Vulnerability Reporting** — external researchers report through
  the repo Security tab; see [`SECURITY.md`](../SECURITY.md).
- **Secret scanning + push protection** — enabled org-wide to catch leaked
  credentials.

**Highest-impact repos** (patched first, monitored most closely): `python-sdk`,
`pinecone-ts-client`, `go-pinecone`, `cli`, `pinecone-mcp`. These run inside
customer environments and CI and handle API keys.

Triggers that start this process: a Dependabot/GitHub advisory, an external report
via `SECURITY.md`, an upstream CVE in a dependency we ship, or an internally
discovered vulnerability.

---

## 3. Triage

1. **Acknowledge** the report within the SLA above.
2. **Reproduce / confirm** and determine the affected versions and code paths.
3. **Score severity** with CVSS v3.1, adjusted for real-world reachability in our
   software.
4. **Open a private tracking issue** (or GitHub Security Advisory draft) — never a
   public issue for an unfixed vulnerability.
5. For a **critical** issue, notify the CEO immediately (may require coordinated
   disclosure).

---

## 4. Patch

- Develop the fix on a **private fork / draft security advisory** for
  externally-reported or embargoed issues; for already-public dependency
  advisories, a normal PR is fine.
- Add a **regression test** that fails before the fix and passes after.
- Keep the diff minimal and targeted — security patches are not the place for
  refactors.
- Backport to supported release lines if the vulnerability affects them.
- Fix requires review by a second maintainer before release. Reviews for
  security fixes are expedited, not skipped.

---

## 5. Release

1. Merge the fix and cut a **patch release** (do not batch it with unrelated work).
2. Publish to the registry (PyPI / npm / Go proxy / Homebrew tap / GitHub Release).
3. Tag the release and write release notes that state a security fix is included
   and the severity, without disclosing exploit detail before users can upgrade.
4. Yank / deprecate affected versions where the registry supports it and it helps
   users.

---

## 6. Notify

- **Publish a GitHub Security Advisory (GHSA)** for the repo and request a CVE
  where warranted. Credit the reporter unless they opt out.
- **Release notes + CHANGELOG** entry describing the fix and the minimum safe
  version.
- **Escalate to the CEO** for anything requiring customer communication or
  coordinated public disclosure. External announcements need CEO sign-off.
- Update this document if the process changed.

---

## 7. Roles

| Role | Responsibility |
| --- | --- |
| **Security owner** (Founding DX Engineer) | Drives detect→notify end to end, owns the SLA |
| **Repo maintainer** | Reviews and releases the fix for their repo |
| **CEO** | Sign-off on disclosure, customer comms, and any external announcement |

---

## 8. Monitoring baseline checklist (per repo)

- [ ] Dependabot vulnerability alerts enabled
- [ ] Dependabot security updates (auto patch PRs) enabled
- [ ] `.github/dependabot.yml` with version updates for every ecosystem in the repo
- [ ] Secret scanning + push protection enabled
- [ ] `SECURITY.md` present (inherited from org `.github` repo by default)
- [ ] Private Vulnerability Reporting enabled

Current rollout status is tracked in issue **PIN-4** and its child issues.
