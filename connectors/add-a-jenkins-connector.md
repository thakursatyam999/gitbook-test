---
description: Connect Harness to Jenkins using a Harness Jenkins connector.
---

# Connect to Jenkins

You can perform Continuous Integration (CI) in Harness using the CI module and [CI pipelines](https://app.gitbook.com/s/qKtVmwAGTfGQS1MVC97G/new-to-harness-ci/key-concepts). If you use Harness Continuous Delivery (CD) but not Harness CI, you can still perform CI using the **Jenkins** step in a CD stage.

You connect Harness to Jenkins using a Harness Jenkins connector. This connector allows you to run Jenkins jobs in [Jenkins steps](https://app.gitbook.com/s/y1JhZ4oKIppwY7d5AhPj/use-continuous-delivery/cd-building-blocks/cd-steps/builds/run-jenkins-jobs-in-cd-pipelines).

This topic shows you how to add a Jenkins connector to Harness.

***

## What you will learn from this topic

- How to check the [Jenkins permissions and authentication requirements](#before-you-begin) before you connect Harness to Jenkins.
- How to [add a Jenkins connector](#add-a-jenkins-connector) to a Harness project so you can run Jenkins jobs from a pipeline.

***

## Before you begin

- **Jenkins permissions**: The user account for this connection needs **Read** access at the **Overall** level and **Build** access at the **Job** level on the Jenkins server. For details on configuring these permissions, see [Jenkins Matrix-based security](https://wiki.jenkins.io/display/JENKINS/Matrix-based+security) from Jenkins.
- **Jenkins API token** (for token-based authentication): Go to `http://JENKINS_IP_ADDRESS/jobs/me/configure` on your Jenkins server to check or generate your API access token. The Harness Jenkins connector sends this token as part of the HTTP header.
- **Okta or two-factor authentication (2FA)**: If you use Okta or 2FA for connections to Jenkins, select **API Token** for **Authentication** when you configure the connector.

{% hint style="info" %}
**SAML authentication**

Harness does support SAML authentication for Jenkins connections.
{% endhint %}

***

### Add a Jenkins connector

You can add a Jenkins connector at the project, org, or account scope. This procedure covers the project scope, and the process is the same for org and account. You can also add a Jenkins connector directly when you configure the Jenkins step in a pipeline.

Perform the following steps to add a Jenkins connector:

1. Open your Harness project.
2. In **Project Settings**, select **Connectors**.
3. Click **New Connector**, then click **Jenkins**.
4. In **Name**, enter a name for the connector. Optionally, add a description and tags. Click **Continue**.
6. Enter the URL of the Jenkins master or controller:
   - If you use the Jenkins SaaS (cloud) edition, find the URL in your browser's address bar.
   - If you use the standalone edition of Jenkins, navigate to **System** in **Manage Jenkins**, and find the URL under **Jenkins Location**.

   ![Jenkins Location field in Manage Jenkins showing the Jenkins server URL](../../../.gitbook/assets/connect-to-jenkins-10.png)
7. In **Authentication**, select one of the following options to authenticate with the Jenkins server:
   - **Username** and **Password/API Token**: Enter the username for the account. Select or create a Harness Encrypted Text secret using your Jenkins API token or password. Go to [Before you begin](#before-you-begin) to generate or check this token.
   - **Bearer Token (HTTP Header)**: Select or create a Harness Encrypted Text secret using the OpenShift OAuth access token. This option applies only to Jenkins servers hosted or embedded in an OpenShift cluster that use this authentication method. Go to [Authentication](https://docs.openshift.com/container-platform/3.7/architecture/additional_concepts/authentication.html) from OpenShift to configure OAuth authentication for a Jenkins server hosted on OpenShift. 
  Click **Continue**.
8. In **Delegates Setup**, use any delegate or enter [tags](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md) for the specific delegates that you want to allow to connect to this connector. Click **Save and Continue**.
9. Click **Finish** after the connection test succeeds. 

The Harness Jenkins connector is added.

<details>

<summary>YAML - Jenkins connector</summary>

```yaml
connector:
  name: satyam-jenkins-connector
  identifier: satyamjenkinsconnector
  description: ""
  accountIdentifier: OgiB4-xETamKNVAz-wQRjw
  orgIdentifier: default
  projectIdentifier: donotdeletesatyamtest
  type: Jenkins
  spec:
    jenkinsUrl: http://15.115.240.200:8080/
    auth:
      type: UsernamePassword
      spec:
        username: username
        passwordRef: password
    delegateSelectors:
      - satyam-helm-delegate
    ignoreTestConnection: false
```

</details>

***

## Next steps

- [Run Jenkins jobs in CD pipelines](https://app.gitbook.com/s/y1JhZ4oKIppwY7d5AhPj/use-continuous-delivery/cd-building-blocks/cd-steps/builds/run-jenkins-jobs-in-cd-pipelines): configure a Jenkins step to run Jenkins jobs in your pipeline.
