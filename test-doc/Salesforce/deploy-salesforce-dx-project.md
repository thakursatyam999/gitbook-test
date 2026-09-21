---
description: Learn how to deploy a Salesforce DX project using Harness Continuous Delivery (CD).
tags:
  - salesforce
  - salesforce-dx
  - sfdx
  - metadata-deployment
---

# Deploy a Salesforce DX Project

{% hint style="info" %}
**Feature Flag**

This feature is behind the feature flag `CDS_ENABLE_SALESFORCE_APPLICATION_DEPLOYMENT`. Contact [Harness Support](mailto:support@harness.io) to enable this feature.
{% endhint %}

This topic walks you through deploying a Salesforce DX (SFDX) project from version control to a target Salesforce org using Harness Continuous Delivery (CD).

A Salesforce DX project deployment retrieves metadata from your version control system (Git, GitHub, GitLab, and so on) and deploys it to your target Salesforce org using the Salesforce CLI.

## Before you begin

Perform the following steps to prepare for your Salesforce DX project deployment:

- **Salesforce deployments overview**: Review the [Salesforce deployments overview](salesforce-deployments-overview.md) to understand key concepts.
- **Salesforce connector**: Set up a [Salesforce connector](https://developer.harness.io/docs/platform/connectors/cloud-providers/add-a-salesforce-connector) for your target org.
- **Code repository connector**: Set up a version control connector (Git, GitHub, GitLab, and so on) where your SFDX project is stored. For more information, see [Code repository connectors](https://developer.harness.io/docs/platform/connectors/code-repositories).
- **Salesforce environment**: Set up a Salesforce environment and infrastructure definition. Go to [Set up a Salesforce environment](salesforce-deployments-overview.md#set-up-a-salesforce-environment) to configure one.
- **`sfdx-project.json` file**: Ensure your SFDX project has a valid `sfdx-project.json` file at the root.

***

## SFDX project structure

Your Salesforce DX project must follow the standard structure shown below.

```text
my-salesforce-project/
├── force-app/                    # Main source directory
│   └── main/
│       └── default/
│           ├── apex/            # Apex classes/triggers
│           ├── lwc/             # Lightning Web Components
│           ├── objects/         # Custom objects and fields
│           ├── layouts/         # Page layouts
│           ├── workflows/       # Workflow rules
│           ├── flows/           # Flow definitions
│           └── ...              # Other metadata types
├── manifest/
│   └── package.xml              # Metadata list for deployment
├── config/
│   └── project-scratch-def.json # Scratch org configuration
├── sfdx-project.json            # Core project config file
└── README.md
```

The `sfdx-project.json` file must be present at the root of your project.

***

## Create a Salesforce DX service

A Harness service defines what you want to deploy. For DX project deployments, configure a service with a Salesforce DX Project artifact.

### Create the service

Perform the following steps to create the service:

1. In your Harness project, go to **Services**.
2. Click **New Service** to create a new service.
3. Configure the service:
   * Enter a **Name** for the service. Optionally, add a description and tags.
   * Save the configuration.

![Creating a new Salesforce service](../../../.gitbook/assets/sf-service-3.png)

### Add an artifact source

After you create the service, add an artifact source.

Perform the following steps to add the artifact source:

1. In the service, go to the **Configuration** tab.
2. Configure the **Service Definition**:
   * Under **Deployment Type**, select **Salesforce**.
   * Under **Artifacts**, select **Add Artifact Source**.

   ![Adding an artifact source to the Salesforce service](../../../.gitbook/assets/sf-service-2.png)

   * Select **Salesforce DX Project** as the artifact repository type.

   ![Selecting the Salesforce DX Project artifact repository type](../../../.gitbook/assets/sf-service-4.png)

### Select the repository

Choose your version control provider and select a connector:

- **GitHub**: Select an existing GitHub connector or create a new one.
- **Git**: Select an existing Git connector or create a new one.
- **GitLab**: Select an existing GitLab connector or create a new one.
- **Bitbucket**: Select an existing Bitbucket connector or create a new one.

![Selecting a version control repository](../../../.gitbook/assets/sf-service-5.png)

For information on creating code repository connectors, see [Code repository connectors](../../../../../platform/use-harness-platform/connectors/code-repositories/connect-to-code-repo.md).

### Configure the artifact location

Provide the following details to configure the artifact location:

1. **Artifact Source Identifier**: Enter a unique identifier for this artifact source, for example, `gitproject`.
2. **Repository Name**: Enter or select your repository name.
3. **Git Fetch Type**: Select how to fetch the code:
   - **Latest from Branch**: Fetch the latest commit from a specific branch.
   - **Specific Commit ID**: Fetch a specific commit.
   - **Specific Git Tag**: Fetch a specific Git tag.
4. **Branch**: Enter the branch name, for example, `main` or `prod`.
5. **Folder Path**: Enter the path to your Salesforce DX project root, for example, `/` for the root, or `/my-project` for a subfolder.
6. **Select Fetch Type**: Choose how to retrieve metadata:
   * **All**: Deploy all metadata in the project.
   * **Manifest**: Use a `package.xml` file to specify what to deploy.
   * **Inline**: Specify source paths or metadata types directly.
7. **Manifest Path** (if Manifest is selected): Enter the path to your `package.xml` file, for example, `package_diff/package/package.xml`.
8. **Destructive Changes Path** (optional): Enter the path to your `destructiveChanges.xml` file if you need to delete metadata.

![Configuring the artifact location](../../../.gitbook/assets/sf-service-6.png)

9. Click **Submit**.

### Sample service YAML

The following example shows the YAML for the service created in this topic.

<details>

<summary>YAML - Salesforce DX service</summary>

```yaml
service:
  name: salesforce_dx_project
  identifier: salesforce_dx_project
  serviceDefinition:
    type: Salesforce
    spec:
      artifacts:
        primary:
          primaryArtifactRef: <+input>
          sources:
            - identifier: dx_project
              type: SalesforceDxProject
              spec:
                metadataConfigurationType: all
                metadataStore:
                  type: Github
                  spec:
                    repoName: your_repo_name
                    gitFetchType: Branch
                    branch: main
                    folderPath: /
                    connectorRef: git_connector
            - spec:
                packageId: <+input>
                connectorRef: <+input>
                packageVersionId: <+input>
              identifier: your_identifier
              type: SalesforcePackage
  gitOpsEnabled: false
  orgIdentifier: default
  projectIdentifier: your_project_identifier
```

</details>

***

## Create a deployment pipeline

Now that you have a service and environment configured, create a pipeline to deploy your Salesforce DX project.

### Set up the pipeline

Perform the following steps to set up the pipeline:

1. In your Harness project, go to **Pipelines**.
2. Click **Create a Pipeline**.
3. Enter a name for your pipeline. Optionally, add a description and tags.
4. Click **Start**.

![Creating a new pipeline](../../../.gitbook/assets/sf-pipeline-1.png)

5. Click **Add Stage** and select **Deploy** as the stage type.
6. Enter a stage name and select **Salesforce** as the deployment type.

![Selecting the Salesforce deployment type for the stage](../../../.gitbook/assets/sf-pipeline-2.png)

7. Click **Set Up Stage**.

### Configure the service and environment

Perform the following steps to configure the service and environment for the stage:

1. In the **Service** tab, select the Salesforce DX service you created earlier. Click **Continue** to proceed to the **Environment** tab.

![Selecting the Salesforce DX service in the pipeline](../../../.gitbook/assets/sf-pipeline-3.png)

2. In the **Environment** tab, select the environment and infrastructure definition you created earlier.

![Selecting the environment and infrastructure definition](../../../.gitbook/assets/sf-pipeline-4.png)

3. Click **Continue** to proceed to the **Execution** tab.

### Configure the execution strategy

In the **Execution** tab, select the **Dx Project** execution strategy.

![Selecting the Dx Project execution strategy](../../../.gitbook/assets/sf-pipeline-6.png)

This strategy includes two steps:

1. **Download Manifests**: Downloads the metadata from your Git repository that you specified in the service configuration.
2. **Salesforce Deploy**: Deploys the metadata to your target Salesforce org.

![The Download Manifests and Salesforce Deploy steps](../../../.gitbook/assets/sf-pipeline-7.png)

### Configure the Salesforce Deploy step

Harness adds the **Salesforce Deploy** step automatically with the following default configuration:

- **Name**: SalesforceDeploy_1 (or a custom name).
- **Timeout**: 10m.
- **Container Registry**: harnessImage (Harness-provided registry).
- **Image**: `harnessdev/salesforce-plugin:v0.1.62-amd64` (Harness Salesforce plugin).

![Default configuration of the Salesforce Deploy step](../../../.gitbook/assets/sf-pipeline-10.png)

You can customize the timeout and other optional configurations as needed.

### Sample pipeline YAML

The following example shows the YAML for the deployment pipeline created in this topic.

<details>

<summary>YAML - Deployment pipeline</summary>

```yaml
pipeline:
  name: salesforce_dx_project
  identifier: salesforce_dx_project
  projectIdentifier: your_project_identifier
  orgIdentifier: default
  tags: {}
  stages:
    - stage:
        name: s1
        identifier: s1
        description: ""
        type: Deployment
        spec:
          deploymentType: Salesforce
          service:
            serviceRef: salesforce_dx_project
            serviceInputs:
              serviceDefinition:
                type: Salesforce
                spec:
                  artifacts:
                    primary:
                      primaryArtifactRef: <+input>
                      sources: <+input>
          environment:
            environmentRef: salesforce
            deployToAll: false
            infrastructureDefinitions:
              - identifier: salesforce_target
          execution:
            steps:
              - stepGroup:
                  name: Dx Project Deployment
                  identifier: dxProjectDeployment
                  steps:
                    - step:
                        type: DownloadManifests
                        name: DownloadManifests_1
                        identifier: DownloadManifests_1
                        spec: {}
                        timeout: 10m
                    - step:
                        type: SalesforceDeploy
                        name: SalesforceDeploy_1
                        identifier: SalesforceDeploy_1
                        spec:
                          connectorRef: account.harnessImage
                          image: harnessdev/salesforce-plugin:v0.1.62-amd64
                        timeout: 10m
                  stepGroupInfra:
                    type: KubernetesDirect
                    spec:
                      connectorRef: k8s_delegate
                      namespace: harness-delegate-ng
            rollbackSteps: []
        tags: {}
        failureStrategies:
          - onFailure:
              errors:
                - AllErrors
              action:
                type: MarkAsFailure
```

</details>

### Run the pipeline

Perform the following steps to run the pipeline:

1. Click **Save** to save your pipeline.
2. Click **Run** to execute the pipeline.
3. Select the artifact (if configured as a runtime input) and any other runtime inputs.
4. Click **Run Pipeline** to start the deployment.

Harness performs the following steps during the deployment:

1. Fetches the SFDX project from version control.
2. Authenticates to the target Salesforce org.
3. Deploys the metadata using the Salesforce CLI.
4. Monitors the deployment status.
5. Reports success or failure.

***

## Best practices

Follow these best practices to keep your Salesforce DX project deployments reliable and auditable:

- **Use manifest files**: For production deployments, use `package.xml` to have precise control over what gets deployed.
- **Test in sandboxes first**: Always test your deployment in a sandbox environment before deploying to production.
- **Run tests**: Use `RunLocalTests` or `RunAllTestsInOrg` for production deployments to ensure code quality.
- **Version control everything**: Keep all metadata in version control and use branches for different environments.
- **Use descriptive commit messages**: This helps with rollback and tracking changes.
- **Implement approvals**: Add approval steps for production deployments.
- **Monitor deployments**: Use Harness dashboards to monitor deployment success rates and duration.
- **Handle destructive changes carefully**: Use `destructiveChanges.xml` with caution and test thoroughly.

***

## Troubleshooting

<details>

<summary>Deployment fails with an authentication error?</summary>

Verify that:
- The Salesforce connector is configured correctly.
- The credentials have not expired.
- You have the necessary permissions.

</details>

<details>

<summary>Deployment times out?</summary>

- Increase the timeout value in the deployment step.
- Deploy smaller changesets.
- Check Salesforce org performance.

</details>

<details>

<summary>Tests fail?</summary>

- Review the test results in the Harness execution logs.
- Fix failing tests in your codebase.
- Use `NoTestRun` for sandbox deployments during development.

</details>

<details>

<summary>Metadata conflicts occur?</summary>

- Review the error message for conflicting metadata.
- Ensure the target org does not have conflicting customizations.
- Use `ignoreWarnings: true` for non-critical warnings, and use this setting with caution.

</details>

***

## Next steps

- [Deploy a Salesforce Package](deploy-salesforce-package.md): Create a service with a Salesforce package artifact and deploy unlocked packages to your Salesforce org.
- [Harness GitOps basics](../../../use-gitops/get-started/harness-git-ops-basics.md): Learn the core concepts behind Harness GitOps.
- [Pipeline triggers](https://developer.harness.io/docs/platform/triggers/triggers-overview): Learn how to trigger your pipeline automatically.

## See also

- [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/)
- [Salesforce CLI Command Reference](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/)
- [Metadata API Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/)
