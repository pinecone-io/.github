# Security Policy

Pinecone takes the security of our SDKs, CLIs, and other public developer tools
seriously. This policy is the default security policy for all repositories in the
[`pinecone-io`](https://github.com/pinecone-io) organization.

## Reporting a vulnerability

**Do not open a public issue for security vulnerabilities.**

Report suspected vulnerabilities through either of these private channels:

- **GitHub private advisory** — on the affected repository, go to the
  **Security** tab → **Report a vulnerability** (GitHub Private Vulnerability
  Reporting is enabled on our public repos).
- **Email** — [security@pinecone.io](mailto:security@pinecone.io).

Please include, where possible:

- The affected repository and version (SDK/CLI version, commit SHA, or release tag).
- A description of the vulnerability and its impact.
- Steps to reproduce, a proof of concept, or affected code paths.
- Any suggested remediation.

## Our commitment

| Stage | Target |
| --- | --- |
| Acknowledge your report | within **1 business day** |
| Initial severity assessment | within **2 business days** |
| Fix released for a **critical** advisory | within **3 business days** of a confirmed fix being available |
| Public disclosure / advisory published | coordinated with you, after a fix ships |

Severity is assessed using [CVSS v3.1](https://www.first.org/cvss/). Time-to-ship
targets for each severity level are defined in our
[Security Fast-Patch Process](./docs/security-fast-patch-process.md).

## Scope

In scope: source code, published packages, and release/build pipelines for the
public repositories in the `pinecone-io` organization (SDKs, CLIs, MCP server,
extensions, and example applications).

Out of scope: the hosted Pinecone service and control/data plane APIs. Report
those to [security@pinecone.io](mailto:security@pinecone.io) or through the
[Pinecone Trust Center](https://www.pinecone.io/security/).

## Safe harbor

We will not pursue legal action against researchers who act in good faith, follow
this policy, avoid privacy violations and service degradation, and give us a
reasonable window to remediate before public disclosure.

## Recognition

We credit reporters in the published advisory and release notes unless you prefer
to remain anonymous.
