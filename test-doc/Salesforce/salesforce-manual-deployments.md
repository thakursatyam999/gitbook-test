---
description: Learn how to perform manual Salesforce deployments with diff evaluation and cherry-picking capabilities.
tags:
  - salesforce
  - salesforce-dx
  - manual-deployment
  - diff
  - cherry-pick
---

# Salesforce manual deployments

{% hint style="info" %}
**Feature Flag**

This feature is behind the feature flag `CDS_ENABLE_SALESFORCE_APPLICATION_DEPLOYMENT`. Contact [Harness Support](mailto:support@harness.io) to enable this feature.
{% endhint %}

This topic walks you through manual Salesforce deployments in Harness. Manual deployments enable you to compare metadata between different sources, evaluate differences, and selectively deploy changes to your target Salesforce org.

Manual deployments give you fine-grained control over what gets deployed. Instead of deploying everything at once, you can download metadata from multiple sources, evaluate the differences between them, and select specific changes before pushing to your target org. This approach is useful when you need to compare sandbox and production environments, selectively promote changes between orgs, or create custom deployment packages that combine metadata from different sources.

***

## What you will learn from this topic

- How to understand the [manual deployment workflow](#what-are-manual-salesforce-deployments) and when to use it.
- How to configure the three [manual deployment steps](#manual-deployment-steps): Download Org, Download Deployables, and Evaluate Diff.
- How to [create a manual deployment pipeline](#create-a-manual-deployment-pipeline) with the correct step group infrastructure.
- How to [access and filter the diff output](#understanding-the-diff-output) to selectively deploy changes.

***

## What are manual Salesforce deployments?

Manual Salesforce deployments follow a four-step workflow. First, you download metadata from two different sources, which can be Salesforce orgs, Git repositories, or Harness deployables. Next, you evaluate the differences between these sources to identify which metadata components were added, updated, or deleted. Then you select the changes you want to deploy, and finally you deploy only those selected changes to your target org.

This workflow is designed for scenarios where automated deployments do not provide enough control. You might need to compare metadata between sandbox and production orgs before deploying, selectively promote specific changes from one org to another, thoroughly review and validate changes before they go live, or create custom deployment packages by combining metadata from multiple sources.

***

## Before you begin

Perform the following steps to prepare for manual deployments:

- **Salesforce deployments overview**: Review the [Salesforce deployments overview](salesforce-deployments-overview.md) to understand key concepts.
- **Source and target connectors**: Set up [Salesforce connectors](https://developer.harness.io/docs/platform/connectors/cloud-providers/add-a-salesforce-connector) for both your source and target orgs.
- **Version control connector**: Set up a version control connector (Git, GitHub, GitLab, and so on) if you are using Git repositories as sources. For more information, see [Code repository connectors](https://developer.harness.io/docs/platform/connectors/code-repositories).
- **Salesforce environment**: Set up a Salesforce environment and infrastructure definition. For more information, see [Set up a Salesforce environment](salesforce-deployments-overview.md#set-up-a-salesforce-environment).
- **Permissions**: Verify that you have the necessary permissions on both source and target Salesforce orgs to read and deploy metadata.

***

## Manual deployment steps

Manual deployments in Harness use three specialized steps that work together to download, compare, and prepare metadata for deployment.

### Salesforce Download Org

The **Salesforce Download Org** step downloads metadata directly from a Salesforce org using the Salesforce CLI. You specify which metadata types to download, such as Apex classes, custom objects, or page layouts, and the step retrieves only those components. This targeted approach reduces download time and makes comparisons more efficient.

Use this step when you need to download metadata from any Salesforce org, whether a sandbox, production org, or scratch org. The step requires a Salesforce connector pointing to your source org, a list of metadata types you want to download, and the Salesforce plugin Docker image that contains the CLI tools. You also need to specify a download path where you want to store the downloaded metadata.

![Configuring the Salesforce Download Org step](../../../.gitbook/assets/sf-download-org-config.png)

### Salesforce Download Deployables

The **Salesforce Download Deployables** step is more versatile. It can download metadata from multiple source types and prepare them for comparison. Unlike the single-source Download Org step, this step handles two sources simultaneously. It supports Salesforce orgs (using connectors), Git repositories (DX projects from version control), and Harness deployables (previously staged metadata).

This flexibility means you can compare any combination of sources. Compare two Salesforce orgs to see what is different between sandbox and production. Compare an org against staged metadata to validate what is about to be deployed. Compare an org against version control to see which changes have not been pushed yet. Or compare two different branches or repositories to understand what changed between releases.

The step configuration includes source types and references for both sources, the metadata types to download and compare, and the same container registry and image settings as the Download Org step. The step downloads both sources in parallel and prepares them for the subsequent diff evaluation.

![Configuring the Salesforce Download Deployables step](../../../.gitbook/assets/sf-download-deployables-config.png)

### Salesforce Evaluate Diff

The **Salesforce Evaluate Diff** step takes the metadata downloaded by the previous steps and performs a detailed comparison. It uses Git-based comparison to identify every change between the two sources, and categorizes each component as added, updated, or deleted.

Added components are new metadata that exists in the second source but not the first, for example, a new Apex class or custom field. Updated components exist in both sources but have different content, indicating a validation rule modification or a workflow rule update. Deleted components exist in the first source but not the second, indicating they were removed.

The step runs quickly because it only needs the container registry and image configuration. The metadata is already downloaded by the previous steps. The timeout setting controls how long the comparison can run before failing, which matters when comparing large metadata sets.

![Configuring the Salesforce Evaluate Diff step](../../../.gitbook/assets/sf-evaluate-diff-config.png)

The diff output is structured as JSON and available as a step outcome. Each changed component includes its path, the type of change (add, update, or delete), and the actual differences in content. You can access this output in subsequent steps using Harness expressions, use it to conditionally execute deployment steps, or send it to external systems for approval workflows.

![Example Salesforce Evaluate Diff step output](../../../.gitbook/assets/sf-evaluate-diff-output.png)

***

## Create a manual deployment pipeline

Setting up a manual deployment pipeline follows the standard Harness pipeline creation flow, with a few Salesforce-specific configurations in the execution steps.

### Set up the pipeline

Perform the following steps to set up the pipeline:

1. In your Harness project, go to **Pipelines**.
2. Select **Create a Pipeline**.
3. Enter a name for your pipeline. Optionally, add a description and tags.
4. Click **Start**.

![Creating a new pipeline](../../../.gitbook/assets/sf-pipeline-1.png)

5. Click **Add Stage** and choose **Deploy** as the stage type.
6. Enter a stage name and select **Salesforce** as the deployment type.

![Configuring the Deploy stage type](../../../.gitbook/assets/sf-pipeline-2.png)

7. Click **Set Up Stage**.

### Configure the service and environment

Perform the following steps to configure the service and environment:

1. In the **Service** tab, select the Salesforce service. If you are comparing against a DX project source, select the appropriate artifact.

![Selecting the Salesforce service](../../../.gitbook/assets/sf-pipeline-3.png)

2. In the **Environment** tab, select your target environment and infrastructure definition. This defines where the final deployment goes once you have evaluated the differences and decided what to deploy.

![Selecting the environment and infrastructure definition](../../../.gitbook/assets/sf-pipeline-4.png)

3. Click **Continue** to proceed to the **Execution** tab to configure the manual deployment steps.

### Add the manual deployment steps

The execution configuration is where you add the three steps that make up the manual deployment workflow. Add these steps in sequence within the same step group so they can share the downloaded metadata through a common filesystem.

![Manual deployment steps added to the execution strategy](../../../.gitbook/assets/sf-download-org-pipeline.png)

Perform the following steps to add the download source org metadata step:

1. Click **Add Step** and select **Salesforce Download Org**.
2. Enter a **Name** for the step, for example, `Download Source Org`.
3. Set the **Container Registry** to `harnessImage` to use the Harness-provided registry.
4. Set the **Image** to `harnessdev/salesforce-dx-project-deploy:v0.1.47-amd64`.
5. Under **Source Reference**, select the Salesforce connector that points to your source org. This is typically a sandbox connector if you are comparing sandbox to production.
6. Add the **Metadata Types** you want to download.
7. Enter the **Download Path** where you want to store the downloaded metadata.
8. Select **Apply Changes** to save the step configuration.

Perform the following steps to add the download deployables for comparison step:

1. Click **Add Step** and select **Salesforce Download Deployables**.
2. Enter a **Name** for this step, for example, `Download Deployables`. This step downloads metadata from two sources and prepares them for comparison.
3. Use the same container registry and image settings from the **Salesforce Download Org** step.
4. Set **Source 1 Type** to org and select your first source connector for **Source 1 Reference**.
5. For **Source 2 Type**, choose deployable if you are comparing against previously staged metadata, or org if you are doing an org-to-org comparison.
6. Select the appropriate reference for **Source 2 Reference**.
7. Add the **Metadata Types** you want to compare. Use the same metadata types from the **Salesforce Download Org** step to ensure consistency across your workflow.
8. Click **Apply Changes** to save this step.

Perform the following steps to add the evaluate differences step:

1. Click **Add Step** and select **Salesforce Evaluate Diff**.
2. Enter a **Name** for this step, for example, `Evaluate Diff`. This step has fewer configuration options since it operates on the metadata downloaded by the previous steps.
3. Use the same container registry and image settings from the **Salesforce Download Org** and **Salesforce Download Deployables** steps.
4. Click **Apply Changes** to complete the step configuration.

![Overview of the manual deployment pipeline steps](../../../.gitbook/assets/sf-manual-pipeline-overview.png)

### Add a deployment step (optional)

After evaluating the diff, you can add a **Salesforce Deploy** step to deploy changes to your target org. To deploy only specific components based on the diff results, you can use a Shell Script step before the deployment to parse the diff output and create a filtered `package.xml` manifest. Access the diff data using the expression `<+pipeline.stages.STAGE_ID.spec.execution.steps.STEP_GROUP_ID.steps.EVALUATE_DIFF_STEP_ID.output.outputVariables.diffResult>` and use tools like `jq` to filter the components you want to deploy.

### Configure the step group infrastructure

All three manual deployment steps must run within a step group that has Kubernetes infrastructure configured. This is because the steps execute as containerized plugins that need a Kubernetes cluster to run.

Select the step group that contains your manual deployment steps. In the **Step Group Infrastructure** section, select **Kubernetes Direct** as the infrastructure type. Choose your Kubernetes connector from the dropdown. This should point to the cluster where your Harness delegates are running. Enter the namespace where the steps should execute, such as `default` or `harness-delegate-ng` depending on your cluster configuration.

### Sample pipeline YAML

The following example shows the YAML for the manual deployment pipeline created in this topic.

<details>

<summary>YAML - Manual deployment pipeline</summary>

```yaml
# Replace the following placeholders with your actual values:
# - your_project: Your Harness project identifier
# - your_service: Your Salesforce service identifier
# - your_environment: Your Salesforce environment identifier
# - your_infrastructure: Your Salesforce infrastructure definition identifier
# - your_source_connector: Your Salesforce source org connector
# - your_target_connector: Your Salesforce target org connector (for org-to-org comparison)
# - your_k8s_connector: Your Kubernetes cluster connector

pipeline:
  name: salesforceManualDeployment
  identifier: salesforceManualDeployment
  projectIdentifier: your_project
  orgIdentifier: default
  tags: {}
  stages:
    - stage:
        name: Manual Deployment
        identifier: manual_deployment
        description: ""
        type: Deployment
        spec:
          deploymentType: Salesforce
          service:
            serviceRef: your_service
            serviceInputs:
              serviceDefinition:
                type: Salesforce
                spec:
                  artifacts:
                    primary:
                      primaryArtifactRef: <+input>
                      sources: <+input>
          environment:
            environmentRef: your_environment
            deployToAll: false
            infrastructureDefinitions:
              - identifier: your_infrastructure
          execution:
            steps:
              - stepGroup:
                  name: Dx Project Deployment
                  identifier: dxProjectDeployment
                  steps:
                    - step:
                        type: SalesforceDownloadOrg
                        name: SalesforceDownloadOrg_1
                        identifier: SalesforceDownloadOrg_1
                        spec:
                          connectorRef: account.harnessImage
                          image: harnessdev/salesforce-dx-project-deploy:v0.1.47-amd64
                          sourceRef: your_source_connector
                          metadataTypes:
                            - ApexClass
                            - CustomObject
                            - Profile
                            - Layout
                        timeout: 10m
                    - step:
                        type: SalesforceDownloadDeployables
                        name: SalesforceDownloadDeployables_1
                        identifier: SalesforceDownloadDeployables_1
                        spec:
                          connectorRef: account.harnessImage
                          source1Type: org
                          source1Ref: your_source_connector
                          source2Type: org
                          source2Ref: your_target_connector
                          metadataTypes:
                            - ApexClass
                            - CustomObject
                            - Profile
                            - Layout
                          image: harnessdev/salesforce-dx-project-deploy:v0.1.47-amd64
                        timeout: 10m
                    - step:
                        type: SalesforceEvaluateDiff
                        name: SalesforceEvaluateDiff_1
                        identifier: SalesforceEvaluateDiff_1
                        spec:
                          connectorRef: account.harnessImage
                          image: harnessdev/salesforce-dx-project-deploy:v0.1.47-amd64
                        timeout: 10m
                  stepGroupInfra:
                    type: KubernetesDirect
                    spec:
                      connectorRef: your_k8s_connector
                      namespace: default
            rollbackSteps: []
        tags: {}
        failureStrategies:
          - onFailure:
              errors:
                - AllErrors
              action:
                type: StageRollback
```

</details>

***

## Metadata types

Harness supports downloading and comparing the following Salesforce metadata types: ApexClass (Apex classes), ApexTrigger (Apex triggers), CustomObject (custom objects), CustomField (custom fields), Layout (page layouts), Profile (user profiles), CustomApplication (custom applications), CustomTab (custom tabs), Flow (flow definitions), Workflow (workflow rules), ValidationRule (validation rules), and PermissionSet (permission sets).

You can specify as many metadata types as you need in each download step. The evaluation step compares all the metadata types you downloaded, making it easy to see exactly what changed across your entire metadata set.

***

## Understanding the diff output

The Salesforce Evaluate Diff step produces a structured JSON output that describes every change between your two sources. Each changed component includes several key pieces of information.

The `diffType` field tells you whether the component was added, updated, or deleted. An "add" means the component exists in source B but not in source A: it is new metadata. An "update" means the component exists in both sources but the content has changed. A "delete" means the component exists in source A but not in source B: it was removed.

The `path` field shows exactly where the component lives in the metadata structure, for example, `force-app/main/default/classes/MyClass.cls` for an Apex class. The `diff` array contains the actual differences, showing what changed in the source code or configuration. Each diff entry includes the content from both sources and the line numbers where the differences occur, making it easy to understand exactly what changed.

<details>

<summary>Example diff output structure</summary>

```json
{
  "diffType": "add|update|delete",
  "path": "path/to/component.ext",
  "diff": [
    {
      "sourceA": "content from source A",
      "sourceB": "content from source B",
      "startLineA": 10,
      "startLineB": 12
    }
  ]
}
```

</details>

### Accessing the diff output

The diff output is available as a step output variable that you can reference in subsequent pipeline steps. Access the diff results using:

```text
<+pipeline.stages.STAGE_ID.spec.execution.steps.STEP_GROUP_ID.steps.EVALUATE_DIFF_STEP_ID.output.outputVariables.diffResult>
```

You can use this expression in conditional execution logic to decide whether to proceed with deployment, in approval step configurations to show reviewers what will be deployed, or in Shell Script steps to filter which components to deploy. The structured JSON format makes it easy to parse and process the diff data with tools like `jq`.

***

## Next steps

- [Deploy a Salesforce DX project](deploy-salesforce-dx-project.md): Deploy Salesforce metadata from version control.
- [Deploy a Salesforce package](deploy-salesforce-package.md): Deploy pre-built unlocked packages.
- Learn about [Harness expressions](https://developer.harness.io/docs/platform/variables-and-expressions/harness-variables) to reference step outputs across your pipeline.

## See also

- [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/)
- [Salesforce CLI Command Reference](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/)
- [Metadata API Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/)
