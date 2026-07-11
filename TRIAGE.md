# Pinecone OSS Issue Triage Policy

_Owner: Developer Experience Engineering · Last updated: 2026-07-11_

This policy defines how we respond to and label incoming issues on Pinecone's
public repositories. It exists so contributors get a fast, predictable response
and so maintainers can find the work that matters. It is deliberately
lightweight: a small label set and a short weekly routine.

## Scope

Applies to all public, non-archived repositories in the `pinecone-io` org.
**Tier 1 (highest developer impact — triaged to SLA):**

| Repo | Surface |
|------|---------|
| [`examples`](https://github.com/pinecone-io/examples) | Notebooks & demo apps |
| [`python-sdk`](https://github.com/pinecone-io/python-sdk) | Python SDK |
| [`pinecone-ts-client`](https://github.com/pinecone-io/pinecone-ts-client) | TypeScript SDK |
| [`go-pinecone`](https://github.com/pinecone-io/go-pinecone) | Go SDK |
| [`pinecone-mcp`](https://github.com/pinecone-io/pinecone-mcp) | MCP server |
| [`cli`](https://github.com/pinecone-io/cli) | Pinecone CLI |

Other public repos follow the same scheme on a best-effort basis.

## Service-level targets (SLA)

Business days, measured from issue creation (or the reporter's last reply for
follow-ups). These are response targets, not resolution promises.

| Target | Tier 1 | Other public repos |
|--------|--------|--------------------|
| **First response** (human ack + `status:*` label) | 2 business days | 5 business days |
| **Type + status label applied** | 2 business days | 5 business days |
| **Security reports** — acknowledge & assess | **1 business day** | 1 business day |

Security fixes ship under the separate [fast-patch process](#security). A security
report never waits for the weekly routine — page the on-call maintainer.

"First response" = a maintainer has acknowledged the issue and it carries a
`status:*` label. It does **not** require a fix, a root cause, or a schedule.

## Labelling scheme

Every open issue must carry **one type** label and **one status** label after
first triage. Priority is optional at first pass and is set by the owning team
when the issue is scheduled. The scheme is uniform across all Tier 1 repos.

### Type — what kind of issue is this? (exactly one)

| Label | Meaning |
|-------|---------|
| `bug` | Something is broken or behaves incorrectly |
| `enhancement` | Feature request or improvement |
| `documentation` | Docs/README/example content error or gap |
| `question` | Usage question or support request |
| `security` | Vulnerability or hardening — routes to the fast-patch process |

Housekeeping types `duplicate`, `invalid`, and `wontfix` may replace the above
once a disposition is reached.

### Status — where is it in the pipeline? (exactly one)

| Label | Meaning |
|-------|---------|
| `status:needs-triage` | Landed, not yet reviewed by a maintainer (default on entry) |
| `status:needs-more-info` | Waiting on the reporter for details or a repro |
| `status:backlog` | Triaged and accepted; not yet scheduled |
| `status:on-roadmap` | Triaged and scheduled for work |
| `blocked` | Cannot proceed; blocked on an external dependency |

### Priority — how urgent? (optional; set by owning team when scheduling)

| Label | Meaning |
|-------|---------|
| `priority:p0` | Critical: outage / security / data loss — drop everything |
| `priority:p1` | High: broken core functionality, no workaround |
| `priority:p2` | Medium: important but has a workaround |
| `priority:p3` | Low: minor, cosmetic, or nice-to-have |

### Community & area

`good first issue` and `help wanted` flag community-friendly work. Language/area
labels (`python`, `javascript`, `go`, `rust`, `dependencies`, …) are applied where
useful but are not required for triage to be complete.

## The triage routine

**Weekly, ~30 minutes.** A rotating triage owner runs the pass every Monday.

1. **Find untriaged issues.** For each Tier 1 repo, list open issues without a
   `status:*` label (see the query below).
2. **Label each one.** Assign a type and a status. If it can't be understood or
   reproduced, set `status:needs-more-info` and ask the reporter one specific
   question.
3. **Acknowledge.** Leave a short first-response comment on genuinely new issues
   so the reporter knows a human saw it.
4. **Route security.** Anything security-relevant gets the `security` label and
   is handed to the fast-patch on-call immediately — do not wait for next week.
5. **Escalate blockers.** Anything needing a product/roadmap decision goes to the
   owning team lead.

Daily, the triage owner does a lightweight sweep for new `bug`/`security` issues
so nothing urgent waits a full week.

### Handy queries

```bash
# Untriaged open issues across Tier 1 repos
for r in examples python-sdk pinecone-ts-client go-pinecone pinecone-mcp cli; do
  echo "== $r =="
  gh issue list -R pinecone-io/$r --state open \
    --search 'no:label OR -label:status:needs-triage,status:needs-more-info,status:backlog,status:on-roadmap' \
    --json number,title --jq '.[] | "  #\(.number) \(.title)"'
done

# Aging: issues still needs-triage older than the SLA window
gh issue list -R pinecone-io/<repo> --state open \
  --label status:needs-triage --search 'created:<2026-07-09' \
  --json number,title,createdAt
```

## Label bootstrap

New repos inherit the scheme with this idempotent snippet (run per repo):

```bash
REPO=pinecone-io/<repo>
gh label create security               -R $REPO -c b60205 -d "Security vulnerability or hardening" -f
gh label create status:needs-triage    -R $REPO -c fbca04 -d "Not yet reviewed by a maintainer" -f
gh label create status:needs-more-info -R $REPO -c d4c5f9 -d "Waiting on reporter" -f
gh label create status:backlog         -R $REPO -c c5def5 -d "Accepted; not scheduled" -f
gh label create status:on-roadmap      -R $REPO -c 0e8a16 -d "Scheduled for work" -f
gh label create priority:p0            -R $REPO -c b60205 -d "Critical" -f
gh label create priority:p1            -R $REPO -c d93f0b -d "High" -f
gh label create priority:p2            -R $REPO -c fbca04 -d "Medium" -f
gh label create priority:p3            -R $REPO -c c2e0c6 -d "Low" -f
```

## <a name="security"></a>Security

Security issues are the top priority and never wait for the weekly routine.
Report privately via GitHub Security Advisories on the affected repo (or the
address in that repo's `SECURITY.md`). They are acknowledged within 1 business
day and ship under the fast-patch process. Do not disclose details in a public
issue before a fix is released.
