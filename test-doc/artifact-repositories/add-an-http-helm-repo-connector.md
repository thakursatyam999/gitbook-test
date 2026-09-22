---
description: Documentation for the provisioning of HTTP Helm Repository connectors.
---

# Connect to HTTP Helm Repository

You connect Harness to an HTTP Helm repository using a Harness HTTP Helm connector. This connector allows you to pull Helm charts from your repository for use as manifests in Harness pipelines.

This topic shows you how to add an HTTP Helm connector to Harness.

{% hint style="info" %}
**Helm charts use the Manifests section**

Since Harness lets you use the `<+artifact.image>` expression in your Helm Chart Values YAML files, Helm charts are added to a service's **Manifests** section, not its **Artifacts** section. If you use that expression, Harness pulls the image you add to the service's **Artifacts** section. For more information, see [Deploy Helm Charts](../../../../delivery/continuous-delivery/use-continuous-delivery/deploy-services-on-different-platforms/helm/deploy-helm-charts.md).
{% endhint %}

***

## What you will learn from this topic

- How to [add an HTTP Helm connector](#add-an-http-helm-connector) to a Harness project so you can pull Helm charts in a pipeline.

***

## Add an HTTP Helm connector

You can add an HTTP Helm connector at the project, org, or account scope. This procedure covers the project scope, and the process is the same for org and account. You can also add an HTTP Helm connector directly when configuring a Helm chart manifest in a service.

Follow the interactive guide below for a step-by-step walkthrough:

{% embed url="https://app.arcade.software/share/NCkcgaUPwfTl62Oxa1bD" %}
Add an HTTP Helm connector in Harness
{% endembed %}

Perform the following steps to add an HTTP Helm Repo connector:

1. Open your Harness project.
2. In **Project Settings**, select **Connectors**.
3. Click **New Connector**, then click **HTTP Helm Repo**. The HTTP Helm Repo settings appear.

### Overview

4. In **Name**, enter a name for the connector. Optionally, add a description and tags. Click **Continue**.

### Details

5. Enter the **Helm Repository URL**.
6. In **Authentication**, select one of the following options:
   - **Username and Password**: Enter the **Username** for the account. For **Password**, create a new Harness Encrypted Text secret or use an existing one.
   - **Anonymous (no credentials required)**.
   Click **Continue**.

### Delegates Setup

7. In **Delegates Setup**, use any delegate or enter [tags](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md) for the specific delegates that you want to allow to connect to this connector. Click **Save and Continue**.

## Test Connection

8. Click **Finish** after the connection test succeeds. The connector is listed in **Connectors**.

### Sample YAML

The following example shows a YAML for the HTTP Helm Repo connector created in this guide.

<details>

<summary>YAML - HTTP Helm Repo connector</summary>

```yaml
connector:
  name: satyamHelm
  identifier: satyamHelm
  description: ""
  accountIdentifier: youraccountidentifier
  type: HttpHelmRepo
  spec:
    helmRepoUrl: yourhelmrepourl
    auth:
      type: Anonymous
    ignoreTestConnection: false
```

</details>

***

## Next steps

- [Artifact repository connectors](artifact-repositories-connectors-overview.md): learn about the other artifact repository connectors Harness supports.
- [Connect to an OCI Helm registry](add-an-oci-helm-registry-connector.md): connect to a Helm chart repository hosted on an OCI-compliant registry instead of an HTTP server.
- [Deploy Helm Charts](https://developer.harness.io/docs/continuous-delivery/use-continuous-delivery/deploy-services-on-different-platforms/helm/deploy-helm-charts): use the Helm charts pulled by this connector as manifests in a pipeline.
- [Use delegate selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md): control which delegates can connect to this connector.
- [Add a secret manager](../../../troubleshooting-and-resources/tutorials/add-secrets-manager.md): store connector credentials in a secret manager.
