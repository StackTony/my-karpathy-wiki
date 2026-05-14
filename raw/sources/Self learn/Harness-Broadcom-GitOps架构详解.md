vSphere Supervisor 9.0 Services and Standalone Components

[Overview](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0.html)

- [Release Notes](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/release-notes.html)
- [Managing vSphere Kubernetes Service with vSphere Supervisor](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/managing-vsphere-kubernetes-service.html)
- [Managing vSphere Kubernetes Service Clusters and Workloads](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/managing-vsphere-kuberenetes-service-clusters-and-workloads.html)
- [Managing Supervisor Services](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/managing-supervisor-services-with-vsphere-iaas-control-plane.html)
- [Using Supervisor Services](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/using-supervisor-services.html)
- [Modern Applications Consumption Models](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/modern-applications.html)
	- [Continuous Integration and Delivery with Harness](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/modern-applications/continuous-integration-and-delivery-with-harness.html)
		- [Continuous Integration and Delivery with Harness Architecture](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/modern-applications/continuous-integration-and-delivery-with-harness/gitops.html)
				- [Continuous Integration and Delivery with Harness Design](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/modern-applications/continuous-integration-and-delivery-with-harness/continuous-integration-and-delivery-with-harness-design.html)
				- [Security Integration with Wiz and Dynatrace into the Harness CI/CD pipeline](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/modern-applications/continuous-integration-and-delivery-with-harness/vks-security-integration-with-wiz-and-observability-with-dynatrace-into-the-harness-cicd-pipeline.html)
				- [IAM Roles for Service Accounts with vSphere Kubernetes Service](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/modern-applications/continuous-integration-and-delivery-with-harness/iam-roles-for-service-accounts-with-vsphere-kubernetes-service.html)
		- [Continuous Integration and Delivery with the Argo CD Supervisor Service](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/modern-applications/continuous-integration-and-delivery-with-argocd.html)
- [Documentation Legal Notice](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/documentation-legal-notice-english-public.html)

The high-level architecture for implementing GitOps with Harness and

VKS

clusters uses a CI/CD approach, integrating a suite of tools for automation, security, and observability. This architecture aims to provide a robust and efficient delivery pipeline for applications and infrastructure.

![](https://techdocs.broadcom.com/content/broadcom/techdocs/us/en/vmware-cis/vcf/vcf-service-administration-and-development/9-0/_jcr_content/assetversioncopies/a222c5dd-193c-493e-809e-338c22ad0623.cq5dam.web.1280.1280.jpeg)

Example Harness CI/CD Architecture

## Introduction to GitOps with CI/CD

GitOps is an operational framework that takes DevOps best practices that are used for application development, like version control, collaboration, compliance, and CI/CD, and applies them to infrastructure automation. With GitOps, the desired state of the application and infrastructure is described declaratively in Git, and Git repositories are the single source of truth. CI/CD pipelines then automate the deployment and synchronization of this desired state to the deployment environments.

## Architectural Overview

The architecture uses a combination of industry-leading tools to create a secure, automated, and observable GitOps workflow. The core principles guiding this architecture include:

- Git as the single source of truth - All infrastructure and application configurations are stored in Git.
- Automation - CI/CD pipelines automate the build, test, and deployment processes.
- Observability - Comprehensive monitoring and tracing provide insights into the system's health and performance.
- Security - Integrated security scanning and secrets management protect the pipeline and deployed applications.

## Component Overview

The Harness CI/CD architecture incorporates the following components:

- GitLab - Serves as the Git repository for source code, infrastructure as code (IaC), and configuration files. It also provides built-in CI/CD capabilities.
- Harness - Acts as the primary CI/CD platform, orchestrating deployments, managing pipelines, and providing release automation.
- Artifactory - Functions as a universal repository manager for all binaries, artifacts, and Docker images.
- Dynatrace - Provides end-to-end observability, including application performance monitoring (APM), infrastructure monitoring, and digital experience monitoring.
- HashiCorp Vault - Manages and secures sensitive data, such as API keys, passwords, and certificates.
- Wiz - Offers cloud security posture management (CSPM) and cloud workload protection platform (CWPP) capabilities, providing continuous security monitoring across the cloud environment.

## Detailed Workflow

In each phase in the Harness CI/CD architecture, you must include a set of operations.

| CI/CD Pipeline Phase | Operations |
| --- | --- |
| 1\. Code commit and CI pipeline (GitLab and Harness) | - Developer commits code. 	Developers push application code or infrastructure changes (IaC) to a designated GitLab repository. - GitLab webhook. 	A GitLab webhook triggers a Harness CI pipeline upon code commit to the main branch or a pull request merge. - Harness CI pipeline. 	- Fetches the latest code from GitLab. 	- Runs static code analysis and unit tests. 	- Builds application binaries or Docker images. 	- Pushes built artifacts and Docker images to Artifactory. |
| 2\. Artifact management (Artifactory) | - Artifact storage. 	Artifactory securely stores all built artifacts, Docker images, and other dependencies. - Version control. 	Artifactory maintains version control for all stored artifacts, enabling easy rollbacks if needed. |
| 3\. Security scanning (Wiz) | - Pre-deployment scanning. 	Wiz integrates with the CI pipeline (using Harness) to scan Docker images and IaC templates for vulnerabilities, misconfigurations, and compliance issues before deployment. - Continuous monitoring. 	Wiz continuously monitors the cloud environment and deployed workloads for security risks and compliance deviations. |
| 4\. Secrets management (HashiCorp Vault) | - Centralized secret storage. 	All sensitive information, such as database credentials, API keys, and certificates, is stored securely in HashiCorp Vault. - Dynamic secrets. 	Vault can generate dynamic secrets for services, reducing the risk of compromised long-lived credentials. - Integration with Harness. 	Harness integrates with Vault to retrieve secrets at deployment time, ensuring that sensitive information is never hardcoded or exposed in plain text. |
| 5\. CD Pipeline and deployment (Harness and GitLab) | - Harness CD pipeline trigger. 	Upon successful completion of the CI pipeline and security scans, a Harness CD pipeline is triggered. This can also be triggered manually for specific releases. - GitOps reconciliation. 	The CD pipeline fetches the desired state (application and infrastructure configurations) from the Git repository (GitLab). - Deployment to environment. 	Harness orchestrates the deployment to the target environment (for example, Kubernetes cluster, cloud infrastructure). The deployment operation involves: 	- Pulling the latest images from Artifactory. 	- Applying the IaC changes. 	- Updating application configurations. - Rollback strategy. 	Harness provides robust rollback capabilities, providing support for quick reversions to previous stable states if deployment failures or issues occur. |
| 6\. Observability and monitoring (Dynatrace) | - Automatic instrumentation. 	Dynatrace automatically instruments applications and infrastructure upon deployment, collecting metrics, logs, and traces. - End-to-end monitoring. 	Provides real-time insights into application performance, infrastructure health, user experience, and business impact. - Problem detection. 	The AI engine of Dynatrace automatically detects anomalies, identifies root causes, and provides intelligent alerts. - Feedback loop. 	Monitoring data from Dynatrace can feed back into the development process to improve application quality and performance. |

## Code Repository for Harness CI/CD

For an example of a Harness CI/CD pipeline, see [Harness CI/CD Pipeline in the VKS Consumption Models GitHub public repository](https://github.com/vsphere-tmm/vks-consumption-models/blob/main/harness-consumption-model/README.md). This Harness CI/CD pipeline builds and deploys a modern microservices-based application. The pipeline automates Docker image creation, pushing to a container registry, updating Helm charts, and rolling out deployments to a

VKS

cluster.

Other Product Support Resources

Support

[Support Portal](https://support.broadcom.com/) [Product Communities](https://community.broadcom.com/) [Knowledge Base](https://www.broadcom.com/support/knowledgebase)

Learning

[Brocade Education](https://www.broadcom.com/support/education/brocade) [Mainframe Software Education](https://www.broadcom.com/support/education/mainframe/education-program) [Software Education](https://www.broadcom.com/support/education/software) [VMware Learning](https://www.broadcom.com/support/education/vmware)

Need more help?

[Virtual Agent](https://support.broadcom.com/) [Advanced Support](https://www.broadcom.com/support/services-support/ca-support/support-programs) [Contact Us](https://support.broadcom.com/web/ecx/contact-support)