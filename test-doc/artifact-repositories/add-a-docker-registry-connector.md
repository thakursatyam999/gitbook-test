---
description: Documentation for the provisioning of Docker Registry connectors.
---

# Connect to Docker Registry

You connect Harness to a Docker container registry using a Harness Docker Registry connector. This connector is platform-agnostic and can be used to connect to any Docker container registry, so you can pull container images for use in Harness pipelines.

This topic shows you how to add a Docker Registry connector to Harness.

For details on settings and permissions, see [Docker Connector Settings Reference](ref-artifact-repositories/docker-registry-connector-settings-reference.md).

{% hint style="info" %}
**AWS and GCP registries**

Harness provides dedicated support for registries in AWS and GCP. For more details, see [Add an AWS Connector](../cloud-providers/add-aws-connector.md) and [Google Cloud Platform (GCP) Connector Settings Reference](../cloud-providers/connect-to-google-cloud-platform-gcp.md). For Azure ACR, use the steps below.
{% endhint %}

***

## What you will learn from this topic

- How to [add a Docker Registry connector](#add-a-docker-registry-connector) to a Harness project so you can pull container images in a pipeline.

***

## Add a Docker Registry connector

You can add a Docker Registry connector at the project, org, or account scope. This procedure covers the project scope, and the process is the same for org and account. You can also add a Docker Registry connector directly when configuring the artifact source in a service.

Follow the interactive guide below for a step-by-step walkthrough:

{% embed url="https://app.arcade.software/share/VNCOOedBAC6dytNvRSfO" %}
Add a Docker Registry connector in Harness
{% endembed %}

Perform the following steps to add a Docker Registry connector:

1. Open your Harness project.
2. In **Project Settings**, select **Connectors**.
3. Click **New Connector**, then click **Docker Registry**. The Docker Registry settings appear.

### Overview

4. In **Name**, enter a name for the connector. Optionally, add a description and tags. Click **Continue**.

### Details

5. Select a **Provider Type**.
6. Enter the **Docker Registry URL**.
7. In **Authentication**, select one of the following options:
   - **Username and Password**: Enter the **Username** for the account. For **Password**, create a new Harness Encrypted Text secret or use an existing one.
   - **Anonymous (no credentials required)**.
   Click **Continue**.

### Select Connectivity Mode

8. Under **Select Connectivity Mode**, select how you want Harness to connect to Docker:
   - **Connect through Harness Platform**: Use a direct, secure communication between Harness and Docker.
   - **Connect through a Harness Delegate**: Harness communicates with Docker through a Harness Delegate.

### Delegates Setup

9.  In **Delegates Setup**, use any delegate or enter [tags](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md) for the specific delegates that you want to allow to connect to this connector. Click **Save and Continue**.

### Connection Test

10. Click **Finish** after the connection test succeeds. The connector is listed in **Connectors**.

### Sample YAML

The following example shows a YAML for the Docker Registry connector created in this guide.

<details>

<summary>YAML - Docker Registry connector</summary>

```yaml
connector:
  name: satyam-docker-connector
  identifier: satyamdockerconnector
  description: ""
  accountIdentifier: youraccountidentifier
  type: DockerRegistry
  spec:
    dockerRegistryUrl: yourdockerregistryurl
    providerType: DockerHub
    auth:
      type: UsernamePassword
      spec:
        username: username
        passwordRef: password
    executeOnDelegate: true
    proxy: false
    ignoreTestConnection: false
```

</details>

***

## Next steps

- [Connect to the Harness container image registry](./connect-to-harness-container-image-registry-using-docker-connector.md): Use a Docker Registry connector with the Harness Container Image Registry.
- [Connect to IBM Cloud Container Registry](./using-ibm-registry-to-create-a-docker-connector.md): Use a Docker Registry connector with the IBM Cloud Container Registry.

