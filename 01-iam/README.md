# IAM — Multi-Group Cumulative Permissions

## What this lab shows

A user's effective IAM permissions are the **union** of every group they
belong to — not just their "primary" group. This lab builds a 3-group /
6-user setup where two users sit in two groups each, then proves the union
with IAM Policy Simulator.

## Scenario

| Group | Attached policy | Members |
|---|---|---|
| `Developers` | `AmazonEC2FullAccess` | Alice, Bob, Charles |
| `AuditTeam` | `SecurityAudit` | Charles, David |
| `Operations` | `AmazonS3FullAccess` | David, Edward, Fred |

Charles and David are each in two groups, so they each accumulate permissions
from two policies at once.

## Tagging decision

Each user got a `Department` tag. IAM allows exactly one value per tag key,
so Charles and David — who don't map cleanly to a single department — needed
a deliberate choice.

**Decision:** tag both as `Department: AuditTeam`, since that's the trait
that makes them different from everyone else in the setup, rather than
arbitrarily picking one of their two "home" groups.

**Trade-off to remember for ABAC later:** this tag carries no information
about their *other* group membership. If a future attribute-based policy
grants access to anyone tagged `Department=Developers`, Charles won't match
it even though he also has developer permissions. Tags here are for
search/reporting — they don't drive permissions (that's what groups do).

## Result: Policy Simulator

### Charles (Developers + AuditTeam)

| Action | Result | Why |
|---|---|---|
| `ec2:RunInstances` | ✅ Allowed | `AmazonEC2FullAccess` via Developers |
| `s3:GetBucketAcl` | ✅ Allowed | `SecurityAudit` via AuditTeam |

### David (AuditTeam + Operations)

| Action | Result | Why |
|---|---|---|
| `s3:PutObject` | ✅ Allowed | `AmazonS3FullAccess` via Operations |
| `ec2:DescribeInstances` | ✅ Allowed | `SecurityAudit` via AuditTeam |
| `ec2:RunInstances` | ⛔ Denied | Not a member of Developers |

## Lesson learned: `SecurityAudit` does not grant `s3:GetObject`

The original test plan assumed Charles would get `s3:GetObject` Allowed
through `SecurityAudit`. Testing it live in Policy Simulator returned
**Denied**. Checking the AWS-managed policy directly confirmed why:
`SecurityAudit` grants read access to *security configuration metadata*
(bucket ACLs, bucket policies, encryption settings, tags, etc.) — not the
actual object data inside a bucket. `s3:GetObject` reads content, which is a
different permission entirely.

The test was corrected to `s3:GetBucketAcl`, which the policy does grant.
Takeaway: don't infer what a managed policy grants from its name — check it,
or test it.

## Screenshots

| File | Shows |
|---|---|
| `screenshots/01-groups-overview.png` | 3 groups with correct member counts |
| `screenshots/02-charles-groups-membership.png` | Charles in 2 groups |
| `screenshots/03-david-groups-membership.png` | David in 2 groups |
| `screenshots/04-policy-simulator-charles.png` | Charles: union of permissions proven |
| `screenshots/05-policy-simulator-david.png` | David: union of permissions proven |

## Clean up

All 6 users and 3 groups were deleted after the lab — users first, then
groups (some consoles block group deletion while members remain).

See [`INSTRUCTIONS.md`](./INSTRUCTIONS.md) to reproduce this lab step by step.
