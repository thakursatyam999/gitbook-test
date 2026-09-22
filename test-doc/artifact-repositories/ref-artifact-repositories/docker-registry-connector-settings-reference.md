---
description: This topic provides settings and permissions for the Docker connector.
---

# Docker connector settings reference

You can use the Docker connector to connect to DockerHub, Harbor, Quay, and other Docker V2 compliant container registries, such as [GitHub Container Registry](https://developer.harness.io/docs/continuous-integration/use-ci/build-and-upload-artifacts/build-and-push/build-and-push-to-ghcr/).

This topic provides settings and permissions for the Docker connector.

{% hint style="info" %}
**Docker registry rate limits**

Harness is restricted by the limits of the Docker repo, such as [Docker Hub limits](https://docs.docker.com/docker-hub/download-rate-limit/), for pulling Docker images from Docker repos.
{% endhint %}

{% hint style="info" %}
**Docker registries in cloud platforms**

The Docker connector is platform-agnostic and you can use it to connect to any Docker container registry. Harness also provides support for registries in AWS and Google Cloud Platform (GCP) through [AWS connectors](../../cloud-providers/add-aws-connector.md) and [Google Cloud Platform (GCP) connectors](../../cloud-providers/connect-to-google-cloud-platform-gcp.md).
{% endhint %}

{% hint style="info" %}
**Docker base image connection rate limits**

When you use Docker as a base image connector, select the Docker connector to use for the base image pull. This capability is generally available. Make sure you use the correct Docker registry URL and API version. See the guidance below and the Continuous Integration FAQ on [why Build and Push steps do not support V2 API URLs](https://developer.harness.io/docs/continuous-integration/troubleshooting-and-resources/ci-articles-and-faqs/continuous-integration-faqs/#why-build-and-push-steps-dont-support-v2-api-urls).
{% endhint %}

***

## What you will learn from this topic

- **Create a Docker connector**: How to create the connector using the visual editor or the YAML editor.
- **Connector metadata settings**: How to name and describe the connector.
- **Provider type**: Which Docker registry platforms are supported.
- **Docker registry URL**: How to configure the registry URL for different Docker registry providers.
- **Authentication**: How to authenticate using a username and password or anonymously.
- **Connectivity mode**: How to connect through a Harness Delegate or the Harness Platform.

***

## Create a Docker connector <a href="#create-a-docker-connector" id="create-a-docker-connector"></a>

Perform the following steps to create a Docker connector:

{% tabs %}
{% tab title="Visual editor" %}
1. In Harness, go to **Account Settings**, **Organization Settings**, or **Project Settings**, depending on the [scope](../../../platform-access-control/#permissions-hierarchy-scopes) at which you want to create the connector.
2. Select **Connectors**, select **New Connector**, and then select the **Docker Registry** connector.
3. Configure the Docker connector settings using the guidance provided in the sections below.
4. Select **Save and Continue**, wait for the connectivity test to run, and then select **Finish**.
5. In the list of connectors, make a note of your Docker connector's ID. When you need to reference this connector, use this ID in your pipeline YAML, such as `connectorRef: docker_connector_ID`.
{% endtab %}

{% tab title="YAML editor" %}
You can create Docker connectors in the YAML editor. For example:

```yaml
connector:
  name: My Docker Connector
  identifier: mydockerconnector
  description: ""
  orgIdentifier: default
  projectIdentifier: default
  type: DockerRegistry
  spec:
    dockerRegistryUrl: https://docker.dev.harness.io/v2/
    providerType: DockerHub
    auth:
      type: Anonymous
    executeOnDelegate: true
```
{% endtab %}
{% endtabs %}

***

## Connector metadata settings <a href="#connector-metadata-settings" id="connector-metadata-settings"></a>

The Docker connector has the following metadata settings.

* **Name**: Enter a name for this connector. Harness creates an [ID](../../../references/entity-identifier-reference.md) based on the name.
* **Description**: Optional text string.
* **Tags**: Optional [tags](../../../tags/overview.md#create-tags-for-pipelines).

### Provider type <a href="#provider-type" id="provider-type"></a>

Select the Docker registry platform: **DockerHub**, **Harbor**, **Quay**, or **Other**.

If you select **Other**, the registry must be Docker V2 compliant.

### Docker registry URL <a href="#docker-registry-url" id="docker-registry-url"></a>

The URL of the Docker registry. This is usually the URL used for your [docker login](https://docs.docker.com/engine/reference/commandline/login/) credentials.

* To connect to a public Docker Hub registry, use `https://index.docker.io/v2/`.
* To connect to a private Docker Hub registry, use `https://index.docker.io/v1/`. If you run into authentication issues, such as an anonymous account being used even though a valid Docker registry and credentials are used, go to [why this happens](https://developer.harness.io/docs/continuous-integration/troubleshooting-and-resources/ci-articles-and-faqs/continuous-integration-faqs/#why-build-and-push-steps-dont-support-v2-api-urls).
* For other Docker registries, provide the relevant URL for your container registry provider. For example:
  * For GitHub Container Registry, provide the GHCR hostname and namespace, such as `https://ghcr.io/NAMESPACE`. The namespace is the name of a GitHub personal account or organization.
  * For JFrog Artifactory Docker registries, provide your JFrog instance URL, such as `https://mycompany.jfrog.io`. You can get this URL from the `docker-login` command on your repo's **Set Me Up** page.
  * For Sonatype Nexus Docker registries, provide the Nexus instance URL, such as `<nexus-hostname>:<repository-port>` or `<subdomain>.<nexus-hostname>`. For more information, see the Sonatype Nexus [Docker Authentication](https://help.sonatype.com/en/docker-authentication.html) documentation.

### Harness Artifact Registry configuration <a href="#harness-artifact-registry-configuration" id="harness-artifact-registry-configuration"></a>

When you use the Docker connector with Harness Artifact Registry (HAR), configure the registry URL and image names correctly to avoid validation errors.

* **Correct URL format**: Set the registry URL to `https://pkg.harness.io/`. Avoid including the registry name in the URL to prevent validation errors.
* **Fully qualified image name**: Provide the fully qualified image name within the step configuration, such as `pkg.qa.harness.io/<account-id>/harness/<registry-name>`.
* **Deprecated source type**: If you use a deprecated source type, such as `image` in YAML configurations, update the configuration to the current standard to avoid potential issues. For example, if you previously used `sourceType: image`, update it to the current standard, such as `sourceType: container`.

{% hint style="info" %}
**Policy enforcement and authentication**

**SBOM (Software Bill of Materials) policy enforcement**: Make sure the registry URL is correctly configured to avoid hardcoded URL issues.

**SLSA (Supply-chain Levels for Software Artifacts) verification authentication**: Double-check the authentication settings if you encounter errors.
{% endhint %}

***

## Authentication <a href="#authentication" id="authentication"></a>

You can authenticate anonymously or by username and password.

{% tabs %}
{% tab title="Username and password" %}
* **Username:** Enter the username for your Docker registry account.
* **Password:** Provide a [Harness encrypted text secret](../../../secrets/add-use-text-secrets.md) containing the password or token corresponding with the **Username**.
  * For Docker Hub and GHCR, use a personal access token with **Read, Write, Delete** permissions.
  * For JFrog Docker registries, provide a password.

{% hint style="info" %}
**Docker registry permissions**

Make sure the connected user account has read permission for all repositories, as well as access and permissions to pull images and list images and tags.

For more information, go to the Docker documentation on [Docker Permissions](https://docs.docker.com/datacenter/dtr/2.0/user-management/permission-levels/).
{% endhint %}
{% endtab %}

{% tab title="Anonymous" %}
Select **Anonymous** to pull images from public Docker registries with anonymous access. This option can encounter issues with limits, such as Docker Hub rate limiting.

If you use anonymous access with a Kubernetes deployment, make sure `imagePullSecrets` is removed from the container specification. This is standard Kubernetes behavior and not specific to Harness.
{% endtab %}
{% endtabs %}

***

## Connectivity mode <a href="#connectivity-mode" id="connectivity-mode"></a>

You can connect through a Harness Delegate or the Harness Platform. If you plan to use this connector with [Harness Cloud build infrastructure](https://developer.harness.io/docs/continuous-integration/use-ci/set-up-build-infrastructure/use-harness-cloud-build-infrastructure/), you must select **Connect through Harness Platform**.

{% hint style="info" %}
For private network connectivity options with Harness Cloud, see [Private network connectivity options](../../../references/private-network-connectivity/private-network-connectivity.md).
{% endhint %}

{% hint style="warning" %}
**Limitation**

The Docker connector currently does not support OpenID Connect (OIDC) for authentication, which limits integration with OIDC-compliant identity providers.
{% endhint %}

{% @harness-feedback/feedback module="harness-ai" pagePath="harness-ai/use-harness-platform/connectors/cloud-providers/ref-cloud-providers/docker-registry-connector-settings-reference" %}
