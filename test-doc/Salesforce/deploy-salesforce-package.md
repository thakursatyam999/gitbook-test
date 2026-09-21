---
description: Learn how to deploy Salesforce unlocked packages using Harness Continuous Delivery (CD).
tags:
  - salesforce
  - salesforce-packages
  - unlocked-packages
---

# Deploy a Salesforce Package

{% hint style="info" %}
**Feature Flag**

This feature is behind the feature flag `CDS_ENABLE_SALESFORCE_APPLICATION_DEPLOYMENT`. Contact [Harness Support](mailto:support@harness.io) to enable this feature.
{% endhint %}

This topic walks you through deploying Salesforce unlocked packages using Harness Continuous Delivery (CD).

Salesforce packages let you bundle metadata and code for distribution and deployment. Harness supports deploying unlocked packages (2GP) to target Salesforce orgs.

***

## Before you begin

Perform the following steps to prepare for deploying your Salesforce packages:

- **Salesforce deployments overview**: Review the [Salesforce deployments overview](salesforce-deployments-overview.md) to understand key concepts.
- **Target org connector**: Set up a [Salesforce connector](https://developer.harness.io/docs/platform/connectors/cloud-providers/add-a-salesforce-connector) for your target org.
- **DevHub connector**: Set up a Salesforce connector for your DevHub org, where packages are registered.
- **Salesforce environment**: Set up a Salesforce environment and infrastructure definition. For more information, see [Set up a Salesforce environment](salesforce-deployments-overview.md#set-up-a-salesforce-environment).
- **Package version**: Have an existing package with a package version ID ready to deploy.

***

## Unlocked packages (2GP)

Unlocked packages are ideal for the following use cases:

- Internal application development.
- Modular development and CI/CD workflows.
- Version-controlled, source-driven development.
- Deployments that support rollback.

Unlocked packages have the following key features:

- Fully source-driven (Git-first).
- Optional namespace.
- Support for dependencies.
- CLI automation.
- Installation key is optional.

***

## Deployment workflow

A Salesforce package deployment in Harness involves the following steps:

1. **Create a Salesforce service**: Specify the package version to deploy in a service artifact.
2. **Create a deployment pipeline**: Install the package to your target Salesforce org.

***

## Create a Salesforce Package service

A Harness service defines what you want to deploy. For package deployments, you configure a service with a Salesforce Package artifact.

### Create the service

Perform the following steps to create the service:

1. In your Harness project, go to **Services**.
2. Click **New Service** to create a new service.
3. Configure the service:
   * Enter a **Name** for the service, for example, `salesforce_package_service`. Optionally, add a description and tags.
   * Save the configuration.

![Creating a new Salesforce Package service](../../../.gitbook/assets/sf-service-3.png)

### Add an artifact source

After creating the service, add an artifact source.

Perform the following steps to add an artifact source:

1. In the service, go to the **Configuration** tab.
2. Configure the **Service Definition**:
   * Under **Deployment Type**, select **Salesforce**.
   * Under **Artifacts**, select **Add Artifact Source**.

![Adding an artifact source to the Salesforce service](../../../.gitbook/assets/sf-service-2.png)

   * Select **Salesforce Package** as the artifact repository type.

![Selecting Salesforce Package as the artifact repository type](../../../.gitbook/assets/sf-service-10.png)

### Select the Salesforce connector

Select the **Salesforce connector** that points to your DevHub org where the package is registered, or create a new one.

![Selecting the Salesforce connector for the DevHub org](../../../.gitbook/assets/sf-service-8.png)

For information on creating Salesforce connectors, see [Add a Salesforce connector](https://developer.harness.io/docs/platform/connectors/cloud-providers/add-a-salesforce-connector).

### Configure package details

Perform the following steps to configure the package details:

![Configuring the Salesforce package details](../../../.gitbook/assets/sf-service-9.png)

1. **Artifact Source Identifier**: Enter a unique identifier for this artifact source, for example, `package`.
2. **Package Id**: Enter or select the Salesforce package ID, for example, `04tgK00000037o9QAA`. This is the 04t ID that identifies your package.
3. **Package Version Id**: Enter or select the specific package version ID, for example, `04tgK00000037o9QAA`. This identifies the exact version of the package to deploy.
4. **Installation Key Ref** (optional): If your package requires an installation key, configure a secret reference, for example, `<salesforce-token>`. This is used for password-protected packages.
5. Click **Submit**.

### Sample service YAML

The following example shows the YAML for the service created in this topic.

<details>

<summary>YAML - Salesforce Package service</summary>

```yaml
service:
  name: salesforcePackage
  identifier: salesforcePackage
  serviceDefinition:
    type: Salesforce
    spec:
      artifacts:
        primary:
          primaryArtifactRef: <+input>
          sources:
            - spec:
                packageId: package_id
                packageVersionId: package_version_id
                connectorRef: salesforce_connector
              identifier: package
              type: SalesforcePackage
  gitOpsEnabled: false
  orgIdentifier: default
  projectIdentifier: project_identifier
```

</details>

***

## Create a deployment pipeline

Now that you have a service and environment configured, create a pipeline to deploy your Salesforce package.

### Set up the pipeline

Perform the following steps to set up the pipeline:

1. In your Harness project, go to **Pipelines**.
2. Click **Create a Pipeline**.
3. Enter a name for your pipeline. Optionally, add a description and tags.
4. Click **Start**.

![Creating a new pipeline](../../../.gitbook/assets/sf-pipeline-1.png)

5. Click **Add Stage** and select **Deploy** as the stage type.
6. Enter a stage name and select **Salesforce** as the deployment type.

![Configuring the Deploy stage type](../../../.gitbook/assets/sf-pipeline-2.png)

7. Click **Set Up Stage**.

### Configure the service and environment

Perform the following steps to configure the service and environment:

1. In the **Service** tab, select the Salesforce package service you created earlier. Click **Continue** to proceed to the **Environment** tab.

![Selecting the Salesforce package service](../../../.gitbook/assets/sf-pipeline-3.png)

2. In the **Environment** tab, select the environment and infrastructure definition you created earlier.

![Selecting the environment and infrastructure definition](../../../.gitbook/assets/sf-pipeline-4.png)

3. Click **Continue** to proceed to the **Execution** tab.

### Configure the execution strategy

In the **Execution** tab, select the **Package** execution strategy.

![Selecting the Package execution strategy](../../../.gitbook/assets/sf-pipeline-8.png)

This strategy includes a single step:

- **Salesforce Deploy**: Installs the package to your target Salesforce org.

![Salesforce Deploy step added to the execution strategy](../../../.gitbook/assets/sf-pipeline-9.png)

### Salesforce Deploy step configuration

The **Salesforce Deploy** step is automatically added with the following default configuration:

- **Name**: `SalesforceDeploy_1` (or custom name).
- **Timeout**: `10m`.
- **Container Registry**: `harnessImage` (Harness-provided registry).
- **Image**: `harnessdev/salesforce-plugin:v0.1.62-amd64` (Harness Salesforce plugin).

![Default configuration of the Salesforce Deploy step](../../../.gitbook/assets/sf-pipeline-10.png)

You can customize the timeout and other optional configurations as needed.

### Sample pipeline YAML

The following example shows the YAML for the deployment pipeline created in this topic.

<details>

<summary>YAML - Salesforce Package deployment pipeline</summary>

```yaml
pipeline:
  projectIdentifier: <project_identifier>
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
            serviceRef: salesforcePackage
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
                  name: Package Deployment
                  identifier: packageDeployment
                  steps:
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
                type: StageRollback
  identifier: salesforce_package_deployment
  name: salesforce_package_deployment
```

</details>

### Run the pipeline

Perform the following steps to run the pipeline:

1. Click **Save** to save your pipeline.
2. Click **Run** to execute the pipeline.
3. Select the artifact (if configured as runtime input) and any other runtime inputs.
4. Click **Run Pipeline** to start the deployment.

Harness performs the following actions during the deployment:

1. Authenticates to the target Salesforce org.
2. Installs the package version.
3. Monitors the installation status.
4. Reports success or failure.

***

## Best practices

Follow these best practices to keep your Salesforce package deployments reliable and maintainable:

- **Use semantic versioning**: Follow semantic versioning principles (major.minor.patch) for package versions.
- **Test in sandboxes first**: Always test package installations in sandbox environments before deploying to production.
- **Manage installation keys securely**: Store installation keys as Harness secrets and rotate them regularly.
- **Document package dependencies**: Keep track of package dependencies and installation order.
- **Version control your source**: Keep package source code in version control with clear branching strategies.
- **Automate package creation**: Use CI/CD pipelines to automatically create package versions on code changes.
- **Monitor package installations**: Use Harness dashboards to track installation success rates and duration.
- **Handle namespace carefully**: Choose your namespace carefully, as it cannot be changed.
- **Plan for upgrades**: Design your package with upgradability in mind.

***

## Next steps

- [Deploy a Salesforce DX Project](deploy-salesforce-dx-project.md): Create a service with a Salesforce DX project artifact and deploy metadata from version control to your Salesforce org.
- [Harness secrets management](https://developer.harness.io/docs/platform/secrets/secrets-management/harness-secret-manager-overview): Store installation keys and other sensitive values as Harness secrets.
- [Pipeline triggers](https://developer.harness.io/docs/platform/triggers/triggers-overview): Automatically trigger a package deployment pipeline on a code or artifact event.

## See also

- [Salesforce Packaging Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_dev2gp.htm)
- [Salesforce CLI Command Reference](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/)
- [Second-Generation Packaging](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_dev2gp_create_pkg.htm)
