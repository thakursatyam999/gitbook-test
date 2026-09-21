---
description: Learn how to deploy Salesforce applications using Harness CD.
title: Salesforce deployments overview
tags:
  - salesforce
  - salesforce-deployment
  - salesforce-dx
  - salesforce-packages
---

<!-- # Salesforce deployments overview -->

This topic provides an overview of Salesforce deployments in Harness and walks you through the setup process.

{% hint style="info" %}
**Feature Flag**

This feature is behind the feature flag `CDS_ENABLE_SALESFORCE_APPLICATION_DEPLOYMENT`. Contact [Harness Support](mailto:support@harness.io) to enable this feature.
{% endhint %}

Salesforce deployments in Harness follow the same core principles as other deployment types, you define a service and deploy it to an environment. However, the way services and environments are configured is specific to Salesforce and requires understanding Salesforce-specific concepts.

## What you will learn from this topic

- **Salesforce artifact types:** The two ways to package and deploy Salesforce metadata: DX projects and unlocked packages.
- **Salesforce environments:** How a Salesforce "org" maps to a Harness environment, and the org types you can target.
- **Connector setup:** How to connect Harness to a Salesforce org using JWT or SFDX Auth URL authentication.
- **Infrastructure setup:** How to configure a Salesforce infrastructure definition, including the Inline and Remote setup options.

## What is a Salesforce deployment?

A Salesforce deployment involves moving metadata, the configuration and code that defines your Salesforce org, from one environment to another. This metadata includes Apex classes, Lightning Web Components, custom objects, validation rules, flows, profiles, and more.

Harness supports deploying two different Salesforce artifacts:

- **Salesforce DX Project**: Deploy metadata from version control systems (Git, GitHub, GitLab, Bitbucket) using a source-driven development model
- **Salesforce Package**: Deploy pre-built unlocked packages that bundle metadata for distribution

## Key concepts

### Salesforce packages

Packaging in Salesforce is a way to bundle and distribute metadata and code. Harness supports **Unlocked Packages (2GP)**, which are ideal for internal apps, modular development, and CI/CD workflows. Unlocked packages are fully source-driven and support versioning.

### Salesforce environments (Orgs)

Salesforce uses the term "org" to refer to an environment. Common org types include:

- **Production Org**: Your live Salesforce environment
- **Sandbox Orgs**: Isolated environments for development, QA, and UAT
- **Scratch Orgs**: Temporary, disposable environments for development
- **DevHub**: A production or Developer Edition org that manages scratch orgs and packages

---

## Before you begin

A Harness Salesforce deployment requires the following:

* **Salesforce Org**: A target Salesforce organization (production, sandbox, or scratch org) to deploy your application.
* **Salesforce Metadata**: Your application's metadata, which can come from:
  - A Salesforce DX project in version control (Git, GitHub, GitLab, etc.)
  - An existing Salesforce unlocked package
* **Salesforce CLI**: Harness uses the Salesforce CLI internally to interact with Salesforce orgs.
* **Authentication Credentials**: JWT-based credentials or SFDX Auth URL to connect to Salesforce orgs.

---

## Set up a Salesforce connector

A Salesforce connector establishes the connection between Harness and your Salesforce org. You will need connectors for:

- **Target orgs**: Where you want to deploy (production, sandbox, etc.)
- **DevHub org**: If you're working with packages or scratch orgs

Harness supports two authentication methods for Salesforce connectors:

- **JWT flow (Recommended)**: Uses JSON Web Token authentication with a Connected App
- **SFDX Auth URL flow**: Uses a pre-authenticated SFDX Auth URL

For detailed instructions on creating a Salesforce connector, including step-by-step guidance and authentication setup, see [Add a Salesforce connector](https://app.gitbook.com/s/3F2TpHXhur2QtQnORSM9/use-harness-platform/connectors/cloud-providers/add-a-salesforce-connector).

---

## Set up a Salesforce environment

A Harness Salesforce environment defines **where** you want to deploy your application. Setting up a Salesforce environment involves configuring the infrastructure definition.

### Create a Salesforce infrastructure

The infrastructure definition for Salesforce is straightforward. It only requires a Salesforce connector that points to your target org.

1. In your Harness project, go to **Environments**.
2. Select an existing environment, or click on **New Environment** to create a new one. 
3. Configure the environment:
   - Enter a name for the environment. Optionally, add a description and tags.
   - Select the **Environment Type**.
   - Save the configuration.

![](../../../.gitbook/assets/sf-env-1.png)

1. In the environment, go to **Infrastructure Definitions**.
2. Click **Infrastructure Definition** to create a new infrastructure.

![](../../../.gitbook/assets/sf-env-2.png)

6. Configure the infrastructure:
   1.   Enter a name for the infrastructure. Optionally, add a description and tags.
   2.   Under **Deployment Type**, select **Salesforce**. 
   3.   Select the Salesforce connector for your target org.
   4.   Save the configuration.

![](../../../.gitbook/assets/sf-env-3.png)

### Example infrastructure YAML

<details>
<summary>View complete infrastructure YAML</summary>

```yaml
infrastructureDefinition:
  name: salesforce_infrastructure
  identifier: salesforce_infrastructure
  orgIdentifier: default
  projectIdentifier: your_project_identifier
  environmentRef: salesforce_environment
  deploymentType: Salesforce
  type: Salesforce
  spec:
    connectorRef: salesforce_connector
  allowSimultaneousDeployments: false
```

</details>

---

## Next steps

Now that you understand the basics of Salesforce deployments in Harness and have set up your environment, you can proceed to deploy your Salesforce application:

- **[Deploy a Salesforce DX Project](deploy-salesforce-dx-project.md)** - Learn how to create a service with a Salesforce DX project artifact and deploy metadata from version control to your Salesforce org.

- **[Deploy a Salesforce Package](deploy-salesforce-package.md)** - Learn how to create a service with a Salesforce package artifact and deploy unlocked packages to your Salesforce org.

- **[Back up Salesforce metadata to Git](salesforce-source-backup.md)** - Learn how to back up Salesforce org metadata to a Git repository for version control, disaster recovery, and compliance.

## See also

- [Salesforce Developer Documentation](https://developer.salesforce.com/)
- [Salesforce CLI Command Reference](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/)
- [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/)
