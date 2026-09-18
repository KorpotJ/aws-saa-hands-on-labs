# IAM — Identity and Access Management

Five hands-on parts covering IAM — from user/group permissions through
roles, CLI access, and security auditing — built while working through
AWS SAA-C03 Section 4.

## Progress

| Part | Topic | Status |
|---|---|---|
| 1 | Multi-group cumulative permissions | ✅ Done |
| 2 | Password Policy + MFA | 📋 Planned |
| 3 | IAM Roles | ✅ Done |
| 4 | Access Keys + CLI | 📋 Planned |
| 5 | Security Tools (Credential Report + Access Advisor) | 📋 Planned |

## Lessons learned across this lab

- **`SecurityAudit` does not grant `s3:GetObject`** — only read access to
  config metadata, not object content. Don't infer a managed policy's
  permissions from its name — test it. *(Part 1)*
- **Creating an EC2 role through the Console also creates a matching
  Instance Profile automatically** — via CLI/SDK these are two separate
  objects that have to be created and linked explicitly. *(Part 3)*

---

## Part 1: Multi-group cumulative permissions ✅

A user's effective IAM permissions are the union of every group they
belong to — proved with a 3-group / 6-user scenario and Policy Simulator.

<details>
<summary>Full write-up, scenario, results, and screenshots</summary>

### What this lab shows

A user's effective IAM permissions are the **union** of every group they
belong to — not just their "primary" group. This lab builds a 3-group /
6-user setup where two users sit in two groups each, then proves the union
with IAM Policy Simulator.

![Diagram: users, groups, and policies showing cumulative permission via shared membership](screenshots/00-concept-diagram.png)

### Scenario

| Group | Attached policy | Members |
|---|---|---|
| `Developers` | `AmazonEC2FullAccess` | Alice, Bob, Charles |
| `AuditTeam` | `SecurityAudit` | Charles, David |
| `Operations` | `AmazonS3FullAccess` | David, Edward, Fred |

![IAM user groups overview — Developers, AuditTeam, and Operations with correct member counts](screenshots/01-groups-overview.png)

Charles and David are each in two groups, so they each accumulate permissions
from two policies at once — confirmed on their own group membership pages:

![Charles's Groups tab showing membership in both Developers and AuditTeam](screenshots/02-charles-groups-membership.png)

![David's Groups tab showing membership in both AuditTeam and Operations](screenshots/03-david-groups-membership.png)

### Tagging decision

Each user got a `Department` tag. IAM allows exactly one value per tag key,
so Charles and David — who don't map cleanly to a single department —
needed a deliberate choice.

**Decision:** tag both as `Department: AuditTeam`, since that's the trait
that makes them different from everyone else in the setup, rather than
arbitrarily picking one of their two "home" groups.

**Trade-off to remember for ABAC later:** this tag carries no information
about their *other* group membership. If a future attribute-based policy
grants access to anyone tagged `Department=Developers`, Charles won't match
it even though he also has developer permissions. Tags here are for
search/reporting — they don't drive permissions (that's what groups do).

### Result: Policy Simulator

**Charles (Developers + AuditTeam)**

| Action | Result | Why |
|---|---|---|
| `ec2:RunInstances` | ✅ Allowed | `AmazonEC2FullAccess` via Developers |
| `s3:GetBucketAcl` | ✅ Allowed | `SecurityAudit` via AuditTeam |

![Policy Simulator result for Charles — ec2:RunInstances and s3:GetBucketAcl both Allowed](screenshots/04-policy-simulator-charles.png)

**David (AuditTeam + Operations)**

| Action | Result | Why |
|---|---|---|
| `s3:PutObject` | ✅ Allowed | `AmazonS3FullAccess` via Operations |
| `ec2:DescribeInstances` | ✅ Allowed | `SecurityAudit` via AuditTeam |
| `ec2:RunInstances` | ⛔ Denied | Not a member of Developers |

![Policy Simulator result for David — s3:PutObject and ec2:DescribeInstances Allowed, ec2:RunInstances Denied](screenshots/05-policy-simulator-david.png)

### Clean up

All 6 users and 3 groups were deleted after the lab — users first, then
groups (some consoles block group deletion while members remain).

</details>

---

## Part 2: Password Policy + MFA 📋

Custom password policy + virtual MFA on the root account.

<details>
<summary>Full write-up (coming soon)</summary>

_Not started yet._

</details>

---

## Part 3: IAM Roles ✅

An IAM Role bundles two things: a **trust policy** (who is allowed to
assume it) and a **permission policy** (what it can do once assumed).
This lab creates a role for EC2 and verifies both halves directly in the
console.

<details>
<summary>Full write-up, scenario, results, and screenshots</summary>

### What this lab shows

Unlike a user, a role has no long-term credentials — it's assumed via AWS
STS (`sts:AssumeRole`), which issues temporary credentials that expire.
Roles are how AWS services (EC2, Lambda, etc.), other AWS accounts, or
federated identities get scoped, expiring access instead of a shared
long-term secret.

### Scenario

Created an EC2 role from the console with two settings:

- **Trusted entity:** AWS service → EC2 (this becomes the trust policy —
  `Principal: ec2.amazonaws.com`, `Action: sts:AssumeRole`)
- **Permission policy:** `IAMReadOnlyAccess` (read-only access to IAM)
- **Role name:** `DemoRoleForEC2`

### Result

**Trust relationships** — confirms EC2 is the only trusted entity that can
assume this role:

![Trust relationships tab for DemoRoleForEC2, showing ec2.amazonaws.com as the trusted principal](screenshots/06-role-trust-relationship.png)

**Permissions** — confirms `IAMReadOnlyAccess` is attached:

![Permissions tab for DemoRoleForEC2, showing IAMReadOnlyAccess attached](screenshots/07-role-permissions.png)

### Note on Instance Profiles

Creating this role through the console silently created a matching
**Instance Profile** (`arn:aws:iam::<account-id>:instance-profile/DemoRoleForEC2`)
— this is the actual object an EC2 instance attaches to; the role lives
inside it. The console hides this distinction. Via CLI/SDK, both the role
and the instance profile must be created and linked as separate steps.

This role isn't attached to a running EC2 instance yet — that happens once
the EC2 section of the course is reached.

</details>

---

## Part 4: Access Keys + CLI 📋

<details>
<summary>Full write-up (coming soon)</summary>

_Not started yet._

</details>

---

## Part 5: Security Tools 📋

IAM Credentials Report + IAM Access Advisor.

<details>
<summary>Full write-up (coming soon)</summary>

_Not started yet._

</details>

---

See [`INSTRUCTIONS.md`](./INSTRUCTIONS.md) for step-by-step reproduction of
each part.
