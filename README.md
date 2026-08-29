# Aegis Cloud Security Operations Platform

A production-style cloud security engineering project demonstrating
secure AWS infrastructure, identity security, security posture management,
threat detection, vulnerability management, edge protection, DevSecOps
controls and incident response.

## Project Objectives

- Build secure AWS infrastructure using Terraform
- Implement least-privilege IAM
- Replace long-lived AWS credentials with temporary federated access
- Enable centralized logging and security monitoring
- Implement AWS GuardDuty, Security Hub, Config and Inspector
- Protect web workloads using secure edge and origin controls
- Integrate security controls into CI/CD
- Demonstrate vulnerability-management workflows
- Perform controlled incident-response simulations
- Map technical controls to recognised security frameworks
- Produce operational runbooks and auditable evidence

## Project Status

| Stage | Description | Status |
|---|---|---|
| Stage 0 | Project Foundation | ✅ Complete |
| Stage 1 | Secure AWS Identity Foundation | ✅ Complete |
| Stage 2 | AWS Security Baseline and Remediation | ✅ Complete |

## Stage 2 — AWS Security Baseline and Remediation

**Status: ✅ Complete**

Stage 2 established and verified a hardened AWS account security baseline.
The work focused on identity hygiene, root-account security, storage security,
logging and configuration controls, public-access remediation, federated CI/CD
access and external-access analysis.

### Identity and Credential Security

The account was reviewed for legacy IAM users, long-lived credentials,
console access and excessive administrative privileges.

Key outcomes included:

- Verified AWS access through IAM Identity Center and temporary STS credentials
- Removed legacy IAM access keys
- Removed unnecessary IAM console access
- Removed legacy administrative group membership and direct policies
- Verified removal of dormant legacy IAM users
- Verified the root account has MFA enabled
- Verified the root account has no active access keys
- Hardened the IAM account password policy
- Verified the final IAM user inventory after cleanup

### S3 and CloudFront Security

A security review identified legacy public S3 access associated with the
existing website architecture.

Rather than enabling restrictions blindly and risking an outage, the
dependency was investigated and remediated.

The final architecture uses:

```text
Internet
   |
   v
Cloudflare / DNS
   |
   v
Amazon CloudFront
   |
   v
Origin Access Control (OAC)
   |
   v
Private Amazon S3 bucket
```

Key controls implemented and verified:

- Account-level S3 Block Public Access
- Bucket-level S3 Block Public Access
- Private S3 origin access
- CloudFront Origin Access Control (OAC)
- Restricted S3 bucket policy
- Terraform state bucket encryption
- Terraform state bucket versioning
- Final public-access regression checks

### Logging and Configuration Controls

Stage 2 also reviewed foundational AWS security monitoring controls.

The assessment included:

- AWS CloudTrail configuration and logging verification
- AWS Config recorder assessment and configuration
- Config delivery-channel preparation
- Config service-linked role verification
- Encryption verification for supporting storage
- EBS encryption-by-default verification

### GitHub Actions OIDC Hardening

The GitHub Actions deployment role was reviewed after IAM Access Analyzer
identified an external federated trust relationship.

The IAM role uses GitHub OIDC so CI/CD can obtain temporary AWS credentials
without storing long-lived AWS access keys.

The trust relationship was restricted to:

```text
Repository: daviddigheji/fuelops-ecs-platform
Branch:     main
Audience:   sts.amazonaws.com
```

The GitHub Actions workflow was also hardened so that:

- Pull requests can validate Terraform without AWS credentials
- `id-token: write` is not granted workflow-wide
- OIDC permission is granted only to the AWS-authenticated Terraform Plan job
- The AWS-authenticated job runs only on pushes to `main`
- Terraform validation works without connecting to the remote backend
- Terraform Validate and Terraform Plan were verified successfully

### IAM Access Analyzer

IAM Access Analyzer was used in `eu-west-2` to identify resources with
external or federated access.

Two active findings were investigated:

1. AWS IAM Identity Center SAML federation
2. GitHub Actions OIDC federation

The IAM Identity Center relationship was confirmed as intentional.

The GitHub Actions trust relationship was reviewed, hardened to the specific
repository and `main` branch, and successfully tested through GitHub Actions.

After investigation, the intentional findings were archived.

Final verification:

```text
ACTIVE Access Analyzer findings: 0
```

### Stage 2 Outcome

Stage 2 demonstrates the following security-engineering workflow:

```text
Discover
   |
   v
Assess
   |
   v
Investigate
   |
   v
Remediate
   |
   v
Validate
   |
   v
Document
```

The resulting AWS environment has a reduced identity and public-access attack
surface, uses temporary federated credentials for administrative and CI/CD
access, and includes auditable evidence of the implemented controls.

## Evidence

Stage 2 contains 79 evidence artifacts covering discovery, remediation and
final verification.

Evidence is stored under:

```text
evidence/stage-2/
```

Key final evidence includes:

- `stage-2-35-root-account-security-posture-verified.png`
- `stage-2-36-github-oidc-trust-policy-review.png`
- `stage-2-37-github-oidc-trust-policy-remediated.png`
- `stage-2-38-access-analyzer-active-findings-review.png`
- `stage-2-39-github-oidc-workflow-verified.png`
- `stage-2-40-access-analyzer-zero-active-findings.png`

## Next Stage

The next phase of Aegis will build on this hardened AWS account baseline with
additional detection, security posture management, vulnerability management
and automated security-response capabilities.
