---
id: kubernetes
title: Sumo Logic App for Kubernetes
sidebar_label: Kubernetes
description: The Sumo Logic Kubernetes App provides visibility into the worker nodes that comprise a cluster, as well as application logs of the worker nodes.
---

import useBaseUrl from '@docusaurus/useBaseUrl';

<img src={useBaseUrl('img/integrations/containers-orchestration/k8s.png')} alt="VMware dashboards" width="50"/>

The Sumo Logic Kubernetes App provides visibility into the worker nodes that comprise a cluster, as well as application logs of the worker nodes. The App is a single-pane-of-glass through which you can monitor and troubleshoot container health, replication, load balancing, pod state and hardware resource allocation. It utilizes [Falco](https://falco.org/docs/) events to monitor and detect anomalous container, application, host, and network activity.

In conjunction with the Kubernetes App, the AKS Control Plane, GKE Control Plane, EKS Control Plane, or Kubernetes Control Plane Apps provide visibility into the control plane, including the APIserver, scheduler, and controller manager.

[Kubernetes](https://kubernetes.io/) is a system that automates the deployment, management, scaling, networking, and availability of container-based applications. Kubernetes container-orchestration allows you to easily deploy and manage multi-container applications at scale.

:::tip
For an end-to-end solution for deploying, managing, monitoring, and administering your Kubernetes environment, see the Kubernetes Solution pages.
:::

## Supported versions

The following are the minimum supported requirements for this application:

<table>
  <tr>
   <td><strong>Name</strong>
   </td>
   <td><strong>Supported versions</strong>
   </td>
  </tr>
  <tr>
   <td>Kubernetes</td>
   <td>1.10 and later</td>
  </tr>
  <tr>
   <td>Kops</td>
   <td><p>1.13.10-k8s</p>
<p>1.13.10-kops</p>
<p>1.12.8-k8s</p>
<p>1.12.2-kops</p>
<p>1.10.13-k8s</p>
<p>1.10.0-kops</p>
   </td>
  </tr>
</table>


## Log and Metric Types

The Sumo Logic App for Kubernetes uses logs and metrics.

### Log source
* Application Logs

### Metrics sources

* [Node-exporter Metrics](https://prometheus.io/docs/guides/node-exporter/) - System-level statistics about bare-metal nodes or virtual machine and generates metrics.
* [Kube-state-metrics](https://github.com/kubernetes/kube-state-metrics) - Metrics about the state of Kubernetes logical objects, including node status, node capacity (CPU and memory), number of desired/available/unavailable/updated replicas per deployment, pod status (e.g., waiting, running, ready), and containers.


For more information, see [this page](https://github.com/SumoLogic/sumologic-kubernetes-collection). Metrics are collected using [Prometheus with FluentD](https://github.com/SumoLogic/sumologic-kubernetes-collection/tree/master/deploy#step-1-create-sumo-collector-and-deploy-fluentd).


## Collect Logs and Metrics for the Kubernetes App

This page has instructions for collecting logs and metrics for the Sumo App for Kubernetes. FluentBit and FluentD. Prometheus collects metrics data for Sumo Logic.

### What You'll Need  
Set the following fields in the Sumo Logic UI prior to configuring collection. This ensures that your logs are tagged with relevant metadata, which is required by the app dashboards and Explore.

* cluster
* container
* deployment
* host
* namespace
* node
* pod
* service

For information on setting up fields, see the [Fields](/docs/manage/fields) help page.

### Collecting metrics and logs for Kubernetes
Reference the [Deployment Guide](https://github.com/SumoLogic/sumologic-kubernetes-collection/blob/main/README.md#documentation) in our sumologic-kubernetes-collection GitHub repository for detailed instructions on how to collect Kubernetes logs, metrics, and events; enrich them with deployment, pod, and service level metadata; and send them to Sumo Logic.

The Deployment Guide has information on advanced configurations, best practices, performance, troubleshooting, and upgrading for our latest and previous versions of supported software.

### Sample log message

Application Logs:

```
{"timestamp":1561534865020,"log":"E0626 07:41:05.020255       1
manager.go:101] Error in scraping containers from kubelet:192.168.190.54:10255:
failed to get all container stats from Kubelet URL \"http://192.168.190.54:10255/stats/container/\":
Post http://192.168.190.54:10255/stats/container/: dial tcp 192.168.190.54:10255:
getsockopt: connection refused"}
```

### Sample Query

Message Breakdown by Container from the Dashboard Container Logs:

```sql
 cluster = * and namespace = * and pod = * and container = *
| json field=_raw "log" as message
| fields - message | count container | top 10 container by _count
```

## Install the Kubernetes App, Alerts, and view the Dashboards

This section provides instructions for installing the Kubernetes App and Alerts, as well as descriptions and examples for each of the dashboards. These instructions assume you have already set up the collection as described in "Collect Logs and Metrics for the Kubernetes App".


### Installing the Kubernetes App

Now that you have set up the collection for Kubernetes App, install the Sumo Logic App for Kubernetes to use the pre-configured Kubernetes dashboards that provide visibility into your Kubernetes environment.

To install the app, do the following:

1. Locate and install the app from the App Catalog. If you want to see a preview of the dashboards included with the app before installing, click  Preview Dashboards .
2. From the App Catalog, search for  Kubernetes  and select the app.
3. Click Add to Library.
4. Complete the following fields:
   * App Name. You can retain the existing name, or enter a name of your choice for the app. 
   * Data Source. For each the sources listed, enter a Custom Data Filter or Source Category, as follows: For Falco Log Source, leave  Source Category  selected, and enter the following source category: *falco* or one that matches the source categories in your environment. For  Events Log Source, leave  Source Category  selected, and enter the following source category: *events* or one that matches the source categories in your environment.
   * Advanced . Select the location in the Library (the default is the Personal folder in the Library), or click New Folder to add a new folder.
5. Click Add to Library.

### Installing Alerts

Sumo Logic has provided out of the box alerts available through Sumo Logic monitors Visualizations-and-Alerts/Alerts/Monitors to help you quickly determine if the Kubernetes cluster is available and performing as expected. These alerts are built based on metrics datasets and have preset thresholds based on industry best practices and recommendations.

* To install these alerts, you need to have the Manage Monitors role capability.
* Alerts can be installed by either importing them a JSON or a Terraform script.

For details on the individual alerts, see [Kubernetes Alerts](/docs/observability/kubernetes-solution/alerts).

#### Method 1: Install the alerts by importing a JSON file:

1. Download the [JSON file](https://raw.githubusercontent.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/main/monitor_packages/kubernetes/kubernetes.json) describing all the monitors.   

2. The alerts should be restricted to specific clusters and/or namespaces to prevent the monitors hitting the cardinality limits. To limit the alerts, update the JSON file by replacing the text `$$kubernetes_data_source` with `<Your Custom Filter>`. For example: `cluster=k8s-prod.01`.

3. Go to Manage Data > Alerts > Monitors.

4. Click Add:

<img alt="Add monitors page.png" height="181" src="https://lh4.googleusercontent.com/EFaIvWK46GHRZabY_FlDjQURgAxwVs7p8c1FIQpUkQVA4_rU0BXo_lNvxxpM0kkFp9CuOvQHfrtGp1DnfmE_OOIZORCwaKcPnAYUgYzzcM3UAM9qynLgOziqQBg5ex0eYKwVAhXI" width="153" />

5. Click Import to import monitors from the JSON above.

:::note
The monitors are disabled by default. Once you have installed the alerts using this method, navigate to the Kubernetes folder under  Monitors  to configure them. See this document to enable monitors, to configure each monitor, to send notifications to teams or connections please see the instructions detailed in Step 4 of this /Visualizations-and-Alerts/Alerts/Monitors#Add_a_monitor document.
:::

#### Method 2: Install the alerts using a Terraform script

1. Generate a Sumo Logic access key and ID

Generate an access key and access ID for a user that has the Manage Monitors role capability in Sumo Logic using [these instructions](/docs/manage/security/access-keys). Please identify which deployment your Sumo Logic account is in, using this /APIs/General-API-Information/Sumo-Logic-Endpoints-by-Deployment-and-Firewall-Security"> link.

2. [Download and install Terraform 0.13](https://www.terraform.io/downloads.html) or later.

3. Download the Sumo Logic Terraform package for Kubernetes alerts. The alerts package is available in the [Sumo Logic github repository](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/tree/main/monitor_packages/kubernetes). You can either download it through the “git clone” command or as a zip file.

4. Alert Configuration. After the package has been extracted, navigate to the package directory terraform-sumologic-sumo-logic-monitor/monitor_packages/kubernetes/

Edit the kubernetes.auto.tfvars file and add the Sumo Logic Access Key, Access Id and Deployment from Step 1 .

```
access_id   = "<SUMOLOGIC ACCESS ID>"
access_key  = "<SUMOLOGIC ACCESS KEY>"
environment = "<SUMOLOGIC DEPLOYMENT>"
```

The alerts should be restricted to specific clusters and/or namespaces to prevent the monitors hitting the cardinality limits. To limit the alerts, update the variable  `kubernetes_data_source` with your `<Your Custom Filter>`. For example: `cluster=k8s.prod.01`.

All monitors are disabled by default on installation, if you would like to enable all the monitors, set the parameter  monitors_disabled  to false in this file.

By default, the monitors are configured in a monitor folder called “Kubernetes”, if you would like to change the name of the folder, update the monitor folder name in this file.

If you would like the alerts to send email or connection notifications, configure these in the file kubernetes_notifications.auto.tfvars . For configuration examples, refer to the next section.

5. Email and Connection Notification Configuration Examples. Modify the file kubernetes_notifications.auto.tfvars and populate connection_notifications_critical, connection_notifications_warnings, connection_notifications_missingdata  and email_notifications_critical, email_notifications_warnings, email_notifications_missingdata as per below examples.

Pagerduty Connection Example:

```
connection_notifications_critical = [
    {
      connection_type       = "PagerDuty",
      connection_id         = "<CONNECTION_ID>",
      payload_override      = "{\"service_key\": \"your_pagerduty_api_integration_key\",\"event_type\": \"trigger\",\"description\": \"Alert: Triggered {{TriggerType}} for Monitor {{Name}}\",\"client\": \"Sumo Logic\",\"client_url\": \"{{QueryUrl}}\"}",
      run_for_trigger_types = ["Critical", "ResolvedCritical"]
    },
    {
      connection_type       = "Webhook",
      connection_id         = "<CONNECTION_ID>",
      payload_override      = "",
      run_for_trigger_types = ["Critical", "ResolvedCritical"]
    }
  ]
```

Replace <CONNECTION_ID> with the connection id of the webhook connection. The webhook connection id can be retrieved by calling the https://api.sumologic.com/docs/#operation/listConnections">Monitors API.

For overriding payload for different connection types, refer to this /Manage/Connections-and-Integrations/Webhook-Connections/Set_Up_Webhook_Connections">document.

Email Notifications Example:

```
email_notifications_critiical = [
    {
      connection_type       = "Email",
      recipients            = ["abc@example.com"],
      subject               = "Monitor Alert: {{TriggerType}} on {{Name}}",
      time_zone             = "PST",
      message_body          = "Triggered {{TriggerType}} Alert on {{Name}}: {{QueryURL}}",
      run_for_trigger_types = ["Critical", "ResolvedCritical"]
    }
  ]
```

6. Install the Alerts.
   * Navigate to the package directory terraform-sumologic-sumo-logic-monitor/monitor_packages/ kubernetes / and run  terraform init.  This will initialize Terraform and will download the required components.
   * Run  terraform plan  to view the monitors which will be created/modified by Terraform.
   * Run  terraform   apply .

7. Post Installation. If you haven’t enabled alerts and/or configured notifications through the Terraform procedure outlined above, we highly recommend enabling alerts of interest and configuring each enabled alert to send notifications to other people or services. See Step 4 of /Visualizations-and-Alerts/Alerts/Monitors#Add_a_monitor">Add a Monitor.

:::note
There are limits to how many alerts can be enabled - see the [Alerts FAQ](/docs/alerts/index.md).
:::


### Dashboards

### Filter with template variables   

Template variables provide dynamic dashboards that can rescope data on the fly. As you apply variables to troubleshoot through your dashboard, you view dynamic changes to the data for a quicker resolution to the root cause. For more information, see the /Visualizations-and-Alerts/Dashboard_(New)/Filter_with_template_variables" title="Filter with template variables">Filter with template variables help page.

:::tip
You can use template variables to drill down and examine the data on a granular level.
:::

#### Cluster Explorer Dashboard

The **Kubernetes - Cluster Explorer** dashboard provides a high-level view of the health of the cluster services, along with details on the utilized resources by service.

Use this dashboard to:
* Navigate the cluster topology
* Review the memory and CPU usage by cluster and service components.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Cluster_Explorer.png')} alt="K8s dashboards" />


#### Cluster Dashboard  

The**Kubernetes - Cluster**dashboard provides detailed status of the cluster health, along with details on all the components, resources and related entities.

Use this dashboard to:  
* Monitor overall cluster health.
* Get insight into the state and resource usage of cluster components and use this information to fine-tune your Kubernetes cluster.  
* Get quick insights into the state of the related entities.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Cluster.png')} alt="K8s dashboards" />


#### Cluster Overview Dashboard

The**Kubernetes - Cluster Overview**dashboard provides a high-level view of the cluster health. Use this dashboard to:  
* Get quick insights into the health of the cluster.
* View top resource intensive components and use this information to fine tune your cluster.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Cluster_Overview.png')} alt="K8s dashboards" />


#### Node Dashboard

The **Kubernetes - Node** dashboard provides detailed information on the health and performance of nodes in a Kubernetes cluster.

Use this dashboard to:
* Monitor node health.
* Get insight  into how resources are being used across nodes and fine-tune node configurations accordingly.
* Investigate potential issues with nodes.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Node.png')} alt="K8s dashboards" />

#### Node Overview Dashboard  

The **Kubernetes - Node Overview**dashboard provides a high-level view of a node, along with details on all the related components and resources.

Use this dashboard to:  
* Get quick insights into the health of the node.  
* View top resource intensive components and use this information to fine tune your node.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Node_Overview.png')} alt="K8s dashboards" />


#### Namespace Dashboard

The **Kubernetes - Namespace**dashboard provides insights into the health and resource utilization of a namespace.

Use this dashboard to:  
* Monitor namespace health.  
* Get insight into the components of a namespace and how resources are being used across namespaces and fine-tune configurations accordingly.  
* Investigate potential issues.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Namespace.png')} alt="K8s dashboards" />


#### Pod Dashboard

The **Kubernetes - Pod** dashboard provides insights into the health of and resource utilization of a Kubernetes pod.

Use this dashboard to:  
* Monitor pod health.  
* Get insight into the components of a pod and how resources are being used across namespaces and fine-tune configurations accordingly.  
* Investigate potential issues.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Pod.png')} alt="K8s dashboards" />
#
### Container Dashboard

The **Kubernetes - Container** dashboard provides insights into the health and resource utilization of a Kubernetes container.

Use this dashboard to:  
* Monitor container health.  
* Get insight into container resource utilization and fine-tune configurations accordingly.  
* Determine if containers are stuck in CrashLoopBackOff, Terminated or Waiting states and make necessary adjustments.  
* Investigate containers that are over-utilizing resources.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Container.png')} alt="K8s dashboards" />


#### Daemonsets Overview Dashboard

The **Kubernetes - Daemonsets Overview**dashboard provides insights into the health of and resource utilization of Kubernetes Daemonsets.

Use this dashboard to:  
* Monitor the health of Daemonsets.   
* Identify whether the required replica level is achieved or not.  
* View logs and errors and investigate potential issues.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Daemonsets_Overview.png')} alt="K8s dashboards" />


#### StatefulSets Overview Dashboard  

The **Kubernetes - StatefulSets Overview** dashboard provides insights into the health of and resource utilization of Kubernetes StatefulSets.

Use this dashboard to:  
* Monitor the health of StatefulSets.   
* Identify whether the required replica level is achieved or not.
* View logs and errors and investigate potential issues.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_StatefulSets_Overview.png')} alt="K8s dashboards" />


#### Deployment Overview Dashboard

The **Kubernetes - Deployment Overview** dashboard provides insights into the health and performance of your Kubernetes deployments.

Use this dashboard to:  
* Monitor the health of deployments in your Kubernetes environment.   
* Identify whether the required replica level has been achieved or not.  
* View logs and errors and investigate potential issues.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Deployment_Overview.png')} alt="K8s dashboards" />


#### Kubernetes - Health Check

The **Kubernetes - Health Check**dashboard displays the collection status from all the components in the Kubernetes cluster.

Use this dashboard to:  
* Monitor the health of FluentD and FluentBit pods in your Kubernetes environment.
* Gain insights into Prometheus metric collection endpoint status.
* Get insight into resource utilization and fine-tune configurations accordingly.
* View logs and errors and investigate potential issues.

<img src={useBaseUrl('img/integrations/containers-orchestration/k8s-health.png')} alt="K8s dashboards" />


#### Deployment Dashboard  

The **Kubernetes - Deployment**dashboard provides insights into the health and performance of your Kubernetes deployments.

Use this dashboard to:  

* Monitor the health of deployments in your Kubernetes environment.   
* Identify whether the required replica level has been achieved or not.  
* View logs and errors and investigate potential issues.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Deployment.png')} alt="K8s dashboards" />


#### Security Overview Dashboard

:::note
This dashboard relies on Falco. If the Dashboard is not populated, enable Falco by setting the flag `falco:enabled` as `"true"` in values.yaml as described [here](https://github.com/SumoLogic/sumologic-kubernetes-collection/blob/master/deploy/docs/Installation_with_Helm.md).
:::

This dashboard provides high level details around anomalous container, application, host, and network activity detected by Falco.

Use this dashboard to:  
* Identify and investigate anomalous activity.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Security_Overview.png')} alt="K8s dashboards" />


#### Security Rules Triggered Dashboard

:::note This dashboard relies on Falco. If the Dashboard is not populated, enable Falco by setting the flag "falco:enabled" as "true" in values.yaml as described https://github.com/SumoLogic/sumologic-kubernetes-collection/blob/master/deploy/docs/Installation_with_Helm.md">on this page.
:::

The**Kubernetes - Security Rules Triggered**dashboard provides detailed information around anomalous activity detected by Falco. It also shows information around the OOB Falco rules triggered by anomalous activity in your Kubernetes environments.

Use this dashboard to:
* Reviewed detailed information of anomalous activity.
* Review if the OB Falco security events are triggered and identify the root cause.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Sec_Rules_Triggered.png')} alt="K8s dashboards" />


#### Service Dashboard

The **Kubernetes - Service** dashboard provides a high-level view of the health of the cluster services, along with details on utilized resources by service.

Use this dashboard to:  
* Reviewed detailed information of services.  
* Identify components by Services.  
* Determine any errors and warnings by Services.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Service.png')} alt="K8s dashboards" />


#### Hygiene Check Dashboard

The **Kubernetes - Hygiene Check** dashboard provides visibility into the configuration hygiene of your Kubernetes cluster. This dashboard displays color-coded performance checks for nodes, along with resource utilization, pod capacity, pod errors, and pod states.

 Use this dashboard to:  
* Assess bad configurations and determine the trouble areas for proactive adjustment.   
* Monitor resource allocation across your cluster to maintain optimum performance.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_Hygiene_Check.png')} alt="K8s dashboards" />


#### CoreDNS

CoreDNS is a [DNS server](https://en.wikipedia.org/wiki/Domain_Name_System) and can be used as a replacement for kube-dns in a kubernetes cluster.

The **Kubernetes - CoreDNS** dashboard provides visibility into the health and performance of CoreDNS.  

Use this dashboard to:  
* Track the total number of requests.  
* Review Cache statistics.  
* Monitor CoreDNSs resource usage and spikes.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_CoreDNS.png')} alt="K8s dashboards" />


#### HPA Dashboard

The Horizontal Pod Autoscaler automatically scales the number of Pods in a replication controller, deployment, replica set or stateful set based on observed CPU utilization.

The **Kubernetes - HPA**dashboard provides visibility into the health and performance of HPA.  

Use this dashboard to:  
* Identify whether the required replica level has been achieved or not.  
* View logs and errors and investigate potential issues.

<img src={useBaseUrl('img/integrations/containers-orchestration/K8s_HPA.png')} alt="K8s dashboards" />