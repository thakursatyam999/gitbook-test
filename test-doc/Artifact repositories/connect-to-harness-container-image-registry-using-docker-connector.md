---
description: There are multiple ways to pull required Harness images.
---

# Connect to the Harness container image registry

When you run a Harness pipeline, the Harness Delegate makes an anonymous outbound connection, through a [Docker connector](ref-artifact-repositories/docker-registry-connector-settings-reference.md), to pull the required Harness images used for backend processes, such as [Harness CI images](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/harness-ci.md), from the public registry where they are stored.

By default, Harness uses the built-in Harness Image Docker connector (Id: `harnessImage`) with anonymous access to pull these images from a public Docker Hub container registry. This topic shows you four ways to modify that default behavior.

***

## What you will learn from this topic

- How to [pull images anonymously from GAR or ECR](#pull-images-anonymously-from-gar-or-ecr).
- Why to [always use credentials instead of anonymous access](#configure-harness-to-always-use-credentials-to-pull-harness-images).
- How to [use credentials to pull Harness images for specific stages](#use-credentials-to-pull-harness-images-for-specific-stages).
- How to [pull Harness images from a private registry](#pull-harness-images-from-a-private-registry).

***

## Before you begin

Confirm the following before you configure how Harness pulls its container images:

- **Permissions**: To configure any of the options in this topic, you need [permissions](../../platform-access-control/permissions-reference.md) to create, edit, and view connectors at the account [scope](../../platform-access-control/#permissions-hierarchy-scopes).

{% hint style="info" %}
**RATE LIMITING**

To prevent rate limiting or throttling issues when pulling images, configure the built-in Harness Image Docker connector to use credentials (instead of anonymous access) and pull images from GAR or ECR (instead of Docker Hub). For instructions, see [Configure Harness to always use credentials to pull Harness images](connect-to-harness-container-image-registry-using-docker-connector.md#configure-harness-to-always-use-credentials-to-pull-harness-images).

If you use anonymous access on Harness Cloud build infrastructure, use GAR to avoid ECR Public's anonymous pull limit. Public ECR enforces this limit per source IP, and Harness Cloud runners run on GCP, so anonymous ECR pulls see the same limit as any other anonymous caller. This limit does not apply to credentialed ECR access. Go to [Pull images anonymously from GAR or ECR](connect-to-harness-container-image-registry-using-docker-connector.md#pull-images-anonymously-from-gar-or-ecr) for details.
{% endhint %}

***

## Which option do you need?

Use the following table to match your situation to the option that addresses it:

| Situation | Scope of change | Go to |
|---|---|---|
| Hitting Docker Hub rate limits and want to stay anonymous | Account-wide | [Pull anonymously from GAR or ECR](#pull-images-anonymously-from-gar-or-ecr) |
| Security policy disallows anonymous pulls entirely | Account-wide | [Always use credentials](#configure-harness-to-always-use-credentials-to-pull-harness-images) |
| Only one build infrastructure/stage cannot pull anonymously (e.g., private cloud) | Single stage | [Use credentials for specific stages](#use-credentials-to-pull-harness-images-for-specific-stages) |
| Need full control over image provenance, or air-gapped environment | Account-wide | [Pull from a private registry](#pull-harness-images-from-a-private-registry) |

All four options are built on the same underlying action: **create or edit a Docker connector.** The steps below cover that shared procedure once. Each option after it only calls out what is different (registry URL, auth type, and where the connector lives).

## Create or edit a Docker connector

Use this procedure for any of the four options. Where a step differs by option, it is called out inline in that option's section below.

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

### Select connectivity mode

8. Under **Select Connectivity Mode**, select how you want Harness to connect to Docker:
   - **Connect through Harness Platform**: Use a direct, secure communication between Harness and Docker.
   - **Connect through a Harness Delegate**: Harness communicates with Docker through a Harness Delegate.

### Delegate setup

9.  In **Delegates Setup**, use any delegate or enter [tags](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md) for the specific delegates that you want to allow to connect to this connector. Click **Save and Continue**.

### Connection test

10. Click **Finish** after the connection test succeeds. The connector is listed in **Connectors**.


## Pull images anonymously from GAR or ECR <a href="#pull-images-anonymously-from-gar-or-ecr" id="pull-images-anonymously-from-gar-or-ecr"></a>

By default, Harness pulls Harness images from GAR with anonymous access. You can also pull Harness images with anonymous access from GAR or ECR. This option changes the behavior for your entire account by editing the configuration of the built-in **Harness Docker Connector**. This is useful if you experience rate limiting issues when pulling from Docker Hub.

{% hint style="warning" %}
**ECR DOES NOT AVOID RATE LIMITING ON HARNESS CLOUD**

If you use [Harness Cloud build infrastructure](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/use-harness-cloud-build-infrastructure.md), switching anonymous access to ECR does not resolve rate limiting. Public ECR enforces its own fixed anonymous pull limit per source IP, and Harness Cloud runners run on GCP, so they receive no AWS-side preferential treatment and hit the same limit as any other anonymous caller. GAR is the only anonymous option that avoids this limit on Harness Cloud.
{% endhint %}

If you do not want to change the behavior for your entire account, follow the steps in [Use credentials to pull Harness images for specific stages](connect-to-harness-container-image-registry-using-docker-connector.md#use-credentials-to-pull-harness-images-for-specific-stages) to modify the behavior for specific stages only.

1. Go to **Account Settings**. Under **Account-level Resources**, select **Connectors**.
2.  Select the **harnessImage Connector** (Id: `harnessImage`).

    If there is no connector with the `harnessImage` identifier in your Account, you need to [create a Docker connector](ref-artifact-repositories/docker-registry-connector-settings-reference.md) with the exact **Id** of `harnessImage`. Harness gives precedence to the connector with the `harnessImage` identifier and uses it to pull the images.

3. Click **Edit Details**.

### Overview

4. Click **Continue** to go to the **Details** settings.

### Details

5. For **Provider Type** and **Docker Registry URL**, do one of the following:

    * To pull [Harness images from GAR](https://us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate), select **Other (Docker V2 compliant)** for Provider Type, and then enter `https://us-docker.pkg.dev/gar-prod-setup/harness-public` for Docker Registry URL.
    * To pull [Harness images from ECR](https://gallery.ecr.aws/harness), select **Other (Docker V2 compliant)** for Provider Type, and then enter `https://public.ecr.aws/harness` for Docker Registry URL.

    If you want to change the connector back to Docker Hub, select **Docker Hub** and enter `https://registry.hub.docker.com`.

6. For **Authentication**, select **Anonymous**. You can use anonymous access to pull Harness images from GAR, ECR, or Docker Hub. Click **Continue**

### Select connectivity mode

7. Under **Select Connectivity Mode**, configure the connector to connect through a Harness Delegate or the Harness Platform.
   * To use this connector with [Harness Cloud build infrastructure](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/use-harness-cloud-build-infrastructure.md), select **Connect through Harness Platform**.
   * If you select **Connect through a Harness Delegate**, you can allow Harness to use any available delegate or specify delegates based on tags. For more information about how Harness selects delegates, go to [Delegate overview](../../delegates/delegate/delegate-concepts/delegate-overview.md) and [Use delegates selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md).
   * For delegate installation instructions, go to [Delegate installation overview](../../delegates/delegate/install-delegates/overview.md).
Click **Save and Continue**.

### Connection test

8.  After the connectivity test succeeds, click **Finish**.


{% hint style="info" %}
**NOTE**

Harness now supports anonymous access to all public Docker registries, including Amazon ECR and Artifactory Public. You can now pull images without requiring authentication. If you use Harness Cloud build infrastructure, go to [Pull images anonymously from GAR or ECR](connect-to-harness-container-image-registry-using-docker-connector.md#pull-images-anonymously-from-gar-or-ecr) to understand rate limiting behavior before choosing a registry.
{% endhint %}

## Configure Harness to always use credentials to pull Harness images <a href="#configure-harness-to-always-use-credentials-to-pull-harness-images" id="configure-harness-to-always-use-credentials-to-pull-harness-images"></a>

If you do not want to connect anonymously, you can configure Harness to always use credentials, instead of anonymous access, to pull the Harness images. This option changes the behavior for your entire account by editing the credentials of the built-in **Harness Docker Connector**. This is useful if your organization's security policies do not allow anonymous connections to public image repos.

If you do not want to change the behavior for your entire account, you can [Use credentials to pull Harness images for specific stages](connect-to-harness-container-image-registry-using-docker-connector.md#use-credentials-to-pull-harness-images-for-specific-stages).

1. Go to **Account Settings**. Under **Account-level Resources**, select **Connectors**.
2.  Select the **harnessImage Connector** (Id: `harnessImage`).

    If there is no connector with the `harnessImage` identifier in your Account, you need to [create a Docker connector](ref-artifact-repositories/docker-registry-connector-settings-reference.md) with the exact **Id** of `harnessImage`. Harness gives precedence to the connector with the `harnessImage` identifier and uses it to pull the images.

3. Click **Edit Details**.

### Overview

4. Click **Continue** to go to the **Details** settings.

### Details

5.  For **Provider Type** and **Docker Registry URL**, do one of the following:

    * To pull [Harness images from GAR](https://us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate), select **Other (Docker V2 compliant)** for Provider Type, and then enter `https://us-docker.pkg.dev/gar-prod-setup/harness-public` for Docker Registry URL.
    * To pull [Harness images from ECR](https://gallery.ecr.aws/harness), select **Other (Docker V2 compliant)** for Provider Type, and then enter `https://public.ecr.aws/harness` for Docker Registry URL.
   * To pull images from Docker Hub, select **Docker Hub** and enter `https://registry.hub.docker.com`.
6. For **Authentication**, select **Username and Password**, and provide a username and token to access Docker Hub or GAR, depending on the **Docker Registry URL**. The token needs **Read, Write, and Delete** permissions. Click **Continue**.

### Select connectivity mode

7. Under **Select Connectivity Mode**, configure the connector to connect through a Harness Delegate or the Harness Platform.
   * To use this connector with [Harness Cloud build infrastructure](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/use-harness-cloud-build-infrastructure.md), select **Connect through Harness Platform**.
   * If you select **Connect through a Harness Delegate**, you can allow Harness to use any available delegate or specify delegates based on tags. For more information about how Harness selects delegates, go to [Delegate overview](../../delegates/delegate/delegate-concepts/delegate-overview.md) and [Use delegates selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md).
   * For delegate installation instructions, go to [Delegate installation overview](../../delegates/delegate/install-delegates/overview.md).
Click **Save and Continue**.

### Connection test
8. After the connectivity test succeeds, click **Finish**.

    If the connectivity test fails, make sure your connector's credentials are configured correctly and that the token has the necessary permissions.


## Use credentials to pull Harness images for specific stages <a href="#use-credentials-to-pull-harness-images-for-specific-stages" id="use-credentials-to-pull-harness-images-for-specific-stages"></a>

If you do not want to connect anonymously, you can configure Harness to use credentials, instead of anonymous access, to pull the Harness images for specific stages in your pipelines. This option lets you override the Harness image pull behavior in individual [Build stages](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/prep-ci-pipeline-components.md#stages) by creating a dedicated [Docker connector](ref-artifact-repositories/docker-registry-connector-settings-reference.md) you can use for these specific use cases. This is useful when the delegate for that stage's build infrastructure cannot anonymously access the public repo. For example, if the build infrastructure is running in a private cloud.

If you want to change the behavior for your entire account, you can [configure Harness to always use credentials to pull Harness images](connect-to-harness-container-image-registry-using-docker-connector.md#configure-harness-to-always-use-credentials-to-pull-harness-images).

1.  Go to **Account Settings**. Under **Account-level Resources**, select **Connectors**.

    Although you will select the connector at the stage scope, you must create the Docker connector at the account scope.
2.  Click **New Connector**, and select **Docker Registry** under **Artifact Repositories**.

    ![](../../../.gitbook/assets/using-ibm-registry-to-create-a-docker-connector-72.png)

### Overview

3.  In **Name**, enter a name for the connector. Optionally, add a description and tags. 

    Harness automatically creates an **Id** ([entity identifier](../../references/entity-identifier-reference.md)) based on the **Name**. You can edit the **Id** while you create the connector. After you save the connector, the **Id** cannot be changed.

    ![](../../../.gitbook/assets/connect-to-harness-container-image-registry-using-docker-connector-47.png)
4. Click **Continue**.

### Details

5. For **Provider Type** and **URL**, do one of the following:
   * To pull [Harness images from GAR](https://us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate), select **Other (Docker V2 compliant)** for **Provider Type**, and then enter `https://us-docker.pkg.dev/gar-prod-setup/harness-public` for **Docker Registry URL**.
   * To pull [Harness images from ECR](https://gallery.ecr.aws/harness), select **Other (Docker V2 compliant)** for **Provider Type**, and then enter `https://public.ecr.aws/harness` for **Docker Registry URL**.
   * To pull images from Docker Hub, select **Docker Hub** and enter `https://registry.hub.docker.com`.
6. For **Authentication**, select **Username and Password**, and provide a username and token to access GAR, ECR, or Docker Hub. The token needs **Read, Write, and Delete** permissions. Click **Continue**

### Select connectivity mode

7. In **Select Connectivity Mode**, configure the connector to connect through a Harness Delegate or the Harness Platform.
   * To use this connector with [Harness Cloud build infrastructure](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/use-harness-cloud-build-infrastructure.md), select **Connect through Harness Platform**.
   * If you select **Connect through a Harness Delegate**, you can allow Harness to use any available delegate or specify delegates based on tags. For more information about how Harness selects delegates, go to [Delegate overview](../../delegates/delegate/delegate-concepts/delegate-overview.md) and [Use delegates selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md).
   * For delegate installation instructions, go to [Delegate installation overview](../../delegates/delegate/install-delegates/overview.md).
Click **Save and Continue**.

### Connection test

8. After the connectivity test succeeds, click **Finish**.

    If the connectivity test fails, make sure your connector's credentials are configured correctly and that the token has the necessary permissions.

9.  In the **Build** stage, where you want to use your Docker connector, go to the [Infrastructure settings](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/ci-stage-settings.md#infrastructure), and select your Docker connector in the **Override Image Connector** field.

    When the pipeline runs, Harness will use the specified connector to download Harness images.

    ![](../../../.gitbook/assets/connect-to-harness-container-image-registry-using-docker-connector-49.png)

## Pull Harness images from a private registry <a href="#pull-harness-images-from-a-private-registry" id="pull-harness-images-from-a-private-registry"></a>

Harness CI images are stored in a public container registry. If you do not want to pull the images directly from the public registry, you can download the images you need, perform any necessary security checks, upload them to your private registry, and then configure your CI pipelines to pull the Harness CI images from your private registry.

You can also [use a private registry for STO scanner images](../../../../security/application-security-testing/security-testing-orchestration/troubleshooting-and-resources/sto-use-cases/set-up-sto-pipelines/configure-pipeline-to-use-sto-images-from-private-registry.md).

### Download Harness images to your registry <a href="#download-harness-images-to-your-registry" id="download-harness-images-to-your-registry"></a>

1.  Download the images you need from the [Harness project on GAR](https://us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate) or the [Harness ECR public gallery](https://gallery.ecr.aws/harness), perform any tests or validations necessary for your organization's security policies, and then store the images in your private registry.

    {% hint style="warning" %}
    Do not change the image names in your private registry. The image names must match the names specified by Harness.
    {% endhint %}

2. **Recommended:** [Specify the images to use in your pipelines](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/harness-ci.md#specify-the-harness-ci-images-used-in-your-pipelines). This is recommended especially if your registry automatically downloads the latest images from the public Harness registry. This ensures your pipelines use specific image versions that you have validated, rather than automatically using the latest version. You must update this specification when you want to adopt a new version of an image.

### Create a Docker connector for your registry <a href="#create-a-docker-connector-for-your-registry" id="create-a-docker-connector-for-your-registry"></a>

Create a [Docker connector](ref-artifact-repositories/docker-registry-connector-settings-reference.md) that connects to your private registry.

1. Go to **Account Settings**. Under **Account-level Resources**, select **Connectors**. You must create the Docker connector at the account scope.
2. Click **New Connector**, and select **Docker Registry** under **Artifact Repositories**.

### Overview

3.  In **Name**, enter a name for the connector. Optionally, add a description and tags. 

    Harness automatically creates an **Id** ([entity identifier](../../references/entity-identifier-reference.md)) based on the **Name**. You can edit the **Id** while you create the connector. After you save the connector, the **Id** cannot be changed.
4. Click **Continue**.

### Details

5. For **Provider Type**, select **Other (Docker V2 compliant)**.
6. For **Docker Registry URL**, enter the path for your container registry.
7. For **Authentication**, select **Username and Password**, and provide a username and token to access your registry. The token needs **Read, Write, and Delete** permissions. Click **Continue**.

### Select connectivity mode

8. Under **Select Connectivity Mode**, configure the connector to connect through a Harness Delegate or the Harness Platform.
   * To use this connector with [Harness Cloud build infrastructure](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/use-harness-cloud-build-infrastructure.md), select **Connect through Harness Platform**.
   * If you select **Connect through a Harness Delegate**, you can allow Harness to use any available delegate or specify delegates based on tags. For more information about how Harness selects delegates, go to [Delegate overview](../../delegates/delegate/delegate-concepts/delegate-overview.md) and [Use delegates selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md).
   * For delegate installation instructions, go to [Delegate installation overview](../../delegates/delegate/install-delegates/overview.md). Click **Save and Continue**.

### Connection test

9. After the connectivity test succeeds, click **Finish**.

    If the connectivity test fails, make sure your connector's credentials are configured correctly and that the token has the necessary permissions.
10. Configure your pipelines to download Harness images from your private registry. In each **Build** stage where you want to pull from your private registry, go to the [Infrastructure settings](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/ci-stage-settings.md#infrastructure), and select your Docker connector in the **Override Image Connector** field.

When the pipeline runs, Harness will use the specified connector to download images from your private registry.

![](../../../.gitbook/assets/connect-to-harness-container-image-registry-using-docker-connector-49.png)

### Authentication considerations for Amazon ECR private repositories <a href="#authentication-considerations-for-amazon-ecr-private-repositories" id="authentication-considerations-for-amazon-ecr-private-repositories"></a>

You can configure a Docker connector in Harness to authenticate and pull images from your private registry. You can either create your own Docker connector or use the built-in account-level Harness Docker connector (`harnessImage`). When configuring a Docker connector to access an Amazon ECR private repository, you must provide a username and password for authentication. According to [AWS documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/registry_auth.html#registry-auth-token), the authentication token obtained via the `aws ecr get-login-password` command is valid for 12 hours. Therefore, to maintain uninterrupted access, you need to update the connector with a new token every 12 hours. This requirement applies only to private ECR repositories. For public ECR repositories, you can configure the connector to use anonymous access, which does not require token-based authentication.

## Connector selection hierarchy <a href="#connector-selection-hierarchy" id="connector-selection-hierarchy"></a>

When selecting the connector to use to pull images, Harness follows this hierarchy:

1. Check for a connector specified at the stage level, such as when [pulling Harness images from a private registry](connect-to-harness-container-image-registry-using-docker-connector.md#pull-harness-images-from-a-private-registry) or [using credentials to pull Harness images for specific stages](connect-to-harness-container-image-registry-using-docker-connector.md#use-credentials-to-pull-harness-images-for-specific-stages).
2. If there is no stage-level connector, use the account-level Harness Image connector (ID: `account.harnessImage`), which can use the default anonymous access configuration or you can configure it to [always use credentials to pull Harness images](connect-to-harness-container-image-registry-using-docker-connector.md#configure-harness-to-always-use-credentials-to-pull-harness-images).

## End of life notice: app.harness Docker registry <a href="#end-of-life-notice-appharness-docker-registry" id="end-of-life-notice-appharness-docker-registry"></a>

[Harness images](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/harness-ci.md) are available on Docker Hub, the [Harness project on GAR](https://us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate), and the [Harness ECR public gallery](https://gallery.ecr.aws/harness). In a continuation of this effort, and to improve stability when pulling Harness-required images, Harness deprecated the Harness-hosted `app.harness` Docker registry effective 15 Feb 2024. The registry end point will be end of life on 15 May 2024.

The end of life could impact you if:

* Your built-in Harness Docker connector (`account.harnessImage`) is configured to use the `app.harness` Docker registry. To avoid errors when the deprecation takes place, modify the target image registry by following the steps in [Configure Harness to always use credentials to pull Harness images](connect-to-harness-container-image-registry-using-docker-connector.md#configure-harness-to-always-use-credentials-to-pull-harness-images).
* You [pull Harness images from a private registry](connect-to-harness-container-image-registry-using-docker-connector.md#pull-harness-images-from-a-private-registry), and you are currently pulling the latest images from the `app.harness` Docker registry. To avoid errors post end of life, make sure you are pulling images from the [Harness project on GAR](https://us-docker.pkg.dev/gar-prod-setup/harness-public/harness/delegate) or the [Harness ECR public gallery](https://gallery.ecr.aws/harness).
* You have other Docker connectors configured to the `app.harness` Docker registry. Edit these connectors to use `https://registry.hub.docker.com` instead.

## Sample YAML <a href="#sample-yaml" id="sample-yaml"></a>

The following example shows the complete YAML for the connector created in this guide, that uses your credentials to pull Harness images from ECR.

<details>

<summary>YAML - Docker ECR connector</summary>

```yaml
connector:
  name: docker-ecr
  identifier: dockerecr
  description: ""
  accountIdentifier: youraccountidentifier
  orgIdentifier: default
  projectIdentifier: yourprojectidentifier
  type: DockerRegistry
  spec:
    dockerRegistryUrl: yourdockerregistryurl
    providerType: Other
    auth:
      type: UsernamePassword
      spec:
        username: username
        passwordRef: password
    delegateSelectors:
      - your-delegate-selector  # Replace with the selector for your delegate
    executeOnDelegate: true
    proxy: false
    ignoreTestConnection: false
```

</details>

## Troubleshoot Harness images <a href="#troubleshoot-harness-images" id="troubleshoot-harness-images"></a>

Go to the [CI Knowledge Base](../../../../delivery/continuous-integration/troubleshooting-and-resources/ci-articles-and-faqs/continuous-integration-faqs.md) for questions and issues related to Harness-required images, connectors, and pipeline initialization, such as:

* [How do I get a list of tags available for an image in the Harness image registry?](../../../../delivery/continuous-integration/troubleshooting-and-resources/ci-articles-and-faqs/continuous-integration-faqs.md#how-do-i-get-a-list-of-tags-available-for-an-image-in-the-harness-image-registry)
* [Build failed with "failed to pull image" or "ErrImagePull"](../../../../delivery/continuous-integration/troubleshooting-and-resources/ci-articles-and-faqs/continuous-integration-faqs.md#build-failed-with-failed-to-pull-image-or-errimagepull)
* [What access does Harness use to pull the Harness internal images from the public image repo?](../../../../delivery/continuous-integration/troubleshooting-and-resources/ci-articles-and-faqs/continuous-integration-faqs.md#what-access-does-harness-use-to-pull-the-harness-internal-images-from-the-public-image-repo)
* [Can I use my own private registry to store Harness CI images?](connect-to-harness-container-image-registry-using-docker-connector.md#pull-harness-images-from-a-private-registry)
* [Docker Hub rate limiting](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/harness-ci.md#docker-hub-rate-limiting)

***

## Next steps

- [Docker Connector Settings Reference](ref-artifact-repositories/docker-registry-connector-settings-reference.md): review all available settings for a Docker connector.
- [Use delegate selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md): control which delegates can connect to a Docker connector.
