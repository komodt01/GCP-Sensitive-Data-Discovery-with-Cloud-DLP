# Technical Case Study: GCP Sensitive Data Discovery with Cloud DLP

## Architecture Problem

Organizations can store large amounts of structured data without having complete visibility into which fields contain sensitive information. That creates a security problem because decisions about masking, tokenization, access restrictions, retention, and monitoring depend on first understanding what sensitive data exists and where it resides.

For this scenario, I focused on the discovery step: using Google Cloud DLP to inspect structured data in BigQuery for selected sensitive information types.

The objective was not to build a complete enterprise DLP or data-governance platform. It was to demonstrate the technical foundation for sensitive-data discovery while also documenting the broader controls that would need to surround that capability in an enterprise environment.

## Project Scope Note

This project is a portfolio implementation created to demonstrate sensitive-data discovery using Google Cloud DLP and BigQuery. The repository reflects the components I implemented in a controlled environment, along with security requirements and future-state architecture considerations.

Where this case study discusses tokenization, IAM Conditions, centralized findings management, monitoring, approval workflows, or other enterprise controls, those are design requirements or production recommendations and should not be interpreted as components implemented as part of the project.

## Assumptions and Constraints

One assumption I made for this scenario was that structured data already existed in BigQuery. The architecture problem therefore begins with inspecting that data rather than designing the upstream ingestion platform.

The project also identified an environment constraint around Access Context Manager capabilities. Access Levels were considered as part of the future access-governance design, but the available environment did not provide the GCP organization-level prerequisites needed to implement that control.

Rather than treating that as an implemented capability, I documented it as a future architecture option.

## Implemented Technical Flow

The implemented flow was:

**BigQuery Table → Google Cloud DLP → DLP Inspection Job**

Supporting components included:

- Google Cloud DLP
- BigQuery
- Google Cloud IAM
- Service account
- Terraform
- Python
- Google Cloud CLI / WSL development environment

Terraform was used to enable the DLP API and assign the `roles/dlp.user` role to the service account used by the workflow.

Python was used to create a Google Cloud DLP client and submit an inspection job against a specified BigQuery table.

The inspection configuration included two selected information types:

- `PERSON_NAME`
- `EMAIL_ADDRESS`

The script submits the inspection request and prints the DLP job identifier returned by Google Cloud.

## Technical Request Flow

At a technical level, the implemented workflow is:

1. The Python process uses the configured Google Cloud authentication environment.
2. The script creates a `DlpServiceClient`.
3. The target Google Cloud project is identified.
4. The target BigQuery project, dataset, and table are supplied as the table reference.
5. The inspection configuration defines the sensitive information types to evaluate.
6. BigQuery is specified as the storage target for the DLP inspection.
7. The script submits the inspection job through the DLP API.
8. Google Cloud returns the created DLP job identifier.

The implementation establishes the inspection request path. The repository does not demonstrate an automated findings-export, remediation, or tokenization workflow.

## Architecture Decisions

### Discovery Before Protection

For this scenario, I chose to start with discovery and classification rather than immediately applying tokenization or masking.

The reason I chose that sequence is that protection decisions should follow an understanding of the data. Different categories of sensitive information can require different controls based on business use, regulatory obligations, exposure risk, and whether the original value must remain available.

DLP inspection therefore serves as an input into broader data-security decisions rather than representing the entire data-protection architecture.

### BigQuery as the Data Source

The implementation targets a BigQuery table directly.

For this scenario, that allowed me to focus on inspection of structured cloud data without introducing an additional application or data-transfer workflow simply to perform classification.

The design documentation originally considered a broader pipeline, but the implemented Python workflow specifically targets BigQuery.

### Selected Information Types

The inspection configuration explicitly identifies `PERSON_NAME` and `EMAIL_ADDRESS`.

I intentionally treat these as examples of classification targets rather than claiming that two detectors would be sufficient for an enterprise deployment.

In a production environment, the information types would need to be selected based on the organization's actual data classification policy, business processes, regulatory obligations, and risk profile.

### Service Identity

The workflow uses a service account rather than embedding an individual administrator identity into the application flow.

Terraform assigns `roles/dlp.user` to that service account.

This demonstrates the use of a workload identity for the DLP interaction. I would not claim that this implementation proves least privilege. In a production environment, I would evaluate the exact permissions required by the workload and determine whether the predefined role should be reduced or replaced with a more tightly scoped role.

### Infrastructure Configuration with Terraform

Terraform was used for the portion of the Google Cloud configuration represented in the implementation: enabling the DLP API and assigning the DLP IAM role.

This provides a repeatable and reviewable representation of those configuration elements.

The repository's earlier design documentation described additional Terraform-managed resources, including inspection templates and storage resources. Those elements are not represented in the `main.tf` implementation reviewed for this case study and are therefore not claimed as implemented.

## What I Implemented

Based on the project implementation, the defensible technical scope is:

- Google Cloud DLP API enablement through Terraform
- IAM assignment of `roles/dlp.user` to a service account
- Python integration with the Google Cloud DLP client library
- BigQuery table configuration as the DLP inspection target
- Inspection configuration for `PERSON_NAME` and `EMAIL_ADDRESS`
- Submission of a DLP inspection job
- Printing of the DLP job identifier returned by the API

This case study does not claim successful enterprise-scale classification, automated CSV findings export, production monitoring, tokenization, de-identification, or access-governance enforcement.

## Security Requirements I Considered

In addition to the implemented workflow, I documented security requirements for a broader data-protection architecture.

These requirements included:

- Restricting inspection capabilities to approved identities
- Separating data-owner, DLP-administrator, and reader responsibilities
- Protecting input and output data
- Controlling access to inspection findings
- Capturing DLP and access activity for audit purposes
- Managing DLP configuration consistently
- Establishing approval expectations for configuration changes
- Mapping relevant controls to NIST 800-53 and ISO 27001 control areas

These requirements represent architecture and governance considerations. They should not be interpreted as evidence that every control was technically implemented in this portfolio project.

Similarly, the compliance mapping is a reference to relevant control families and security objectives, not a claim that this implementation achieved NIST or ISO certification or compliance.

## Implemented Controls vs. Future Controls

A key part of this project is distinguishing the implemented discovery capability from the larger security architecture.

### Implemented

The project establishes a workload identity, enables the DLP service, targets structured BigQuery data, defines selected sensitive-data detectors, and programmatically submits a DLP inspection job.

### Designed or Documented

The project documentation considers broader requirements around least privilege, role separation, logging, audit evidence, findings protection, configuration governance, and compliance alignment.

### Future / Production Considerations

The original Phase 2 roadmap identified additional capabilities including:

- Deterministic encryption and tokenization
- DLP de-identification templates
- Controlled detokenization
- Privileged access for restoration of original values
- IAM Conditions
- Access Levels where organization-level support is available
- Approval or break-glass workflows
- Monitoring and alerting for DLP failures and suspicious access
- DLP job-history retention
- Automated security reporting
- BigQuery governance integration
- Data Catalog tagging
- VPC Service Controls

None of these Phase 2 capabilities are presented as implemented.

## Production Architecture Considerations

### Findings Management

A production design would need to determine where DLP findings are stored, who can access them, how long they are retained, and how they feed downstream remediation or governance processes.

Findings can reveal where sensitive information exists, so the findings themselves need appropriate protection.

### De-Identification and Tokenization

Once sensitive data is discovered, the organization must decide whether particular use cases require the original value.

Depending on those requirements, I would evaluate masking, redaction, tokenization, deterministic encryption, or other de-identification techniques.

The choice would depend on factors such as reversibility, analytics requirements, correlation needs, and who is authorized to recover the original data.

### IAM Refinement

I would evaluate the exact DLP and BigQuery permissions required by each workload identity.

I would also evaluate how credentials are obtained and whether workload-based identity mechanisms can reduce or eliminate reliance on long-lived credentials.

### Logging and Monitoring

A production environment should provide visibility into DLP jobs, administrative changes, access failures, unusual inspection activity, and access to sensitive findings.

Those signals could feed centralized cloud monitoring or an enterprise SIEM depending on the organization's operating model.

### Automation and Response

For recurring data environments, I would evaluate scheduled or event-driven inspection rather than relying on manually initiated execution.

The organization would also need a defined response to findings. Depending on risk and business context, that could involve reporting, ticketing, alerting, policy review, access changes, or automated remediation.

### Organizational Guardrails

Controls such as IAM Conditions, Access Context Manager, and VPC Service Controls may depend on the organization's Google Cloud hierarchy and governance model.

I would evaluate those controls in the context of the enterprise landing zone rather than assuming they are available or appropriate for every environment.

## Architectural Boundary

The most important boundary in this project is between **discovery** and **protection**.

The implemented architecture addresses:

**How can a defined BigQuery data source be programmatically submitted to Google Cloud DLP for inspection of selected sensitive information types?**

It does not by itself answer:

**What should happen to every sensitive value after it is discovered?**

That second question requires business context, classification policy, regulatory requirements, application requirements, data ownership, and risk decisions.

Keeping those responsibilities separate prevents a technical inspection capability from being mistaken for a complete data-governance or data-protection program.

## Lessons Learned

One of the main lessons from this project was that the DLP API call is only a small part of the architecture.

The larger security questions are deciding what should be inspected, which identity should be allowed to perform the inspection, which classifications matter to the organization, how findings should be protected, and which controls should follow from discovery.

The project also reinforced the importance of distinguishing requirements and future-state architecture from implemented controls. Tokenization, IAM Conditions, centralized findings management, monitoring, and governance workflows are logical extensions of the design, but I would not represent them as implemented unless they had actually been built and tested.

## Architecture Takeaway

Sensitive-data discovery provides evidence that can drive subsequent security decisions.

For this scenario, BigQuery supplied the structured data source, Google Cloud DLP supplied the inspection capability, IAM established the service identity relationship, Terraform represented the implemented cloud configuration, and Python initiated the inspection workflow.

The project demonstrates the beginning of a data-protection architecture: first understand where sensitive information exists, then use business requirements and risk to determine what protection and governance controls should follow.
