---
description: Learn how to validate and quickly deploy Salesforce metadata using validated deploy request IDs.
tags:
  - salesforce
  - quick-deploy
  - validation
  - metadata-deployment
---

# Salesforce Quick Deploy

{% hint style="info" %}
**Feature Flag**

This feature is behind the feature flag `CDS_ENABLE_SALESFORCE_APPLICATION_DEPLOYMENT`. Contact [Harness Support](mailto:support@harness.io) to enable this feature.
{% endhint %}

This topic explains how to use the Salesforce Validate and Quick Deploy steps to streamline your Salesforce metadata deployments.

Salesforce quick deploy enables you to deploy previously validated changes without re-running tests. This reduces deployment time, especially for large deployments with extensive test suites.

The process works in two stages:

1. **Validate**: First, you validate your metadata changes against the target org. During validation, Salesforce runs all specified tests and generates a **Validated Deploy Request ID** if the validation succeeds.
2. **Quick Deploy**: Using the Validated Deploy Request ID from the validation step, you deploy the same changes to your target org without re-running the tests, as long as the deployment happens within 96 hours of validation.

This approach is useful for production deployments where you want to validate changes during a maintenance window, but deploy them quickly at a scheduled time.

{% hint style="warning" %}
**Test coverage requirement**

Salesforce requires that the validated deployment meets a minimum code coverage threshold (typically 75%) for a quick deploy to succeed. If your validation does not achieve sufficient test coverage, the quick deploy fails. Always ensure your test suite provides adequate coverage during the validation step.
{% endhint %}

{% hint style="info" %}
Quick Deploy is only applicable for metadata-based deployments and is not supported for Salesforce packages.
{% endhint %}

***

## What you will learn from this topic

- How to validate Salesforce metadata changes and generate a Validated Deploy Request ID using the [Salesforce Validate step](#stage-1-salesforce-validate-step).
- How to deploy previously validated changes without re-running tests using the [Salesforce Quick Deploy step](#stage-2-salesforce-quick-deploy-step).
- How to combine both steps into a [complete validate-then-deploy pipeline](#complete-workflow-example).

***

## Before you begin

- **Salesforce deployment setup**: Configure a Salesforce service, environment, and pipeline as described in [Deploy a Salesforce DX project](deploy-salesforce-dx-project.md) or [Deploy a Salesforce package](deploy-salesforce-package.md).
- **Feature flag**: Verify that the `CDS_ENABLE_SALESFORCE_APPLICATION_DEPLOYMENT` feature flag is enabled for your account.

***

## Stage 1: Salesforce Validate step

The first stage in the quick deploy process is to validate your metadata changes. The Salesforce Validate step is similar to the Salesforce Deploy step, but instead of deploying, it only validates the changes and returns a Validated Deploy Request ID.

The validation process runs all specified tests against your metadata changes without deploying them to the target org. If the validation succeeds, Salesforce generates a Validated Deploy Request ID that you can use for quick deployment within the next 96 hours.

![Configuring the Salesforce Validate step](../../../.gitbook/assets/salesforce-validate.png)

### Step parameters

The Salesforce Validate step includes the following configuration parameters:

- **Name**: A unique identifier for the step in your pipeline. This name is used to reference the step in expressions and logs. Example: `SalesforceValidate_1`.
- **Id**: The unique step identifier that you can reference throughout the pipeline. You can use this to access the step's output, such as the Validated Deploy Request ID.
- **Timeout**: The maximum time the step is allowed to run before timing out.
  - **Format**: `10m` (minutes), `1h` (hours).
  - **Default**: `10m`.
  - **Recommendation**: Set a higher timeout for validations that include extensive test suites, especially when using `RunAllTestsInOrg` or `RunLocalTests`.
- **Container Registry**: The container registry where the Salesforce deployment image is stored. You can use:
  - **Harness Image**: Use the pre-configured Harness container registry (`harnessImage`).
  - **Custom Registry**: Select your own Docker registry connector.
- **Image**: The Docker image that contains the Salesforce CLI and deployment tools. Harness provides a pre-built image: `harnessdev/salesforce-plugin:v0.1.62-amd64` (or latest version).
- **Test Level**: The level of testing to perform during validation. This is a critical parameter that determines which tests are executed.

You can select from the following **Test Level** options:

- **NoTestRun**: No tests are run during validation. This is the default if no test level is specified.
  - **Use case**: Sandbox deployments during development.
  - **Warning**: Not suitable for production deployments.
- **RunLocalTests**: Runs all tests in your org that are not from managed packages.
  - **Use case**: Production deployments for most organizations.
  - **Recommended**: Best practice for production deployments.
- **RunAllTestsInOrg**: Runs all tests in your org, including tests from managed packages.
  - **Use case**: Critical production deployments requiring full test coverage.
  - **Warning**: Can significantly increase validation time.
- **RunSpecifiedTests**: Runs only the specific test classes you specify.
  - **Use case**: When you know exactly which tests are relevant to your changes.
  - **Requirement**: You must provide a list of test class names.
- **RunRelevantTests**: Automatically runs only the tests affected by your code changes. Harness analyzes the diff between the current org state and the changes being deployed, then runs tests for components that depend on those changes.
  - **Use case**: Fast, intelligent testing that reduces execution time while maintaining test coverage.
  - **Requirement**: Must be used with Salesforce diff evaluation steps (`SalesforceDownloadOrg`, `SalesforceDownloadDeployables`, `SalesforceEvaluateDiff`) placed earlier in your pipeline.

{% hint style="success" %}
For production deployments, always use `RunLocalTests` or `RunAllTestsInOrg` to ensure your code meets quality standards and does not break existing functionality.
{% endhint %}

### Validation command

Behind the scenes, the Salesforce Validate step uses the following Salesforce CLI command:

```bash
sf project deploy validate --source-dir force-app --target-org <target-org> --test-level <test-level> --json
```

This command validates the metadata without deploying it and returns a Validated Deploy Request ID upon successful validation.

### Validation output

When the validation succeeds, the step outputs a **Validated Deploy Request ID**. You can access this ID in subsequent steps using the following expression:

```text
<+pipeline.stages.<stage_id>.spec.execution.steps.<step_id>.output.validatedDeployRequestId>
```

You can then pass this ID to the Salesforce Quick Deploy step to perform the actual deployment without re-running tests.

***

## Stage 2: Salesforce Quick Deploy step

After successfully validating your metadata changes, use the Salesforce Quick Deploy step to deploy the validated changes to your Salesforce org using the Validated Deploy Request ID from the validation step.

The Quick Deploy step deploys the exact same metadata that was validated, without re-running the tests. This reduces deployment time and results in a faster, more predictable deployment process.

![Configuring the Salesforce Quick Deploy step](../../../.gitbook/assets/sf-quick-deploy.png)

### Step parameters

The Quick Deploy step includes the following configuration parameters:

- **Name**: A unique identifier for the step in your pipeline. This name is used to reference the step in expressions and logs. Example: `SalesforceQuickDeploy_1`.
- **Id**: The unique step identifier that you can reference throughout the pipeline.
- **Timeout**: The maximum time the step is allowed to run before timing out.
  - **Format**: `10m` (minutes), `1h` (hours).
  - **Default**: `10m`.
  - **Recommendation**: Adjust based on the size of your deployment. Quick deploys are typically faster than regular deployments since tests are not re-run.
- **Container Registry**: The container registry where the Salesforce deployment image is stored. You can use:
  - **Harness Image**: Use the pre-configured Harness container registry (`harnessImage`).
  - **Custom Registry**: Select your own Docker registry connector.
- **Image**: The Docker image that contains the Salesforce CLI and deployment tools. Harness provides a pre-built image: `harnessdev/salesforce-plugin:v0.1.62-amd64` (or latest version).
- **Validated Deploy Request ID**: The ID returned from a previous Salesforce validation step. This is the key parameter for quick deploy functionality.

You can provide the **Validated Deploy Request ID** in three ways:

- **Fixed Value**: Enter a specific validated deploy request ID directly.
- **Runtime Input**: Allow the value to be entered when the pipeline runs.
- **Expression**: Reference a value from a previous step or pipeline variable, for example, `<+pipeline.stages.validate.spec.execution.steps.SalesforceValidate_1.output.validatedDeployRequestId>`.

{% hint style="success" %}
When using a validation step in the same pipeline, use an expression to automatically pass the validated deploy request ID from the validation step to the quick deploy step.
{% endhint %}

### Quick Deploy commands

Behind the scenes, the Quick Deploy step uses the following Salesforce CLI commands.

Quick deploy with a specific job ID:

```bash
sf project deploy quick --job-id <validatedDeployRequestId> --target-org <target-org>
```

This command deploys the metadata that was previously validated, using the validated deploy request ID.

Quick deploy with the most recent validation:

```bash
sf project deploy quick --async --use-most-recent --target-org <target-org>
```

This command asynchronously deploys the most recently validated deployment to the specified org.

***

## Complete workflow example

The following is a typical workflow that combines validation and quick deploy across three pipeline stages:

1. **Stage 1: Validate Changes**
   - Download manifests from version control.
   - Run the Salesforce Validate step with `RunLocalTests`.
   - Capture the Validated Deploy Request ID.
2. **Stage 2: Approval** (optional)
   - Add a manual approval step.
   - Review validation results before deploying.
3. **Stage 3: Quick Deploy**
   - Run the Salesforce Quick Deploy step.
   - Use the Validated Deploy Request ID from Stage 1.
   - Deploy to production quickly without re-running tests.

***

## Next steps

- [Deploy a Salesforce DX project](deploy-salesforce-dx-project.md): Deploy Salesforce metadata from version control.
- [Deploy a Salesforce package](deploy-salesforce-package.md): Deploy pre-built unlocked packages.
- [Adding a Harness approval stage](https://developer.harness.io/docs/platform/approvals/adding-harness-approval-stages): Add a manual approval stage between validation and quick deploy.

## See also

- [Salesforce CLI Command Reference - Deploy Quick](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/cli_reference_project_commands_unified.htm#cli_reference_project_deploy_quick_unified)
- [Salesforce Deployment Best Practices](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_deploy_best_practices.htm)
- [Salesforce Test Levels](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_deploy.htm)
