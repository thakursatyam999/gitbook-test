---
description: Overview of the artifact repository connectors available in Harness, where to add them, and how they differ from cloud provider connectors.
---

# Overview

You connect Harness to an artifact repository by adding an **Artifact Repositories** connector. Once you add the connector, it is available in Pipelines and in Connectors of the same Account, Org, or Project.

You can connect to an artifact repository inline while developing your pipeline, or separately from your Account, Org, or Project **Connectors** page.

---

## What you will learn from this topic

- **Available connector types:** Which artifact repositories Harness connects to, and where the setup steps for each one live.
- **Connector scope:** How account, org, and project scope affect where you add a connector.
- **Cloud storage exceptions:** Why AWS S3 and Google Cloud Storage artifacts use a different connector type.
- **Inline connectors:** How to add an artifact repository connector while building a pipeline, instead of from the Connectors page.

---

## Available connectors

For instructions on connecting to a specific artifact repository, see:

- [Connect to Artifactory](add-an-artifactory-connector.md)
- [Connect to Jenkins](add-a-jenkins-connector.md)
- [Connect to a Docker registry](add-a-docker-registry-connector.md)
- [Connect to an HTTP Helm repository](add-an-http-helm-repo-connector.md)
- [Connect to an OCI Helm registry](add-an-oci-helm-registry-connector.md)
- [Connect to Nexus](add-a-nexus-connector.md)

---

## Scope

You can add an artifact repository connector at the account, org, or project scope. Each connector page above explains the steps at the project scope. The process is the same for org and account scope, starting from the corresponding **Connectors** page.

---

## AWS, Azure, and Google Cloud Storage artifacts

Connectors for artifacts stored in Google Cloud Storage or Amazon S3 are added as **Cloud Providers** connectors, not **Artifact Repositories** connectors. Go to [Connect to a cloud provider](../cloud-providers/connect-to-a-cloud-provider.md) for detailed information.

For Azure ACR, use the **Docker Registry** connector. Go to [Connect to Docker registry](add-a-docker-registry-connector.md) to add one.

---

## Inline connectors in a pipeline

You can also add an artifact repository connector inline while developing a pipeline. The steps for adding a connector inline are covered in the relevant how-to and technical reference topics for that connector type. For example, go to [Docker Connector Settings Reference](ref-artifact-repositories/docker-registry-connector-settings-reference.md) to review the settings for an inline Docker Registry connector.

---

## Next steps

- [Use delegate selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md): Control which delegate runs a connector's operations.
- [Add a secret manager](../../../troubleshooting-and-resources/tutorials/add-secrets-manager.md): Store the credentials an artifact repository connector uses to authenticate.
