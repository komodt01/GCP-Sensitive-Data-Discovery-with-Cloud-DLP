# GCP Sensitive Data Discovery with Cloud DLP

## Project Overview

This project demonstrates a focused sensitive-data discovery workflow in Google Cloud using Cloud DLP and BigQuery.

For this scenario, I used a BigQuery table as the structured data source and Google Cloud DLP to inspect the data for selected sensitive information types. Terraform was used to enable the DLP API and assign the required DLP role to a service account, while Python was used to initiate the DLP inspection job.

The project is a portfolio implementation intended to demonstrate the security architecture concepts behind discovering and classifying sensitive data before additional protection and governance controls are applied.

## Project Scope Note

This project is a portfolio implementation created to demonstrate sensitive-data discovery using Google Cloud DLP and BigQuery. The repository reflects the components I implemented and tested in a controlled environment. Production architecture considerations discussed in this documentation represent how I would extend or evaluate the design for an enterprise environment and should not be interpreted as components deployed as part of this project.

## Problem Statement

Organizations may store large amounts of structured data without having complete visibility into where personally identifiable or other sensitive information exists.

Before decisions can be made about access restrictions, masking, tokenization, retention, or other data-protection controls, the organization first needs a way to identify sensitive data.

For this project, the problem I focused on was:

**How can sensitive information stored in BigQuery be programmatically inspected and identified using Google Cloud security services?**

## Implemented Architecture

The implemented workflow consists of:

**BigQuery Table → Google Cloud DLP → DLP Inspection Job**

Supporting components include:

- **BigQuery** as the structured data source
- **Google Cloud DLP API** for sensitive-data inspection
- **Python** for creating the DLP inspection job
- **Terraform** for enabling the DLP API and assigning DLP permissions
- **Service Account** for authenticated access to the DLP service

The Python implementation configures DLP to inspect the target BigQuery table for selected information types, including:

- `PERSON_NAME`
- `EMAIL_ADDRESS`

The script submits the inspection job to Google Cloud DLP and returns the created DLP job identifier.

## Security Architecture Decisions

### Sensitive Data Discovery Before Protection

For this scenario, I focused first on data discovery rather than immediately applying masking or tokenization.

The reason I chose this approach is that an organization needs to understand where sensitive information exists before determining which protection controls should be applied to it.

### Defined Information Types

Rather than performing an unrestricted inspection, the implementation explicitly identifies the information types being evaluated.

This demonstrates how DLP inspection can be aligned with the types of sensitive information an organization expects to protect.

### Service-Based Access

Terraform assigns the required DLP role to a service account used by the workflow.

This separates application/service access from individual user access and provides a foundation for more tightly scoped IAM design in a production environment.

### Infrastructure Configuration with Terraform

Terraform is used for the cloud configuration included in this implementation rather than relying entirely on manual console configuration.

This makes the implemented DLP service enablement and IAM assignment repeatable and reviewable.

## What Was Implemented

The project includes:

- Google Cloud DLP API enablement through Terraform
- DLP IAM role assignment to a service account
- Python integration with the Google Cloud DLP API
- BigQuery table targeting for DLP inspection
- Inspection for selected sensitive information types
- Creation of a Cloud DLP inspection job

## Project Scope

This is a focused portfolio implementation and not a complete enterprise data-governance platform.

The project demonstrates sensitive-data discovery and the beginning of a classification workflow. It does **not** claim to implement a production tokenization platform, enterprise-wide access governance, automated remediation, or a complete data-loss-prevention program.

## Production Architecture Considerations

In a production environment, I would evaluate additional controls based on the organization's data classification, regulatory requirements, threat model, and operating model.

These could include:

- De-identification, masking, or tokenization of sensitive fields
- More granular IAM roles and service-account permissions
- Centralized storage and analysis of DLP findings
- Cloud Logging and monitoring integration
- Automated workflows based on classification results
- Encryption and key-management requirements
- Broader information-type coverage
- Scheduled or event-driven inspection
- Governance processes for reviewing and remediating findings

These are production architecture considerations and were **not implemented as part of this project**.

## Technologies

- Google Cloud Platform
- Google Cloud DLP
- BigQuery
- Terraform
- Python
- Google Cloud IAM

## Repository Contents

Key project files include:

- `main.tf` – DLP API enablement and IAM configuration
- `variables.tf` – Terraform input variables
- `dlp_pipeline.py` – Python-based BigQuery DLP inspection
- `setup_wsl.md` – WSL and Google Cloud CLI setup notes
- `technologies.md` – Technology overview
- `compliance_mapping.md` – Security/compliance reference material
- `lessonslearned.md` – Project observations and lessons
- `teardown.md` – Resource cleanup guidance

## Key Takeaway

The main architectural lesson from this project is that data protection starts with knowing where sensitive information exists.

Cloud DLP provides the inspection capability, but discovery is only the first control in a broader data-security architecture. Classification results can ultimately inform decisions around access, de-identification, monitoring, retention, and other protection mechanisms.
