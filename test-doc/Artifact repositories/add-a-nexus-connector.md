---
description: Documentation for the provisioning of Nexus Repository connectors.
---

# Connect to Nexus

You connect Harness to Nexus using a Harness Nexus connector. This connector allows you to pull artifacts stored in your Nexus repository, including Docker, Maven, npm, and NuGet formats, for use in Harness pipelines.

This topic shows you how to add a Nexus connector to Harness.

For details on settings and permissions, see [Nexus Connector Settings Reference](ref-artifact-repositories/nexus-connector-settings-reference.md).

***

## What you will learn from this topic

- How to [add a Nexus connector](#add-a-nexus-connector) to a Harness project so you can pull artifacts in a pipeline.

***

## Add a Nexus connector

You can add a Nexus connector at the project, org, or account scope. This procedure covers the project scope, and the process is the same for org and account. You can also add a Nexus connector directly when configuring the artifact source in a service.

Follow the interactive guide below for a step-by-step walkthrough:

{% embed url="https://app.arcade.software/share/0IYltHvvaF1jnCh4msCu" %}
Add a Nexus connector in Harness
{% endembed %}

Perform the following steps to add a Nexus connector:

1. Open your Harness project.
2. In **Project Settings**, select **Connectors**.
3. Select **New Connector**, then select **Nexus**. The Nexus Repository settings appear.

### Overview

4. In **Name**, enter a name for this connector. Optionally, add a description and tags. Click **Continue**.

### Details 

5. In **Nexus Repository URL**, enter the URL that you use to connect to your Nexus server. For example, `https://nexus3.dev.mycompany.io/repository/your-repo-name`.
6. Select a **Version**.

   {% hint style="info" %}
   **Supported repository formats by Nexus version**

   For Nexus 2.x, Harness supports the Maven, npm, and NuGet repository formats. Go to Sonatype's [Supported Formats](https://help.sonatype.com/repomanager3/nexus-repository-administration/formats) page for details.

   For Nexus 3.x, Harness supports the Docker (3.0 and later), Maven, npm, and NuGet repository formats.
   {% endhint %}

7. In **Authentication**, select one of the following options:
   - **Username and Password**: Enter the **Username** for the account. For **Password**, create a new Harness Encrypted Text secret or use an existing one.
   - **Anonymous (no credentials required)**.
   Click **Continue**.

### Delegates Setup

8. In **Delegates Setup**, use any delegate or enter [tags](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md) for the specific delegates that you want to allow to connect to this connector. Click **Save and Continue**.

### Test Connection

9.  Click **Finish** after the test connection succeeds. The connector is listed in **Connectors**.

### Sample YAML

The following example shows a YAML for the Nexus connector created in this guide.

<details>

<summary>YAML - Nexus connector</summary>

```yaml
connector:
  name: satyam-nexus-connector
  identifier: satyamnexusconnector
  description: ""
  accountIdentifier: youraccountidentifier
  type: Nexus
  spec:
    nexusServerUrl: yournexusserverurl
    version: 3.x
    auth:
      type: UsernamePassword
      spec:
        username: username
        passwordRef: password
    ignoreTestConnection: false
```

</details>

***

## Next steps

- [Artifact repository connectors](artifact-repositories-connectors-overview.md): learn about the other artifact repository connectors Harness supports.
- [Use delegate selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md): control which delegates can connect to this connector.
- [Add a secret manager](../../../troubleshooting-and-resources/tutorials/add-secrets-manager.md): store connector credentials in a secret manager.
