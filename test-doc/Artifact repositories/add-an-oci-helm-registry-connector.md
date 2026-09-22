---
description: Documentation for the provisioning of OCI Helm Registry connectors.
---

# Connect to OCI Helm Registry

You connect Harness to an OCI-compliant registry using a Harness OCI Helm Registry connector. This connector allows you to pull Helm charts stored as [OCI artifacts](https://helm.sh/docs/topics/registries/) in registries such as ACR, GCR, or ECR, for use as manifests in Harness pipelines.

This topic shows you how to add an OCI Helm Registry connector to Harness.

{% hint style="info" %}
**Helm charts use the Manifests section**

Since Harness lets you use the `<+artifact.image>` expression in your Helm Chart Values YAML files, Helm charts are added to a service's **Manifests** section, not its **Artifacts** section. If you use that expression, Harness pulls the image you add to the service's **Artifacts** section. For more information, see [Deploy Helm Charts](https://developer.harness.io/docs/continuous-delivery/use-continuous-delivery/deploy-services-on-different-platforms/helm/deploy-helm-charts).
{% endhint %}

***

## What you will learn from this topic

- How to [add an OCI Helm Registry connector](#add-an-oci-helm-registry-connector) to a Harness project so you can pull Helm charts stored in an OCI-compliant registry in a pipeline.

***

## Add an OCI Helm Registry connector

You can add an OCI Helm Registry connector at the project, org, or account scope. This procedure covers the project scope, and the process is the same for org and account. You can also add an OCI Helm Registry connector directly when configuring a Helm chart manifest in a service.

Follow the interactive guide below for a step-by-step walkthrough:

{% embed url="https://app.arcade.software/share/oJXPTn5cFbZlTHWgY6ky" %}
Add an OCI Helm Registry connector in Harness
{% endembed %}


Perform the following steps to add an OCI Helm Registry connector:

1. Open your Harness project.
2. In **Project Settings**, select **Connectors**.
3. Click **New Connector**, then click **OCI Helm Registry** in **Artifact Repositories**. The OCI Helm Registry settings appear.

### Overview

4. In **Name**, enter a name for this connector. Optionally, add a description and tags. Click **Continue**.

### Details

5. Enter the **Helm Repository URL**.

   {% hint style="info" %}
   **Supported URL formats**

   The OCI Helm connector supports the following URL formats:

   - URL without the `oci://` prefix, for example, `public.ecr.aws`.
   - URL with the `oci://` prefix, for example, `oci://public.ecr.aws`.
   - URL with a port number, for example, `public.ecr.aws:443`.
   - URL with the `oci://` prefix and a port number, for example, `oci://public.ecr.aws:443`.
   {% endhint %}

6. In **Authentication**, select one of the following options:
   - **Username and Password**: Enter the **Username** for the account. For **Password**, create a new Harness Encrypted Text secret or use an existing one.
   - **Anonymous (no credentials required)**.
   Click **Continue**.

### Delegates Setup 

7. In **Delegates Setup**, use any delegate or enter [tags](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md) for the specific delegates that you want to allow to connect to this connector. Click **Save and Continue**.

### Test Connection

8. Click **Finish** after the connection test succeeds. The connector is listed in **Connectors**.

### Sample YAML

The following example shows a YAML for the OCI Helm Registry connector created in this guide.

<details>

<summary>YAML - OCI Helm Registry connector</summary>

```yaml
connector:
  name: satyam-oci-helm
  identifier: satyamocihelm
  description: ""
  accountIdentifier: youraccountidentifier
  orgIdentifier: default
  projectIdentifier: yourprojectidentifier
  type: OciHelmRepo
  spec:
    helmRepoUrl: yourhelmrepourl
    auth:
      type: Anonymous
    delegateSelectors:
      - satyam-helm-delegate
    ignoreTestConnection: false
```

</details>

***

## OCI registry notes

Keep the following notes in mind when you use an OCI Helm registry connector:

- Helm officially supports OCI registries for Helm version 3.8 and above. Experimental support is available in versions below 3.8.
- You cannot use OCI Helm registries with [Helm Chart Triggers](../../triggers/trigger-pipelines-on-new-helm-chart.md).
- Harness OCI support is cloud-agnostic, so you can use OCI registries in ACR, GCR, and ECR.

***

## Provider-specific authentication

### Google GCR

For GCR as an OCI registry, Harness supports authentication using:

* An access token.
* A JSON key file, where the username is `_json_key_base64` and the password is the base64-encoded JSON key file content.

Harness does not support a username of `_json_key` with an unencrypted JSON key file content as the password.

### AWS ECR

For **Helm Repository URL**, enter the URL for the repo in the format `https://<aws_account_id>.dkr.ecr.<region>.amazonaws.com`. For example: `https://0838475738302113.dkr.ecr.us-west-2.amazonaws.com`.

For **Username**, enter `AWS`.

For **Password**, create a new Harness text secret using the token retrieved with:

```bash
aws ecr get-login-password --region <region>
```

For example: `aws ecr get-login-password --region us-west-2`. Copy the output and paste it into a Harness text secret.

{% hint style="warning" %}
**AWS ECR token expiration**

The AWS ECR authorization token is valid for only 12 hours, which is an [AWS limitation](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ecr/get-login-password.html#description). You need to refresh the secret regularly to avoid interruptions. For more information, see AWS's [Private registry authentication](https://docs.aws.amazon.com/AmazonECR/latest/userguide/registry_auth.html) documentation.
{% endhint %}

***

## Next steps

- [Artifact repository connectors](artifact-repositories-connectors-overview.md): learn about the other artifact repository connectors Harness supports.
- [Connect to an HTTP Helm repository](add-an-http-helm-repo-connector.md): connect to a Helm chart repository hosted on a plain HTTP server instead of an OCI-compliant registry.
- [Deploy Helm Charts](https://developer.harness.io/docs/continuous-delivery/use-continuous-delivery/deploy-services-on-different-platforms/helm/deploy-helm-charts): use the Helm charts pulled by this connector as manifests in a pipeline.
- [Use delegate selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md): control which delegates can connect to this connector.
- [Add a secret manager](../../../troubleshooting-and-resources/tutorials/add-secrets-manager.md): store connector credentials in a secret manager.
