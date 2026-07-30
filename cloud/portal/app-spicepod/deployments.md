---
description: Monitor and manage app Spicepod instances and deployments.
icon: rocket-launch
---

# Deployments

### Create New Deployment

Navigate to the **Deployments** tab and click on **Create Deployment**.

### Deployment Status

Each deployment listed on the **Deployments** tab reports a status derived from the live state of its instances:

| Status                | Meaning                                                                                         |
| --------------------- | ----------------------------------------------------------------------------------------------- |
| **Pending**           | The deployment is queued or created; no instances have started yet.                              |
| **Deploying**         | Instances are starting.                                                                         |
| **Loading**           | Instances are running and loading their initial datasets.                                       |
| **Ready**             | All replicas are ready and serving traffic.                                                     |
| **Ready with errors** | All replicas are ready and passing health checks, but the runtime is reporting errors.           |
| **Unhealthy**         | All required replicas are ready, but at least one is failing its health check.                  |
| **Terminating**       | The deployment is being replaced by a newer deployment that is ready to take traffic.            |
| **Succeeded**         | The deployment completed and its instances are no longer reporting live state.                   |
| **Paused**            | The app is paused, so no deployment is serving.                                                  |
| **Created**           | No live state is available for the deployment, typically because it has already been replaced.    |
| **Failed**            | The deployment failed.                                                                          |

**Ready with errors** distinguishes a deployment that started successfully from one that works. The instances pass their health checks, but the runtime inside them is reporting problems — a dataset that cannot connect, or a model that fails to load. A failing health check is the stronger signal, so a deployment that is both unhealthy and reporting errors shows **Unhealthy**.

### Issues

When a deployed Spicepod reports errors or warnings, the portal collects them into a single **Issues** feed instead of leaving them in the log tail.

Issues are surfaced in four places:

* A banner on every page of the app, listing the most recent error and the number of other errors. The banner covers errors only — warnings appear in the **Issues** panel.
* An error count on the **Deployments** tab in the app navigation.
* An **Issues** panel on the **Deployments** page, listing every issue for the app, and on each instance page, scoped to that instance.
* An indicator on the affected row in **Datasets** and **Models**, when an issue can be attributed to a component.

The feed combines runtime `ERROR` and `WARN` log lines with the reported status of each dataset. Repeats of the same failure collapse into one row with an occurrence count, so a connector retrying every second appears once rather than hundreds of times.

Each row shows where the issue came from, how many times it occurred, when it was last seen, the dataset or model it was attributed to, and the instance that reported it. **Show details** expands the full text, including stack traces. The link on the row opens the originating instance's logs filtered to the issue, or the component's page for an issue reported by a dataset.

{% hint style="info" %}
Dismissing an error hides that specific error. A different failure raises the banner again.
{% endhint %}

An app with no issues shows no banner, no count, and no panel.

### Spicepod Instance Logs

Navigate to the **Deployments** tab and click on the **Logs** for the selected instance.
