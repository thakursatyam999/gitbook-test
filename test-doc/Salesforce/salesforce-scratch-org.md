---
description: Learn how to create and delete Salesforce scratch orgs for development and testing.
tags:
  - salesforce
  - scratch-org
  - development
  - testing
---

# Salesforce Scratch Org Management

{% hint style="info" %}
**Feature Flag**

This feature is behind the feature flag `CDS_ENABLE_SALESFORCE_APPLICATION_DEPLOYMENT`. Contact [Harness Support](mailto:support@harness.io) to enable this feature.
{% endhint %}

This topic explains how to create and delete Salesforce scratch orgs using Harness Continuous Delivery (CD).

A scratch org is a temporary, fully configurable Salesforce environment that is created on-demand and discarded when the work is complete. Scratch orgs are ideal for development, testing, and continuous integration workflows.

***

## What you will learn from this topic

- How to provision a temporary Salesforce environment using the [Create Scratch Org step](#create-scratch-org-step).
- How to remove a scratch org and free up DevHub resources using the [Delete Scratch Org step](#delete-scratch-org-step).
- How to reference scratch org outputs, such as the authentication URL, between pipeline steps.
- How to structure a [complete workflow example](#complete-workflow-example) that creates, uses, and deletes a scratch org.

***

## Before you begin

- **Salesforce deployments overview**: Review the [Salesforce deployments overview](salesforce-deployments-overview.md) to understand key concepts.
- **DevHub connector**: Set up a Salesforce connector for your DevHub org. Scratch orgs are provisioned through a DevHub org, which acts as the parent account.
- **Salesforce environment**: Set up a Salesforce environment and infrastructure definition. For more information, see [Set up a Salesforce environment](salesforce-deployments-overview.md#set-up-a-salesforce-environment).
- **Scratch org definition file**: Have a scratch org definition file (typically `project-scratch-def.json`) available in your repository. For more information, see [Definition file path](#definition-file-path).

***

## Scratch org characteristics

A scratch org has the following characteristics:

- **Temporary**: Scratch orgs have a lifespan ranging from 1 to 30 days, after which they automatically expire.
- **Configurable**: You can configure a scratch org using a definition file that specifies features, settings, and metadata.
- **Isolated**: Each scratch org is a clean environment that does not copy metadata or settings from your default org.
- **DevHub-based**: Scratch orgs are provisioned through a DevHub org, which acts as the parent account.

Scratch orgs are particularly useful for the following use cases:

- **Feature development**: Developers can work in isolated environments without affecting shared sandboxes.
- **Automated testing**: Create fresh orgs for each test run in CD pipelines.
- **Pull request validation**: Validate code changes in a clean environment before merging.
- **Training and demos**: Quickly set up temporary environments for training sessions.

{% hint style="info" %}
A scratch org does not copy metadata or settings from your default org. Harness uses the DevHub as the parent account to provision the scratch org.
{% endhint %}

***

## Create Scratch Org step

The Create Scratch Org step provisions a new temporary Salesforce environment on-demand. Harness creates the scratch org against the Salesforce org (DevHub) provided in your infrastructure definition.

![Create Scratch Org step configuration](../../../.gitbook/assets/sf-create-scratch-org.png)

### Step parameters

The Create Scratch Org step includes the following configuration parameters:

- **Name**: A unique identifier for the step in your pipeline, used to reference the step in expressions and logs. Example: `SalesforceCreateScratchOrg_1`.
- **Id**: The unique step identifier that can be referenced throughout the pipeline. You can use this to access the step's output, such as the scratch org authentication details.
- **Timeout**: The maximum time the step is allowed to run before it times out.
  - **Format**: `10m` (minutes), `1h` (hours).
  - **Default**: `10m`.
  - **Recommendation**: Scratch org creation typically completes within a few minutes, but complex configurations may take longer.
- **Container Registry**: The container registry where the Salesforce deployment image is stored. You can use:
  - **Harness Image**: Use the pre-configured Harness container registry (`harnessImage`).
  - **Custom Registry**: Select your own Docker registry connector.
- **Image**: The Docker image that contains the Salesforce CLI and deployment tools. Harness provides a pre-built image: `harnessdev/salesforce-plugin:v0.1.62-amd64` (or latest version).
- **Duration Days**: Controls the lifespan of the scratch org, and determines how long the scratch org remains active before it automatically expires.
  - **Range**: 1 to 30 days.
  - **Format**: Integer value, for example, `7`, `15`, `30`.
  - **Recommendation**: Use 1-7 days for CD automated testing, and 14-30 days for feature development work.
  - **Required**: Yes.
- **Scratch Org Name** (optional): The scratch org's display name, visible in your DevHub. This provides a human-readable name to help identify the scratch org.
  - **Example**: `Feature_Auth_Dev`, `PR_123_Test_Org`, `Sprint_15_Demo`.
  - **Optional**: Yes.
  - **Recommendation**: Use descriptive names that include the purpose, branch name, or ticket number for easy identification.
  - If not provided, Salesforce generates a default name based on the scratch org's username.
- **Definition File Path**: Specifies the path to the scratch org definition file in your repository. The definition file (typically `project-scratch-def.json`) tells Salesforce which blueprint to use when building the scratch org.
  - **Format**: Relative path from your project root.
  - **Example**: `config/project-scratch-def.json`, `scratch-orgs/dev-config.json`.
  - **Required**: Yes.
  - **Note**: This file must be available in your repository, and Harness downloads it using the Download Manifests step.
- **Username** (optional): Assigns a specific username to the scratch org. This is useful when you need predictable usernames for testing or automation.
  - **Format**: Email-like format, for example, `test-user@company.org.scratch`.
  - **Optional**: Yes.
  - **Default behavior**: If not provided, Salesforce automatically generates a unique username.

The definition file can specify:

- Salesforce edition (Developer, Enterprise, and so on).
- Features to enable (Communities, PersonAccounts, and so on).
- Org settings and preferences.
- Sample data and metadata.

The following example shows a scratch org definition file:

```json
{
  "orgName": "My Scratch Org",
  "edition": "Developer",
  "features": ["EnableSetPasswordInApi", "Communities"],
  "settings": {
    "lightningExperienceSettings": {
      "enableS1DesktopEnabled": true
    }
  }
}
```

{% hint style="warning" %}
After the duration expires, Salesforce automatically deletes the scratch org, and it cannot be recovered.
{% endhint %}

{% hint style="success" %}
When using scratch orgs in CD pipelines, you can use expressions to generate unique usernames based on pipeline execution IDs or branch names.
{% endhint %}

### Create command

Behind the scenes, the Create Scratch Org step uses the following Salesforce CLI command:

```bash
sf org create scratch --definition-file <path> --duration-days <days> --target-dev-hub <devhub-org> [--username <username>] [--alias <name>]
```

### Step outputs

When Harness successfully creates the scratch org, the step outputs authentication information that you can use in subsequent steps:

- **Scratch Org Username**: The username assigned to the scratch org.
- **Scratch Org Auth URL**: The SFDX auth URL that can be used to authenticate to the scratch org.
- **Scratch Org ID**: The unique identifier for the scratch org.

You can reference these outputs using expressions such as:

```text
<+pipeline.stages.<stage_id>.spec.execution.steps.<step_id>.output.username>
<+pipeline.stages.<stage_id>.spec.execution.steps.<step_id>.output.authUrl>
```

***

## Delete Scratch Org step

Scratch orgs are short-lived and automatically expire after their duration, but they can stay active until the expiration period ends. The Delete Scratch Org step lets you manually delete a scratch org when it is no longer needed, helping you manage your DevHub's scratch org allocation limits.

![Delete Scratch Org step configuration](../../../.gitbook/assets/sf-delete-scratch-org.png)

### Step parameters

The Delete Scratch Org step includes the following configuration parameters:

- **Name**: A unique identifier for the step in your pipeline, used to reference the step in expressions and logs. Example: `SalesforceDeleteScratchOrg_1`.
- **Id**: The unique step identifier that can be referenced throughout the pipeline.
- **Timeout**: The maximum time the step is allowed to run before it times out.
  - **Format**: `10m` (minutes), `1h` (hours).
  - **Default**: `10m`.
  - **Recommendation**: Scratch org deletion typically completes within seconds.
- **Container Registry**: The container registry where the Salesforce deployment image is stored. You can use:
  - **Harness Image**: Use the pre-configured Harness container registry (`harnessImage`).
  - **Custom Registry**: Select your own Docker registry connector.
- **Image**: The Docker image that contains the Salesforce CLI and deployment tools. Harness provides a pre-built image: `harnessdev/salesforce-plugin:v0.1.62-amd64` (or latest version).
- **Scratch Org Auth URL** (optional): The SFDX authentication URL for the scratch org you want to delete. You can provide this parameter in multiple ways:
  - **From Create Step**: Use an expression to reference the auth URL from a previous Create Scratch Org step, for example, `<+pipeline.stages.create.spec.execution.steps.SalesforceCreateScratchOrg_1.output.authUrl>`.
  - **Runtime Input**: Provide the auth URL at runtime.
  - **Fixed Value**: Enter a specific auth URL if known.
  - **Leave Empty**: If not provided, the step uses the auth URL from the Create Scratch Org step output to identify and delete the scratch org.

{% hint style="success" %}
When creating and deleting scratch orgs in the same pipeline, use an expression to automatically pass the auth URL from the Create step to the Delete step.
{% endhint %}

### Delete command

Behind the scenes, the Delete Scratch Org step uses the following Salesforce CLI command:

```bash
sf org delete scratch --target-org <scratch-org> --no-prompt
```

To delete a scratch org, the Salesforce CLI performs the following actions:

1. Authenticates to the DevHub org, obtained from the infrastructure definition.
2. Authenticates to the scratch org, using the provided auth URL or infrastructure.
3. Executes the delete command.

### When to delete scratch orgs

Consider deleting scratch orgs in the following scenarios:

- **After test completion**: Clean up test environments immediately after automated tests finish.
- **On pipeline failure**: Remove scratch orgs created for failed pipelines to free up resources.
- **Development workflow**: Delete scratch orgs when switching between feature branches.
- **Resource management**: Proactively manage your DevHub's scratch org limits.

***

## Complete workflow example

The following workflow shows a typical sequence that creates, uses, and deletes a scratch org:

1. **Stage 1: Create Scratch Org**
   - Download manifests from version control.
   - Create a scratch org with a 7-day duration.
   - Capture the scratch org auth details.
2. **Stage 2: Deploy and Test**
   - Deploy metadata to the scratch org.
   - Run automated tests.
   - Validate functionality.
3. **Stage 3: Cleanup**
   - Delete the scratch org.
   - Free up DevHub resources.

This workflow ensures that each pipeline run has a fresh, isolated environment for testing, and Harness cleans up resources immediately after use.

***

## Next steps

- [Deploy a Salesforce DX project](deploy-salesforce-dx-project.md): Deploy Salesforce metadata from version control to a scratch org or target org.
- [Salesforce Quick Deploy](salesforce-quick-deploy.md): Learn about quick deployment options for Salesforce metadata.
- [Define a failure strategy on stages and steps](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-on-stages-and-steps): Configure how your pipeline responds to failures during scratch org creation or deletion.

## See also

- [Salesforce DX Developer Guide - Scratch Orgs](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_scratch_orgs.htm)
- [Scratch Org Definition Configuration](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_scratch_orgs_def_file.htm)
- [Salesforce CLI Command Reference - Org Commands](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/cli_reference_org_commands_unified.htm)
