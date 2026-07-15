# M.Ez — AWS Cloud Architecture

<!-- ponytail: design doc only; write Terraform/CDK when a real deployment is scheduled -->

```
        Patients (iOS/Android)   Doctors (iOS/Android/Tablet)
                 │                        │
                 └────────┬───────────────┘
                          ▼
   Pharmacy web ──► CloudFront + WAF ◄── Hospital admin web / Company site
   (S3 static hosting for all web frontends)
                          │
                          ▼
                   API Gateway (REST + WebSocket for chat)
                          │
                   Cognito (auth: patient / doctor / pharmacy / admin roles)
                          │
              ┌───────────┼───────────────┐
              ▼           ▼               ▼
        Lambda /      Lambda:         Lambda:
        Fargate:      chat +          prescriptions
        appointments  notifications   & pharmacy orders
              │           │               │
              ▼           ▼               ▼
        Aurora        DynamoDB        Aurora (RDS)
        PostgreSQL    (messages)      + S3 (documents,
        (core data)                    reports, images)
                          │
                          ▼
              SNS / SES / FCM-via-Pinpoint
              (push, SMS, email notifications)
```

## Services

| Concern | AWS service | Why |
|---|---|---|
| Web hosting (4 web UIs) | S3 + CloudFront | Static sites, cheap, global CDN |
| API | API Gateway + Lambda | Pay-per-use, no servers to patch |
| Real-time chat | API Gateway WebSocket + DynamoDB | Managed, scales to zero |
| Auth & roles | Cognito user pools (4 groups) | Built-in MFA, token-based, HIPAA-eligible |
| Core database | Aurora PostgreSQL (Multi-AZ) | Relational: patients, appointments, prescriptions |
| Files (reports, scans) | S3 (SSE-KMS encrypted) | Durable, versioned |
| Notifications | SNS + SES + Pinpoint | Push/SMS/email from one place |
| Monitoring | CloudWatch + X-Ray | Comes free with the above |
| Secrets | Secrets Manager | DB creds, API keys |

## Security notes (non-negotiable for health data)

- All data encrypted at rest (KMS) and in transit (TLS 1.2+).
- VPC-isolated databases; Lambdas in private subnets.
- Cognito role groups enforce interface-level access (patient can't hit admin APIs).
- CloudTrail on for audit logging; required for HIPAA/regional health-data compliance.
- Backups: Aurora automated snapshots, 35-day retention; S3 versioning.

## Environments

`dev` → `prod`. Two accounts via AWS Organizations. Skip staging until there's a team.
