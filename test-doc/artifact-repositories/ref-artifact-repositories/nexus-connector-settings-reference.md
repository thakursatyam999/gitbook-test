---
description: This topic provides settings and permissions for the Nexus connector.
---


# Nexus connector settings reference

This topic provides settings and permissions for the Nexus connector.

***

## What you will learn from this topic

- **Nexus permissions**: How to assign the permissions the Nexus account associated with the connector needs.
- **Artifact type support**: Which artifact types the Nexus connector supports.
- **Nexus Artifact Server settings**: How to configure the connector itself, including the repository URL, version, and credentials.
- **Nexus Artifact Details settings**: How to point a pipeline artifact source at a specific repository, path, and tag in Nexus.

***

## Nexus permissions required <a href="#nexus-permissions-required" id="nexus-permissions-required"></a>

The user account associated with the connector must have the following permissions in the Nexus server:

* Repo: All repositories (Read)
* Nexus UI: Repository Browser
* If using Nexus 3 as a Docker repo, the account also needs a role with the `nx-repository-view-*_*_*` privilege.

![Nexus repository permissions in the Nexus server](../../../../.gitbook/assets/nexus-connector-settings-reference-05.png)

For more information, go to the Sonatype documentation on [Managing Nexus security](https://help.sonatype.com/en/managing-security.html).

***

## Artifact type support <a href="#artifact-type-support" id="artifact-type-support"></a>

The following table lists which artifact types the Nexus connector supports.

Legend:

* **M** - Metadata. This includes Docker image and registry information. For AMI, this means AMI ID-only.
* **Blank** - Not supported.

| **Docker Image**(Kubernetes/Helm) | **AWS AMI** | **AWS CodeDeploy** | **AWS Lambda** | **JAR** | **RPM** | **TAR** | **WAR** | **ZIP** | **PCF** | **IIS** |
| --------------------------------- | ----------- | ------------------ | -------------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- |
| M                                 |             |                    |                |         |         |         |         |         |         | M       |

## Docker support <a href="#docker-support" id="docker-support"></a>

Nexus 3 artifact servers only.

***

## Nexus artifact server settings <a href="#nexus-artifact-server-settings" id="nexus-artifact-server-settings"></a>

The Harness Nexus artifact server connects your Harness account to your Nexus artifact resources. It has the following settings.

### Name, description, and tags <a href="#name-description-and-tags" id="name-description-and-tags"></a>

The unique name for this connector.

Harness creates an [Id (Entity Identifier)](../../../references/entity-identifier-reference.md) based on the name. You can change the **Id** while creating the connector. Once saved, the **Id** cannot be changed, but you can change the **Name**.

**Description** and [**Tags**](../../../tags/overview.md#create-tags-for-pipelines) are optional.

### Nexus repository URL <a href="#nexus-repository-url" id="nexus-repository-url"></a>

The URL that you use to connect to your Nexus server. For example, `https://nexus3.dev.mycompany.io/repository/your-repo-name`.

![Nexus Repository URL setting on the connector](../../../../.gitbook/assets/nexus-repository.png)

### Version <a href="#version" id="version"></a>

The supported Nexus version, `3.x`.

For Nexus 3.x, Harness supports only the Docker repository format as the artifact source.

### Credentials <a href="#credentials" id="credentials"></a>

The username and password for the Nexus account to use for this connector.

For the password, select a [Harness text secret](../../../secrets/add-use-text-secrets.md).

***

## Nexus artifact details settings <a href="#nexus-artifact-details-settings" id="nexus-artifact-details-settings"></a>

Configure the following settings to point a pipeline artifact source at a specific artifact in Nexus.

### Repository URL <a href="#repository-url" id="repository-url"></a>

The URL you would use in the Docker login to fetch the artifact. This is the same as the domain name and port you use for `docker login hostname:port`.

### Repository port <a href="#repository-port" id="repository-port"></a>

The port you use for `docker login hostname:port`.

As a best practice, include the scheme and port. For example, `https://your-repo:443`. If you cannot locate the scheme, you may omit it. For example, `your-repo:18080`.

For more information, go to the following Sonatype documentation:

* [Docker Repository Configuration and Client Connection](https://support.sonatype.com/hc/en-us/articles/115013153887-Docker-Repository-Configuration-and-Client-Connection)
* [Using Nexus 3 as Your Repository - Part 3: Docker Images](https://www.sonatype.com/blog/using-sonatype-nexus-repository-3-part-3-docker-images)

### Repository <a href="#repository" id="repository"></a>

Name of the repository where the artifact is located.

### Artifact path <a href="#artifact-path" id="artifact-path"></a>

The name of the artifact you want to deploy. For example, `nginx`, `private/nginx`, or `public/org/nginx`.

The repository and artifact path must not begin or end with `/`.

![Artifact Path setting on the artifact source](../../../../.gitbook/assets/nexus-connector-settings-reference-06.png)

### Tag <a href="#tag" id="tag"></a>

Select a [tag](../../../tags/overview.md#create-tags-for-pipelines) from the list.

{% @harness-feedback/feedback module="harness-ai" pagePath="harness-ai/use-harness-platform/connectors/artifact-repositories/nexus-connector-settings-reference" %}
