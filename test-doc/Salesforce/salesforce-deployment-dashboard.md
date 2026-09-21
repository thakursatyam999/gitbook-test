---
description: Learn how to use the Salesforce deployment dashboard to monitor deployment health and manage projects, packages, and diff evaluations.
tags:
  - salesforce
  - salesforce-dx
  - dashboard
  - manual-deployment
---

# Salesforce deployment dashboard

{% hint style="info" %}
**Feature Flag**

This feature is behind the feature flag `CDS_ENABLE_SALESFORCE_MANUAL_DEPLOYMENT_UI`. Contact [Harness Support](mailto:support@harness.io) to enable the feature.
{% endhint %}

The Salesforce deployment dashboard provides a centralized view for monitoring and managing all aspects of your Salesforce deployments within Harness. The dashboard displays deployment health at a glance, and organizes your Salesforce metadata operations into DX Projects, Packages, Diff Pairs, and Orgs.

The dashboard is designed for teams performing manual Salesforce deployments who need to monitor deployment success across orgs, track team activity, evaluate differences between environments, and create selective deployment packages. Instead of navigating through pipeline executions to find metadata comparisons, deployment artifacts, or team output, you can view and manage everything in one place.

To access the dashboard, go to **Packaged Applications** -> **Salesforce** in your Harness project.

***

## What you will learn from this topic

- How to monitor deployment health, org connections, and recent activity from the [Dashboard overview](#dashboard-overview).
- How to register and manage [DX projects](#dx-projects) from version control.
- How to register and manage [Packages](#packages) as deployment artifacts.
- How to create [Diff pairs](#diff-pairs), evaluate metadata differences, and promote snapshot components for selective deployment.
- How to track [Team activity](#team-activity) across your project.

***

## Dashboard overview

The top of the dashboard gives you a real-time snapshot of deployment health, followed by activity and team detail.

### Deployment summary

Three summary cards at the top of the dashboard show your deployment activity over the last 30 days:

- **Deployments (30d)**: Total number of deployments run across all orgs, along with the overall success rate.
- **Successful (30d)**: Number of deployments that completed successfully, across all connected orgs.
- **Failed (30d)**: Number of deployments that failed and need attention.

### Deployment Health chart

The **Deployment Health** chart plots successful and failed deployments per day over the last 30 days as a stacked bar chart. Each bar represents a day, with successful deployments shown in blue and failures shown in red, so you can quickly spot days with elevated failure rates.

### Org Overview

The **Org Overview** table lists the Salesforce orgs connected to your project. Each row shows:

- **Org**: The org name and its environment type (for example, Production or Sandbox).
- **Last Deploy**: When the org last received a deployment.
- **Last Validated**: When the org's metadata was last validated.
- **Connection**: The current connection status (Connected or Disconnected).

Click **View all** to open the full Orgs list.

### Recent Activity

The **Recent Activity** panel shows the latest events across your project, including deployments, validations, and quick deployments. Each entry shows the source and target (for example, `QA > Prod`), the activity type, the team member who performed it, the outcome, and how long ago it occurred.

***

## DX Projects

DX Projects represent Salesforce DX projects stored in version control systems. These projects contain your Salesforce metadata organized in the source-driven format expected by the Salesforce CLI. The dashboard allows you to register DX Projects from Git repositories and reference them in diff evaluations and deployments.

### View DX projects

The **DX Projects** panel on the dashboard shows a summary of registered projects and the total project count. Each entry shows:

- **Name**: The project name.
- **Branch**: The Git branch the project pulls metadata from.
- **Components**: The number of metadata components tracked in the project.

Click the project count link to open the full DX Projects list.

### Create a DX project

Creating a DX Project establishes a connection between Harness and your Salesforce metadata in version control. Once created, you can use the project as a source in diff evaluations or as a deployment artifact.

Perform the following steps to create a DX project:

1. From the Salesforce deployment dashboard, select the **DX Projects** count link to view the list of registered projects.
2. Click **Create DX project** to create a new DX project.
3. In the **Create DX project** panel, enter the following details:
   - **Name**: A unique identifier for the project (for example, `dx_project_4450`).
   - **Fetch Type**: Select **Branch** to pull metadata from a specific Git branch.
   - **GitHub Connector**: Select the connector pointing to your repository.
   - **Repository**: Enter the repository name containing your Salesforce DX project.
   - **Branch**: Specify the branch to use (for example, `main`).
   - **Folder Path**: Enter the path to the DX project root within the repository. Use `/` if the project is at the repository root.

![Configuring a new DX project in the Salesforce deployment dashboard](../../../.gitbook/assets/sf-dx-project-config.png)

4. Click **Submit** to create the DX Project.

The project appears in the DX Projects list and is immediately available for use in diff evaluations and deployments. The list displays the project name and when it was created or last updated.

***

## Packages

The **Packages** panel on the dashboard displays a summary of the Salesforce packages configured in your project. Packages bundle metadata and code for distribution and can be referenced in deployment pipelines as alternative artifacts to DX Projects.

### View Packages

Each entry in the Packages panel shows:

- **Name**: The package name.
- **Version**: The package version.
- **Type**: The package type (for example, Unlocked or Managed).

Click the package count link to open the full Packages list.

### Create a package

Creating a package registers an existing Salesforce package with Harness, so it can be used as a deployment artifact. Once created, you can reference the package in your Salesforce deployment pipelines alongside or instead of a DX project.

Perform the following steps to create a package:

1. From the Salesforce deployment dashboard, select the **Packages** count link to view the list of registered packages.
2. Click **Create package** to create a new package.
3. In the **Create package** panel, enter the following details:
   - **Name**: A unique identifier for the package (for example, `sf_package_32d4`).
   - **Package Id**: Enter or select the Salesforce package ID, for example, `04tgK00000037o9QAA`. This is the 04t ID that identifies your package.
   - **Salesforce Connector**: Select the Salesforce connector that points to your DevHub org.

![Configuring a new package in the Salesforce deployment dashboard](../../../.gitbook/assets/sf-package-config.png)

4. Click **Submit** to create the package.

The package appears in the Packages list and is immediately available for use as a deployment artifact. The list displays the package name and when it was created or last updated.

***

## Diff Pairs

Diff Pairs represent metadata comparisons between two sources. When you run a Salesforce Evaluate Diff step in a pipeline, Harness creates a Diff Pair that captures the comparison results. The dashboard allows you to view all diff evaluations in one place, making it easier to track what has been compared and what changes were identified.

### View Diff pairs

The **Diff Pairs** panel on the dashboard shows a summary of your metadata comparisons. Each entry shows:

- **Pair**: The two sources being compared (for example, `QA > Prod`).
- **Snapshots**: The number of open snapshots for that pair.
- **Last Activity**: When the pair was last updated or deployed.

Click **View all** to open the full Diff Pairs list.

### Create a diff pair

Creating a new Diff Pair allows you to compare metadata between two sources outside of a pipeline execution. This is useful when you want to quickly evaluate differences without running a full deployment workflow.

Perform the following steps to create a diff pair:

1. From the Salesforce deployment dashboard, select **View all** from the **Diff Pairs** panel to view the list of registered diff pairs.
2. Click **Create diff pair** to create a new diff pair.
3. In the **Create diff pair** panel, provide:
   - **Name**: A unique name for the diff pair.
   - **Description**: An optional description to explain what is being compared.
   - **Source A**: Select your source Salesforce connector. You can pick a connector scoped to the Project, Organization, or Account level, or select a DX Project.
   - **Source B**: Select your target Salesforce connector.
   - **Metadata**: Specify which Salesforce metadata types to include in the comparison.

![Configuring a new diff pair in the Salesforce deployment dashboard](../../../.gitbook/assets/sf-diffpair-config.png)

4. Click **Confirm** to start the diff evaluation.

Harness downloads metadata from both sources and performs a Git-based comparison. The resulting diff pair appears in the Diff Pairs list and is available for creating snapshots.

### View snapshots for a diff pair

Snapshots represent saved states of diff evaluations. After selecting a diff pair from the dashboard, you can view all snapshots created for that specific comparison. Each snapshot preserves the comparison results and allows you to select specific changes for selective deployments.

Perform the following steps to view snapshots for a diff pair:

1. Navigate to the Diff Pairs list and select the diff pair.
2. The Snapshots list displays all saved states for that diff pair. Each entry shows:
   - **Name**: The snapshot identifier.
   - **Metadata types**: The types included in the snapshot (for example, ApexClass).
   - **Status**: The current state of the snapshot (for example, Draft, Validated, or Deployed).
   - **Updated**: When the snapshot was created or last updated.

### Create a snapshot

Perform the following steps to create a new snapshot from a diff pair:

1. Navigate to the Diff Pairs list and select the diff pair.
2. Click **Create Snapshot**.
3. In the **Create snapshot** panel, enter a **Name** for the snapshot (for example, `snapshot_e2ca`).
4. Optionally add a description and tags, under **Metadata**.
5. Review the default metadata types automatically populated by the diff pair:
   - To use them as-is, leave them unchanged.
   - To modify them, click the **X** next to any tag to remove it, or add new types to fit your needs.
   - **Metadata Filter** (optional): Enter a metadata filter expression in the provided field to refine your selection.
6. Click **Create** to save the snapshot.

The snapshot appears in the Snapshots list for that diff pair and is immediately available for use in deployment pipelines.

### View and promote snapshot components

Each diff pair's **Snapshots** page organizes snapshots into four tabs based on their status:

- **Draft Snapshots**: Snapshots that have been created but not yet validated or deployed.
- **PR Snapshots**: Snapshots created from a pull request.
- **Validated Snapshots**: Snapshots that have passed validation.
- **Deployed Snapshots**: Snapshots that have been deployed to a target org.

Perform the following steps to review and promote the components in a snapshot:

1. From the Diff Pairs list, select a diff pair.
2. Select the tab that matches the snapshot's status (for example, **Deployed Snapshots**).
3. Find the snapshot you want to review, then click **View diff**.
4. In the **Select Diff for snapshot** panel, review the listed components:
   - **Name**: The component's name.
   - **Metadata type**: The component's Salesforce metadata type (for example, Apex class, Object).
   - **Change type**: Whether the component was Added, Modified, or Deleted.
   - **Dependencies**: The number of dependent components.
5. Select the checkbox next to each component you want to promote, then select **Review**.
6. In the **Review Components for Promotion** panel:
   - Confirm the staged components listed under **Staged components**.
   - Under **Target**, select the org you want to deploy to.
   - Under **Test Level**, select the Apex test level to run during deployment (for example, Run Relevant Tests).
7. Select **Validate** to check the deployment, or **Deploy** to deploy the staged components to the target org.

***

### Team Activity

The **Team Activity** table summarizes contributions from each team member over the last 30 days:

- **Member**: The team member's name.
- **Deployments**: Number of deployments they ran.
- **Validations**: Number of validations they ran.
- **Success Rate**: Their overall deployment success rate.
- **Last Active**: When they last performed an action on the dashboard.

***

## Next steps

- [Salesforce manual deployments](salesforce-manual-deployment.md): Learn how to perform manual deployments with diff evaluation and cherry-picking capabilities.
- [Deploy a Salesforce DX project](deploy-salesforce-dx-project.md): Deploy Salesforce metadata from version control.
- [Deploy a Salesforce package](deploy-salesforce-package.md): Deploy pre-built unlocked packages.
