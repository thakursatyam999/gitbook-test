---
description: Connect Harness to IBM Cloud Container Registry using a Harness Docker Registry connector.
---


# Connect to IBM Cloud Container Registry

You can connect Harness to IBM Cloud Container Registry using a Harness Docker Registry connector. The connector uses your credentials to your IBM Cloud Container Registry and allows you to push and pull images.

This topic explains how to use the Harness Docker Registry connector to connect Harness to IBM Cloud Container Registry.

***

## What you will learn from this topic

- How to review the [IAM policies](#review-iam-policies-in-ibm-cloud) required by IBM Cloud Container Registry before you connect Harness to it.
- How to [generate an API key](#step-1-generate-an-api-key-in-ibm-cloud-console) in the IBM Cloud console.
- Steps to [create a Docker Registry connector](#step-2-create-a-docker-registry-connector-in-harness) in Harness, [enter its credentials](#step-3-enter-credentials), and [set up delegates](#step-5-set-up-delegates) so Harness can push and pull images from IBM Cloud Container Registry.

***

## Before you begin

Confirm the following before you connect Harness to IBM Cloud Container Registry:

- [CI key concepts](../../../../delivery/continuous-integration/new-to-harness-ci/key-concepts.md)
- [Delegate overview](../../delegates/delegate/delegate-concepts/delegate-overview.md)

***

### Review IAM policies in IBM Cloud <a href="#review-iam-policies-in-ibm-cloud" id="review-iam-policies-in-ibm-cloud"></a>

If the IBM Cloud IAM role used by your Docker Registry connector does not have the policies required by the IBM service you want to access, you can modify or switch the role.

Go to [Defining access role policies](https://cloud.ibm.com/docs/Registry?topic=Registry-user#user) from IBM to set up and manage IAM policies.

When you switch or modify the IAM role, it might take up to 5 minutes to take effect.

***

### Step 1: Generate an API key in IBM Cloud Console <a href="#step-1-generate-an-api-key-in-ibm-cloud-console" id="step-1-generate-an-api-key-in-ibm-cloud-console"></a>

Follow the instructions outlined in [Creating an API Key](https://cloud.ibm.com/docs/account?topic=account-userapikey&interface=ui#create_user_key) from IBM.

Once the API key is successfully generated, click **Copy** or **Download the API key**.

![Generate an API key in the IBM Cloud console](../../../.gitbook/assets/using-ibm-registry-to-create-a-docker-connector-71.png)

***

### Step 2: Create a Docker Registry connector in Harness <a href="#step-2-create-a-docker-registry-connector-in-harness" id="step-2-create-a-docker-registry-connector-in-harness"></a>

You can create the Docker Registry connector at the Harness account, org, or project level. This procedure covers the project level, and the process is the same for org and account.

Perform the following steps to create a Docker Registry connector:

1. Open your Harness Project.
2. In **Project Settings**, select **Connectors**.
3. Click **New Connector**, and under **Artifact Repositories** click **Docker Registry**. The Docker Registry settings appear.

   ![New Connector dialog with Docker Registry selected under Artifact Repositories](../../../.gitbook/assets/using-ibm-registry-to-create-a-docker-connector-72.png)
4. In **Name**, enter a name for this connector.

   ![Docker Registry connector Name field](../../../.gitbook/assets/using-ibm-registry-to-create-a-docker-connector-73.png)

   Harness automatically creates the corresponding Id ([entity identifier](../../references/entity-identifier-reference.md)).
5. Click **Continue**.

***

### Step 3: Enter credentials <a href="#step-3-enter-credentials" id="step-3-enter-credentials"></a>

Here is where you use the API key you generated in IBM Cloud.

![Docker Registry connector credentials form](../../../.gitbook/assets/using-ibm-registry-to-create-a-docker-connector-74.png)

Select or enter the following options:

| **Field**                | **Description**                                                                                                                                                                                     |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Docker Registry URL**  | Enter the IBM Cloud Container Registry API endpoint URL. For example: `https://us.icr.io`. Go to [IBM Cloud Container Registry](https://cloud.ibm.com/apidocs/container-registry#endpoint-url) from IBM to find the endpoint URL for your region. |
| **Provider Type**        | Select **Other (Docker V2 compliant)**.                                                                                                                                                            |
| **Authentication**       | Select **Username and Password**.                                                                                                                                                                  |
| **Username**             | Enter `iamapikey`. Go to [Authentication](https://cloud.ibm.com/docs/Registry?topic=Registry-registry_access&mhsrc=ibmsearch_a&mhq=iamapikey#registry_access_apikey_auth) from IBM to understand how the API key authenticates with the registry. |
| **Password**             | In **Password**, click **Create** or **Select a Secret**. In the new secret, in **Secret Value**, enter the API key generated in [Step 1](#step-1-generate-an-api-key-in-ibm-cloud-console). |

![Docker Registry connector Save and Continue buttons](../../../.gitbook/assets/using-ibm-registry-to-create-a-docker-connector-75.png)

Click **Save**, and **Continue**.

***

### Step 4: Select connectivity mode <a href="#step-4-select-connectivity-mode" id="step-4-select-connectivity-mode"></a>

Under **Select Connectivity Mode**, select how you want Harness to connect to IBM Cloud Container Registry:

- **Connect through Harness Platform**: Use a direct, secure connection between Harness and IBM Cloud Container Registry. Use this option with [Harness Cloud build infrastructure](../../../../delivery/continuous-integration/use-harness-ci/use-harness-ci/set-up-build-infrastructure/use-harness-cloud-build-infrastructure.md).
- **Connect through a Harness Delegate**: Harness communicates with Docker through a Harness Delegate. For delegate installation instructions, go to [Delegate installation overview](../../delegates/delegate/install-delegates/overview.md).

### Step 5: Set up delegates <a href="#step-5-set-up-delegates" id="step-5-set-up-delegates"></a>

Harness uses Docker Registry connectors at pipeline runtime to authenticate and perform operations with IBM Cloud Container Registry. Authentications and operations are performed by Harness delegates.

If you select **Connect through a Harness Delegate**, you can select any available Harness delegate and Harness selects the delegate for you. For a description of how Harness picks delegates, go to [Delegates overview](../../delegates/delegate/delegate-concepts/delegate-overview.md).

You can use delegate tags to select one or more delegates. For details on delegate tags, go to [Use delegate selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md).

If you need to install a delegate, go to [Delegate installation overview](../../delegates/delegate/install-delegates/overview.md).

{% hint style="warning" %}
**Delegate network connectivity**

Every delegate you use must have network connectivity to IBM Cloud Container Registry.
{% endhint %}

Click **Save and Continue**.

***

### Step 6: Verify the connection <a href="#step-6-verify-the-connection" id="step-6-verify-the-connection"></a>

Harness tests the credentials you provided using the delegates you selected.

![Test connection result for the Docker Registry connector](../../../.gitbook/assets/using-ibm-registry-to-create-a-docker-connector-76.png)

If the credentials fail, you see an error. Click **Edit Credentials** to modify your credentials.

Click **Finish**.

***

## Next steps

- [Delegates overview](../../delegates/delegate/delegate-concepts/delegate-overview.md): learn how Harness selects a delegate to run connector operations.
- [Use delegate selectors](../../delegates/delegate/manage-delegates/select-delegates-with-selectors.md): use delegate tags to control which delegates connect to this registry.
