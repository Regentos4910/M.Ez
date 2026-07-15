# M.Ez

A platform connecting patients, doctors, hospitals and pharmacies for fast, easy communication.

## What's here

Clickable HTML prototypes for every interface, sharing one theme (white / pastel blue / pastel green in `theme.css`). Open any `index.html` in a browser.

| Folder | Interface | Target platform |
|---|---|---|
| `company-webpage/` | Public landing page | Web |
| `patient-app/` | Patient app (book, chat, prescriptions) | Android / iOS |
| `doctor-app/` | Doctor app (schedule, patients, e-prescribe) | Android / iOS / tablet |
| `pharmacy-webapp/` | Pharmacy portal (incoming prescriptions, inventory) | Web |
| `hospital-admin/` | Hospital admin dashboard (appointments, wards, staff) | Web |
| `aws-infra/` | Cloud architecture design (`architecture.md`) | AWS |

## Core flow

Patient books → doctor consults and e-prescribes → prescription lands at the pharmacy → patient gets notified. Hospital admin oversees it all.

## Status

Prototype stage. Next steps when building for real: React Native/Expo for the two mobile apps, a JS framework + API for the web apps, Terraform/CDK for the AWS design.
