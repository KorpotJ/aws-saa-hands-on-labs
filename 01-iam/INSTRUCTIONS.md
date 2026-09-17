# Reproducing this lab

Estimated time: 15–20 minutes. No cost — IAM users, groups, and Policy
Simulator are free.

## 1. Create 3 IAM groups

Console → IAM → User groups → Create group. Create `Developers`,
`AuditTeam`, `Operations`. Don't attach a policy yet.

## 2. Attach policies

- `Developers` → `AmazonEC2FullAccess`
- `AuditTeam` → `SecurityAudit`
- `Operations` → `AmazonS3FullAccess`

## 3. Create 6 users, assigned to groups at creation

- Alice, Bob, Charles → `Developers`
- Charles, David → `AuditTeam` (Charles is intentionally in two groups)
- David, Edward, Fred → `Operations`

## 4. Tag each user (`Department` key)

- Alice, Bob, Charles → `Developers`
- Edward, Fred → `Operations`
- Charles, David → open question. IAM allows only one value per tag key,
  and both users sit in two groups. Decide how you'd tag them before
  reading the "Tagging decision" section in `README.md`.

## 5. Screenshot: groups overview

IAM → User groups. Take this **after** step 3, or member counts will show 0.

## 6. Screenshot: Charles and David's group membership

IAM → Users → Charles → Groups tab (shows 2 groups). Repeat for David.

## 7. Policy Simulator

IAM → Policy simulator.

- Select **Charles**. Test `ec2:RunInstances` and `s3:GetBucketAcl` — both
  should be Allowed.
- Select **David**. Test `s3:PutObject`, `ec2:DescribeInstances` (Allowed)
  and `ec2:RunInstances` (Denied).

## 8. Clean up

Delete all 6 users first, then the 3 groups. Some consoles block deleting a
group that still has members.

## Before committing

Crop or blur the AWS account ID in every screenshot — check the browser
chrome / top nav bar especially, not just the console body.
