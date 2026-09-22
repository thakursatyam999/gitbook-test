---
description: Documentation for the provisioning of Artifactory Repository connectors.
---

# Connect to Artifactory

You connect Harness to Artifactory using a Harness Artifactory connector. This connector allows you to pull artifacts stored in your Artifactory repository for use in Harness pipelines.

This topic shows you how to add an Artifactory connector to Harness.

For details on settings and permissions, see [Artifactory Connector Settings Reference](ref-artifact-repositories/artifactory-connector-settings-reference.md).

***

## What you will learn from this topic

- How to [add an Artifactory connector](#add-an-artifactory-connector) to a Harness project so you can pull artifacts in a pipeline.

***

## Add an Artifactory connector

You can add an Artifactory connector at the project, org, or account scope. This procedure covers the project scope, and the process is the same for org and account. You can also add an Artifactory connector directly when configuring the Artifactory artifact source in a service.

Follow the interactive guide below for a step-by-step walkthrough:

{% embed url="https://app.arcade.software/share/le2HDcHZKjZI67MEIlcb" %}
Add an Artifactory connector in Harness
{% endembed %}

Perform the following steps to add an Artifactory connector:

1. Open your Harness project.
2. In **Project Settings**, select **Connectors**.
3. Click **New Connector**, then click **Artifactory**. The Artifactory Repository settings appear.

### Overview

4. In **Name**, enter a name for the connector. Optionally, add a description and tags. Click **Continue**.

### Details

5. Enter the **Artifactory Repository URL**.
6. In **Authentication**, select one of the following options:
   - **Username and Password**: Enter the **Username** for the account. For **Password**, create a new Harness Encrypted Text secret or use an existing one.
   - **Anonymous (no credentials required)**.
   Click **Continue**.

### Select Connectivity Mode

7. Under **Select Connectivity Mode**, select how you want Harness to connect to Artifactory:
   - **Connect through Harness Platform**: Use a direct, secure communication between Harness and Artifactory.
   - **Connect through a Harness Delegate**: Harness communicates with Artifactory through a Harness Delegate.

### Delegates Setup

8. In **Delegates Setup**, use any delegate or enter [tags](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md) for the specific delegates that you want to allow to connect to this connector. Click **Save and Continue**.

### Test Connection

9.  Click **Finish** after the connection test succeeds. The connector is listed in **Connectors**.

### Sample YAML

<details>

<summary>YAML - Artifactory connector</summary>

```yaml
connector:
  name: TestArtifactory
  identifier: TestArtifactory
  description: ""
  accountIdentifier: youraccountidentifier
  type: Artifactory
  spec:
    artifactoryServerUrl: yourartifactoryurl
    auth:
      type: OidcAuthentication
      spec:
        providerName: testprovider
    executeOnDelegate: true
    proxy: false
    ignoreTestConnection: false
```

</details>

***

## Next steps

- [Use delegate selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md): control which delegates can connect to this connector.
- [Add a secret manager](../../../troubleshooting-and-resources/tutorials/add-secrets-manager.md): store connector credentials in a secret manager.



