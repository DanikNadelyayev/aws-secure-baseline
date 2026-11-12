# AWS account security baseline

Personal lab account, region eu-north-1 (Stockholm). Configured manually in the console first; the next step is to codify it with Terraform.

## Account and identity (IAM)
- Root user protected with MFA; no access keys exist for root.
- Root is not used for daily work. Created IAM user `danylo-admin` with console access and MFA.
- `danylo-admin` has `AdministratorAccess`. This is acceptable for a single-person lab only; in a real environment I would use least-privilege roles, IAM Identity Center (SSO) and short-lived credentials instead of long-lived users.
- Cost guardrail: zero-spend budget with email alerts.

## Logging: AWS CloudTrail
- Trail `management-trail` logging management events (read and write) to a dedicated S3 bucket.
- Log file validation enabled, so tampering with or deleting log files can be detected.
- Logs encrypted at rest with AWS-managed keys (SSE-S3). In production I would use a customer-managed KMS key to control who can decrypt logs.
- Data events, Insights and CloudWatch Logs delivery left off (extra cost, not needed for the lab). Recursive logging disabled to avoid log-delivery loops.

## Threat detection: Amazon GuardDuty
- Enabled in eu-north-1 (30-day trial). GuardDuty analyses CloudTrail, VPC Flow Logs and DNS logs.
- Generated and reviewed sample findings, including a Critical attack sequence (`AttackSequence:EKS/CompromisedCluster`) mapped to MITRE ATT&CK (T1204.002, T1496, T1543): malicious file execution, privilege escalation and cryptomining (xmrig).
- My response approach for such a finding: validate (true or false positive) → contain (isolate workload, block IPs) → revoke or rotate credentials → investigate scope in CloudTrail → recover from known-good images → fix root cause and document.

## Next steps
- [ ] Codify this baseline in Terraform and scan it with Checkov
- [ ] Enable AWS Security Hub with the CIS AWS Foundations Benchmark
- [ ] Replace the admin IAM user with least-privilege roles
- [ ] Disable GuardDuty before the trial ends
