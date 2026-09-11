# Executive Case Study: GCP Sensitive Data Discovery with Cloud DLP

## Business Problem

Organizations cannot consistently protect sensitive data if they do not know where that data exists.

Structured cloud data may contain names, email addresses, customer information, financial information, or other sensitive values. Before an organization decides whether data should be masked, tokenized, restricted, retained differently, or monitored more closely, it first needs a reliable way to identify the sensitive information.

For this scenario, I focused on that first problem: establishing a sensitive-data discovery capability for structured data stored in Google Cloud BigQuery.

## Project Scope Note

This was a portfolio implementation performed in a controlled environment. I implemented a focused Google Cloud DLP inspection workflow and documented the broader security requirements that would surround it in an enterprise environment.

Production capabilities discussed in this case study, including tokenization, advanced access governance, centralized monitoring, and automated remediation, were not implemented as part of the project.

## Approach

For this scenario, I used Google Cloud DLP to inspect a defined BigQuery data source for selected types of sensitive information.

The implementation used a service identity for the DLP interaction, Terraform for the cloud configuration included in the project, and Python to initiate the inspection workflow.

I deliberately kept the implementation focused on discovery rather than trying to present it as a complete data-protection platform.

The reason I chose that boundary is that discovery should inform the next security decision. Finding sensitive data does not automatically tell the organization whether that data should be masked, tokenized, deleted, access-restricted, or left available for an approved business purpose.

## Business and Security Value

The value of sensitive-data discovery is not simply that a security tool can recognize an email address or a person's name.

The larger value is that classification provides evidence for decisions such as:

- Which datasets require stronger access restrictions
- Where de-identification or tokenization may be appropriate
- Which data requires additional monitoring
- Which systems may fall within regulatory or internal control scope
- Where data owners need to review business use and retention
- Which findings require remediation or risk acceptance

This creates a clearer connection between technical discovery and business governance.

## Architecture and Governance Considerations

While the implementation focused on the inspection capability, I also documented requirements for the broader control environment.

Those considerations included service identity, role separation, protection of findings, auditability, configuration governance, and alignment with relevant NIST 800-53 and ISO 27001 control areas.

I treated those as architecture requirements rather than claiming that every control was implemented.

The project also identified that some advanced Google Cloud access controls depend on organization-level capabilities. In an enterprise environment, I would evaluate those controls within the organization's existing Google Cloud hierarchy and landing-zone governance rather than assume they are available everywhere.

## What Was Implemented

The implemented project established the technical foundation for submitting a defined BigQuery data source to Google Cloud DLP for inspection of selected sensitive information types.

It included:

- Google Cloud DLP service enablement
- A service-account DLP role assignment
- BigQuery as the structured data source
- Selected sensitive-data inspection types
- Python-based submission of the DLP inspection job
- Terraform-managed configuration for the implemented cloud components

The project did not implement a production tokenization service, enterprise access-governance platform, automated findings remediation, or complete monitoring solution.

## Production Direction

If this capability were being designed for production, the next decisions would depend on the organization's business requirements and risk profile.

I would evaluate how findings are protected and reviewed, which data requires de-identification or tokenization, how access to sensitive data is governed, what activity needs to be monitored, and what response should occur when sensitive information is discovered unexpectedly.

I would also evaluate whether inspection should run on a schedule or in response to data events and how classification results should feed existing governance, security operations, and risk-management processes.

## Key Architecture Decision

The central decision in this project was to treat **discovery as an input to data protection rather than as the finished security solution**.

Google Cloud DLP can identify sensitive information, but technology alone cannot determine the appropriate business response.

That requires context: who owns the data, why the organization needs it, who should have access, how long it should be retained, what regulations apply, and what would happen if the data were exposed.

## Executive Takeaway

The project demonstrates a simple but important security architecture principle:

**You cannot consistently govern or protect sensitive data until you know where it is.**

For this scenario, I established the beginning of that control chain using BigQuery and Google Cloud DLP. The resulting discovery capability can serve as an input into broader decisions around access governance, de-identification, monitoring, retention, compliance, and risk.
