---
id: couchbase
title: Sumo Logic App for Couchbase
sidebar_label: Couchbase
description: Couchbase is a distributed document database with a powerful search engine and in-built operational and analytical capabilities.
---

import useBaseUrl from '@docusaurus/useBaseUrl';

<img src={useBaseUrl('img/integrations/databases/databases-icon.png')} alt="DB icon" width="75"/>

Couchbase, a modern database for enterprise applications, is a distributed document database with a powerful search engine and in-built operational and analytical capabilities. It brings the power of NoSQL to the edge and provides fast, efficient bidirectional synchronization of data between the edge and the cloud. The Sumo Logic app for Couchbase helps you monitor activity in Couchbase. The pre-configured dashboards provide insight into the Health of the Cluster, the Status of the Buckets, I/O of Reading/Writing, Errors, the Events of Couchbase Servers that help you understand your clusters.

This App has been tested with the following Couchbase with Telegraf versions:

* Non-Kubernetes: Couchbase version: 7.0.2 - enterprise with Telegraf version 1.21.1
* Kubernetes: Couchbase version: 7.0.2 - enterprise with Telegraf version 1.21.1


Telegraf 1.14 default of Kubernetes Collection will not work.


## Collect Logs and Metrics for the Couchbase App

This page provides instructions for configuring log and metric collection for the Sumo Logic App for Couchbase.

Collection Process Overview

Configuring log and metric collection for the Couchbase App includes the following tasks:

* Configure Fields in Sumo Logic.
* Configure Collection for Couchbase
    * [Collect Logs and Metrics for Non-Kubernetes environments](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/Couchbase/Collect_Logs_and_Metrics_for_the_Couchbase_App/Collect_Couchbase_Logs_and_Metrics_for_Non-Kubernetes_environments).
    * [Collect Logs and Metrics for Kubernetes environments](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/Couchbase/Collect_Logs_and_Metrics_for_the_Couchbase_App/Collect_Couchbase_Logs_and_Metrics_for_Kubernetes_environments).


#### Configure Fields in Sumo Logic

Create the following Fields in Sumo Logic prior to configuring the collection. This ensures that your logs and metrics are tagged with relevant metadata, which is required by the app dashboards. For information on setting up fields, see the [Fields](https://help.sumologic.com/Manage/Fields) help page.

If you are using Couchbase in a  non-Kubernetes environment create the fields:

* component
* environment
* db_system
* db_cluster
* pod

If you are using Couchbase in a Kubernetes environment create the fields:

* pod_labels_component
* pod_labels_environment
* pod_labels_db_system
* pod_labels_db_cluster


#### Configure Collection for Couchbase

Sumo Logic supports the collection of logs and metrics data from Couchbase in both Kubernetes and non-Kubernetes environments.

Click on the appropriate links below based on the environment where your Couchbase clusters are hosted.

* [Collect Logs and Metrics for Non-Kubernetes environments](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/Couchbase/Collect_Logs_and_Metrics_for_the_Couchbase_App/Collect_Couchbase_Logs_and_Metrics_for_Non-Kubernetes_environments).
* [Collect Logs and Metrics for Kubernetes environments](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/Couchbase/Collect_Logs_and_Metrics_for_the_Couchbase_App/Collect_Couchbase_Logs_and_Metrics_for_Kubernetes_environments).
1.


### Collect Couchbase Logs and Metrics for Kubernetes environments

In a Kubernetes environment, we use the Telegraf Operator, which is packaged with our Kubernetes collection. You can learn more about it[ here](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/01_Telegraf_Collection_Architecture). The following diagram illustrates how data is collected from Couchbase in Kubernetes environments. There are four services that make up the metric collection pipeline: Telegraf, Prometheus, Fluentd, and FluentBit.

The first service in the pipeline is Telegraf. Telegraf collects metrics from Couchbase. Note that we’re running Telegraf in each pod we want to collect metrics from as a sidecar deployment that is Telegraf runs in the same pod as the containers it monitors. Telegraf uses the [Couchbase input plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/couchbase) to obtain metrics. (For simplicity, the diagram doesn’t show the input plugins.) The injection of the Telegraf sidecar container is done by the Telegraf Operator. We also have Fluentbit that collects logs written to standard out and forwards them to FluentD, which in turn sends all the logs and metrics data to a Sumo Logic HTTP Source.


Follow the below instructions to set up the metric collection:



1. Configure Metrics Collection
    1. Setup Kubernetes Collection with the Telegraf operator
    2. Add annotations on your Couchbase pods
2. Configure Logs Collection
    3. Configure logging in Couchbase.
    4. Add labels on your Couchbase pods to capture logs from standard output.
    5. Collecting Couchbase Logs from a Log file.

**Prerequisites**

It’s assumed that you are using the latest helm chart version if not, upgrade using the instructions [here](https://github.com/SumoLogic/sumologic-kubernetes-collection/blob/release-v2.0/deploy/docs/v2_migration_doc.md#how-to-upgrade).

When you upgrade the helm chart, you must upgrade telegraf version to 1.21.1 by adding the statement below:

"--set telegraf-operator.image.sidecarImage=telegraf:1.21.1" in the upgrade command helm chart.


### Configure Metrics Collection

This section explains the steps to collect Couchbase metrics from a Kubernetes environment.

In a Kubernetes environment, we use the Telegraf Operator, which is packaged with our Kubernetes collection. You can learn more on this[ here](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/01_Telegraf_Collection_Architecture). Follow the steps listed below to collect metrics from a Kubernetes environment:


1. **[Setup Kubernetes Collection with the Telegraf Operator.](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Install_Telegraf_in_a_Kubernetes_environment)**
2. **Add annotations on your Couchbase pods**

On your Couchbase Pods, add the following annotations:


```
annotations:
    telegraf.influxdata.com/class: sumologic-prometheus
    prometheus.io/scrape: "true"
    prometheus.io/port: "9273"
    telegraf.influxdata.com/inputs: |+
[[inputs.couchbase]]
servers = ["http://<USER_TO_BE_CHANGED>:<PASS_TO_BE_CHANGED>@A@localhost:8091"]
bucket_stats_included = ["*"]
[inputs.couchbase.tags]
db_cluster="CLUSTER_NAME_TO_BE_CHANGED"
component="database"
environment="ENV_TO_BE_CHANGED"
db_system="couchbase"
```



If you haven’t defined a cluster in Couchbase, then enter ‘**default**’ for db_cluster.

Enter in values for the following parameters (marked in bold above):



* telegraf.influxdata.com/inputs - This contains the required configuration for the Telegraf Couchbase Input plugin. Refer[ ](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/redis)to this [doc](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/couchbase) for more information on configuring the Couchbase input plugin for Telegraf. Note: As telegraf will be run as a sidecar the host should always be localhost.
    * Input plugins section, which is `[[inputs.couchbase]]`:
        * servers: This is the endpoint of the management portal of couchbase server. For detail, see this [doc](https://docs.couchbase.com/server/current/manage/manage-ui/manage-ui.html) .
    * In the tags section,  which is `[inputs.couchbase.tags]`
        * **environment** - This is the deployment environment where the Couchbase cluster identified by the value of **servers** resides. For example: dev, prod or qa. While this value is optional we highly recommend setting it.
        * **db_cluster **- Enter a name to identify this Couchbase cluster. This cluster name will be shown in the Sumo Logic dashboards.  

Here’s an explanation for additional values set by this configuration that we request you **do not modify** as they will cause the Sumo Logic apps to not function correctly.



* **telegraf.influxdata.com/class: sumologic-prometheus** - This instructs the Telegraf operator what output to use. This should not be changed.
* **prometheus.io/scrape: "true"** - This ensures our Prometheus will scrape the metrics.
* **prometheus.io/port: "9273"** - This tells prometheus what ports to scrape on. This should not be changed.
* **telegraf.influxdata.com/inputs**
    * In the tags section, which is `[inputs.couchbase.tags]`
        * **component**: “database” - This value is used by Sumo Logic apps to identify application components.
        * **db_system**: “couchbase” - This value identifies the database system.

For all other parameters see [this doc](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Configuring_Telegraf) for more properties that can be configured in the Telegraf agent globally.



1. Sumo Logic Kubernetes collection will automatically start collecting metrics from the pods having the labels and annotations defined in the previous step.
2. Verify metrics in Sumo Logic.


### Configure Logs Collection

This section explains the steps to collect Couchbase logs from a Kubernetes environment.



1. **(Recommended Method) Add labels on your Couchbase pods to capture logs from standard output.**

Make sure that the logs from Couchbase are sent to stdout. Follow the instructions below to capture Couchbase logs from stdout on Kubernetes.



1. Apply following labels to the Couchbase pod

                        labels:


              <code>environment="**prod**_**CHANGEME**"</code>


              `component="database"`


              `db_system="couchbase"`


              <code>db_cluster="<**cluster_CHANGEME**>"</code>

Enter in values for the following parameters (marked in **bold and CHANGE_ME** above):



* **environment** - This is the deployment environment where the Couchbase cluster identified by the value of **servers** resides. For example:- dev, prod, or QA. While this value is optional we highly recommend setting it.
* **db_cluster **- Enter a name to identify this Couchbase cluster. This cluster name will be shown in the Sumo Logic dashboards. If you haven’t defined a cluster in Couchbase, then enter ‘**default**’ for db_cluster.

Here’s an explanation for additional values set by this configuration that we request you **do not modify** as they will cause the Sumo Logic apps to not function correctly.



* **component**: “database” - This value is used by Sumo Logic apps to identify application components.
* **db_system**: “couchbase” - This value identifies the database system.

For all other parameters see [this doc](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Configuring_Telegraf) for more properties that can be configured in the Telegraf agent globally.



1. The Sumologic-Kubernetes-Collection will automatically capture the logs from stdout and will send the logs to Sumologic. For more information on deploying Sumologic-Kubernetes-Collection,[ visit](https://help.sumologic.com/07Sumo-Logic-Apps/10Containers_and_Orchestration/Kubernetes/Collect_Logs_and_Metrics_for_the_Kubernetes_App) here.
2. Verify logs in Sumo Logic.





1. **(Optional) Collecting Couchbase Logs from a Log File \
**Follow the steps below to capture Couchbase logs from a log file on Kubernetes.
1. Determine the location of the Couchbase log file on Kubernetes. This can be determined from the config file /opt/couchbase/etc/couchbase/static_config squid.conf for your Couchbase cluster along with the mounts on the Couchbase pods.
2. Install the Sumo Logic [tailing sidecar operator](https://github.com/SumoLogic/tailing-sidecar/tree/main/operator#deploy-tailing-sidecar-operator).
3. Add the following annotation in addition to the existing annotations.


```
annotations:
  tailing-sidecar: sidecarconfig;<mount>:<path_of_Couchbase_log_file>/<Couchbase_log_file_name>
Example:
annotations:
  tailing-sidecar: sidecarconfig;data:/opt/couchbase/var/lib/couchbase/logs/audit.log
```


1. Make sure that the Couchbase pods are running and annotations are applied by using the command: **kubectl describe pod <Couchbase_pod_name>**
2. Sumo Logic Kubernetes collection will automatically start collecting logs from the pods having the annotations defined above.
3. Verify logs in Sumo Logic.





1. **Add a FER to normalize the fields in Kubernetes environments \
**Labels created in Kubernetes environments automatically are prefixed with pod_labels. To normalize these for our app to work, we need to create a Field Extraction Rule if not already created for Proxy Application Components. To do so:
1. Go to **Manage Data > Logs > Field Extraction Rules**.
2. Click the + Add button on the top right of the table.
3. The following form appears: \

9

4. Enter the following options:
    1. **Rule Name**. Enter the name as **App Observability - Proxy**.
    2. **Applied At.** Choose **Ingest Time**
    3. **Scope**. Select **Specific Data**
    4. **Scope**: Enter the following keyword search expression:  \
`pod_labels_environment=* pod_labels_component=database pod_labels_db_cluster=* pod_labels_db_system=*`
* **Parse Expression**.Enter the following parse expression: \
`if (!isEmpty(pod_labels_environment), pod_labels_environment, "") as environment \
| pod_labels_component as component \
| pod_labels_db_system as db_system \
| pod_labels_db_cluster as db_cluster`
1. Click **Save** to create the rule.


### Collect Couchbase Logs and Metrics for Non-Kubernetes environments

Sumo Logic uses the Telegraf operator for Couchbase metric collection and the [Installed Collector](https://help.sumologic.com/03Send-Data/Installed-Collectors/01About-Installed-Collectors) for collecting Couchbase logs. The diagram below illustrates the components of the  Couchbase collection in a non-Kubernetes environment. Telegraf uses the[ Couchbase input plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/couchbase) to obtain Couchbase metrics and the Sumo Logic output plugin to send the metrics to Sumo Logic. Logs from Couchbase are collected by a [Local File Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Local-File-Source).


The process to set up collection for Couchbase data is done through the following steps:



1. Configure Logs Collection
    1. Configure logging in Couchbase
    2. Configure Sumo Logic Installed Collector
    3. Configure a local file source
2. Configure Metrics Collection
    4. Configure a Hosted Collector
    5. Configure an HTTP Logs and Metrics Source
    6. Install Telegraf
    7. Configure and start Telegraf


### Configure Logs Collection
11


Couchbase app supports the audit log , query log, error log, access log. For details [here](https://docs.couchbase.com/server/current/manage/manage-logging/manage-logging.html#changing-log-file-locations).



1. **Configure logging in Couchbase.**

By default, the Couchbase will write the log to the log directory that was configured during installation. For example, on Linux, the log directory would be /opt/couchbase/var/lib/couchbase/logs. By default, the Audit log is disabled, you must enable the audit log following these [instructions](https://docs.couchbase.com/server/current/manage/manage-security/manage-auditing.html). Query log, error log, the access log will be enabled by default.



1. **Configure an Installed Collector.** If you have not already done so, install and configure an installed collector for Windows by [following the documentation](https://help.sumologic.com/03Send-Data/Installed-Collectors/03Install-a-Collector-on-Windows).
2. **Configure a Collector**

Use one of the following Sumo Logic Collector options:



1. To collect logs directly from the Couchbase machine, configure an [Installed Collector](https://help.sumologic.com/03Send-Data/Installed-Collectors).
2. If you are using a service like Fluentd, or you would like to upload your logs manually, configure a [Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors/Configure-a-Hosted-Collector).
1. **Configure a local file source**

    **For an Installed Collector**


    To collect logs directly from your Couchbase machine, use an Installed Collector and Multi Local File Source.


    Repeat steps 1 to 4 for each log source: audit log, query log, error log, access log.

1. Add a [Local File Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Local-File-Source).
2. Configure the Local File Source fields as follows:
* **Name**. (Required)
* **Description**. (Optional)
* **File Path (Required)**. Enter the path to your log files: The files are typically located in folder /opt/couchbase/var/lib/couchbase/logs.
    * For Audit Log: /opt/couchbase/var/lib/couchbase/logs/audit.log
    * For Error Log: /opt/couchbase/var/lib/couchbase/logs/error.log
    * For Access Log: /opt/couchbase/var/lib/couchbase/logs/http_access.log
    * For Query Log: /opt/couchbase/var/lib/couchbase/logs/query.log
* **Source Host**. Sumo Logic uses the hostname assigned by the OS unless you enter a different hostname.
* **Source Category**. Enter any string to tag the output collected from this Source, such as Couchbase/AccessLog for access log. (The Source Category metadata field is a fundamental building block to organize and label Sources. For details see [Best Practices](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category).)
* **Fields. **Set the following fields

```
component = database \
db_system = couchbase \
db_cluster = <Your_Couchbase_Cluster_Name>
```
Enter **Default** if you do not have one. \
`<environment = <Your_Environment_Name>` (for example, Dev, QA, or Prod). \

12

1. Configure the **Advanced **section:
* **Enable Timestamp Parsing**. Select Extract timestamp information from log file entries.
* **Time Zone**. Automatically detect.
* **Timestamp Format**. The timestamp format is automatically detected.
* **Encoding**. Select UTF-8 (Default).
* **Enable Multiline Processing**.
    * **Error logs**. Select **Detect messages spanning multiple lines** and **Infer Boundaries - Detect message boundaries automatically**.
    * **Access logs.** These are single-line logs, uncheck **Detect messages spanning multiple lines**.
1. Click **Save**.

**For a Hosted Collector**

If you are using a service like Fluentd, or you would like to upload your logs manually, use a Hosted Collector and an HTTP Source.



1. Add an [HTTP Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/HTTP-Source).
2. Configure the HTTP Source fields as follows:
* **Name**. (Required)
* **Description**. (Optional)
* **Source Host**. Sumo Logic uses the hostname assigned by the OS unless you enter a different hostname.
* **Source Category**. Enter any string to tag the output collected from this Source, such as Couchbase/AccessLog for access log. (The Source Category metadata field is a fundamental building block to organize and label Sources. For details see [Best Practices](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category).)
1. Configure the **Advanced **section:
* **Enable Timestamp Parsing**. Select **Extract timestamp information from log file entries**.
* **Time Zone**. For Access logs, use the time zone from the log file. For Error logs, make sure to select the correct time zone.
* **Timestamp Format**. The timestamp format is automatically detected.
* **Enable Multiline Processing**.
    * **Error logs**: Select **Detect messages spanning multiple lines** and **Infer Boundaries - Detect message boundaries automatically**.
    * **Access logs**: These are single-line logs, uncheck **Detect messages spanning multiple lines**.
1. Click **Save**.
2. When the URL associated with the HTTP Source is displayed, copy the URL so you can add it to the service you are using, such as Fluentd.


### Configure Metrics Collection
13



#### Set up a Sumo Logic HTTP Source


1. **Configure a Hosted Collector for Metrics.**
To create a new Sumo Logic hosted collector, perform the steps in the [Configure a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors/Configure-a-Hosted-Collector) documentation.
2. **Configure an HTTP Logs & Metrics source**:
    1. On the created Hosted Collector on the Collection Management screen, select **Add Source**.
    2. Select **HTTP Logs & Metrics.**
        1. **Name.** (Required). Enter a name for the source.
        2. **Description.** (Optional).
        3. **Source Category** (Recommended). Be sure to follow the [Best Practices for Source Categories](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category). A recommended Source Category may be Prod/DBServer/Couchbase/Metrics.
    3. Select **Save**.
    4. Take note of the URL provided once you click _Save_. You can retrieve it again by selecting the **Show URL** next to the source on the Collection Management screen.


#### Set up Telegraf

1. **Install Telegraf if you haven’t already. **Use the[ following steps](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf) to install Telegraf.
2. **Configure and start Telegraf. \
**As part of collecting metrics data from Telegraf, we will use the[ Couchbase input plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/couchbase) to get data from Telegraf and the [Sumo Logic output plugin](https://github.com/SumoLogic/fluentd-output-sumologic) to send data to Sumo Logic. \
 \
Create or modify `telegraf.conf` and copy and paste the text below:


```
 [[inputs.couchbase]]
servers = ["http://<USER_TO_BE_CHANGED>:<PASS_TO_BE_CHANGED>@localhost:8091"]
bucket_stats_included = ["*"]
[inputs.couchbase.tags]
   db_cluster="<ClusterName_TO_BE_CHANGED>"
   component="database"
   environment="<env_TO_BE_CHANGED>"
   db_system="couchbase"

[[outputs.sumologic]]
  url = "<URL_from_HTTP_Logs_and_Metrics_Source>"
  data_format = "prometheus"
```


Enter values for fields annotated with `<VALUE_TO_BE_CHANGED>` to the appropriate values. Do not include the brackets (`< >`) in your final configuration


* Input plugins section, which is `[[inputs.couchbase]]`:
    * servers: This is the endpoint of the management portal of couchbase server. For details, see this [doc](https://docs.couchbase.com/server/current/manage/manage-ui/manage-ui.html) .
* In the tags section, which is `[inputs.couchbasesnmp.tags]`:
* **environment** - This is the deployment environment where the Couchbase server identified by the value of **servers** resides. For example; dev, prod, or QA. While this value is optional we highly recommend setting it.
* **db_cluster **- Enter a name to identify this Couchbase cluster. This cluster name will be shown in our dashboards.
* In the output plugins section, which is `[[outputs.sumologic]]`:
    * **URL** - This is the HTTP source URL created previously. See [this doc](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/05_Configure_Telegraf_Output_Plugin_for_Sumo_Logic) for more information on additional parameters for configuring the Sumo Logic Telegraf output plugin.


16
If you haven’t defined a cluster in Couchbase, then enter ‘**default**’ for db_cluster.

Here’s an explanation for additional values set by this Telegraf configuration.


17
If you haven’t defined a cluster in Couchbase, then enter ‘**default**’ for db_cluster.
18
There are additional values set by the Telegraf configuration.  We recommend not to modify these values as they might cause the Sumo Logic app to not function correctly.



* **data_format**: “prometheus” - In the output `[[outputs.sumologic]]` plugins section. Metrics are sent in the Prometheus format to Sumo Logic.
* **component** - “database” - In the input `[[inputs.couchbase]]` plugins section. This value is used by Sumo Logic apps to identify application components.
* **db_system **- “couchbase” - In the input plugins sections. This value identifies the database system.

See[ this doc](https://github.com/influxdata/telegraf/blob/master/etc/telegraf.conf) for all other parameters that can be configured in the Telegraf agent globally.


19
After you have finalized your `telegraf.conf` file, you can start or reload the telegraf service using instructions from this[ doc](https://docs.influxdata.com/telegraf/v1.17/introduction/getting-started/#start-telegraf-service).

At this point, Telegraf should start collecting the Couchbase metrics and forward them to the Sumo Logic HTTP Source.


## Couchbase Alerts

Sumo Logic has provided out-of-the-box alerts available via[ Sumo Logic monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) to help you quickly determine if the Couchbase database cluster is available and performing as expected.


<table>
  <tr>
   <td>Alert Type (Metrics/Logs)
   </td>
   <td>Alert Name
   </td>
   <td>Alert Description
   </td>
   <td>Trigger Type (Critical / Warning)
   </td>
   <td>Alert Condition
   </td>
   <td>Recover Condition
   </td>
  </tr>
  <tr>
   <td>Logs
   </td>
   <td>Couchbase - Bucket Not Ready
   </td>
   <td>This alert fires when a bucket in the Couchbase cluster is not ready.
   </td>
   <td>Critical
   </td>
   <td> &#60; 0
   </td>
   <td>&#60; &#61;0
   </td>
  </tr>
  <tr>
   <td>Logs
   </td>
   <td>Couchbase - Node Down
   </td>
   <td>This alert fires when a node in the Couchbase cluster is down.
   </td>
   <td>Critical
   </td>
   <td>&#62;
   </td>
   <td>&#60; &#61;0
   </td>
  </tr>
  <tr>
   <td>Logs
   </td>
   <td>Couchbase - Node Not Respond
   </td>
   <td>This alert fires when a node in the Couchbase cluster does not respond too many times.
   </td>
   <td>Critical
   </td>
   <td>&#62; &#61; 10
   </td>
   <td>&#60; 10
   </td>
  </tr>
  <tr>
   <td>Logs
   </td>
   <td>Couchbase - Too Many Error Queries on Buckets
   </td>
   <td>This alert fires when there are too many errors queries on a bucket in a Couchbase cluster.
   </td>
   <td>Critical
   </td>
   <td>&#62; &#61; 1000
   </td>
   <td>&#60; 1000
   </td>
  </tr>
  <tr>
   <td>Logs
   </td>
   <td>Couchbase - Too Many Login Failures
   </td>
   <td>This alert fires when there are too many login failures to a node in a Couchbase cluster.
   </td>
   <td>Critical
   </td>
   <td>&#62; &#61; 1000
   </td>
   <td>&#60; 1000
   </td>
  </tr>
  <tr>
   <td>Metrics
   </td>
   <td>Couchbase - High CPU Usage
   </td>
   <td>This alert fires when CPU usage on a node in a Couchbase cluster is high.
   </td>
   <td>Critical
   </td>
   <td>&#62; &#61; 80
   </td>
   <td>&#60; 80
   </td>
  </tr>
  <tr>
   <td>Metrics
   </td>
   <td>Couchbase - High Memory Usage
   </td>
   <td>This alert fires when memory usage on a node in a Couchbase cluster is high.
   </td>
   <td>Critical
   </td>
   <td>&#62; &#61; 80
   </td>
   <td>&#60; 80
   </td>
  </tr>
</table>



## Install the Couchbase Monitors, App, and view the Dashboards

This page provides instructions for installing the Couchbase App, as well as examples of each of the App dashboards. These instructions assume you have already set up the collection as described in the [Collect Logs and Metrics for the Couchbase](https://help.sumologic.com/07Sumo-Logic-Apps/24Web_Servers/Squid_Proxy/Collect_Logs_for_Squid_Proxy) App page.


### Pre-Packaged Alerts

Sumo Logic has provided out-of-the-box alerts available through [Sumo Logic monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) to help you monitor your Couchbase clusters. These alerts are built based on metrics and logs datasets and include preset thresholds based on industry best practices and recommendations.

For details on the individual alerts, see this [page](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/Couchbase/Couchbase_Alerts).


#### Installing Monitors

* To install these alerts, you need to have the Manage Monitors role capability.
* Alerts can be installed by either importing a JSON file or a Terraform script.


22
There are limits to how many alerts can be enabled - see the [Alerts FAQ](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors/Monitor_FAQ) for details.


##### Install the monitors by importing a JSON file method

1. Download the [JSON file](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/blob/main/monitor_packages/Couchbase/couchbase.json) that describes the monitors.
2. The [JSON](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/blob/main/monitor_packages/Couchbase/couchbase.json) contains the alerts that are based on Sumo Logic searches that do not have any scope filters and therefore will be applicable to all Couchbase clusters, the data for which has been collected via the instructions in the previous sections.  However, if you would like to restrict these alerts to specific clusters or environments, update the JSON file by replacing the text `db_system=couchbase` with `<Your Custom Filter>`.

    Custom filter examples:

1. For alerts applicable only to a specific cluster, your custom filter would be, `'db_cluster=couchbase-standalone.01'`.
2. For alerts applicable to all cluster that start with couchbase-standalone, your custom filter would be,`db_cluster=couchbase-standalone*`.
3. For alerts applicable to a specific cluster within a production environment, your custom filter would be**,`db_cluster=couchbase-1`** and `environment=standalone` (This assumes you have set the optional environment tag while configuring collection).
4. Go to Manage Data > Alerts > Monitors.
5. Click **Add**:
6. Click Import and then copy-paste the above JSON to import monitors.


The monitors are disabled by default. Once you have installed the alerts using this method, navigate to the Couchbase folder under **Monitors** to configure them. See [this](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) document to enable monitors to send notifications to teams or connections. See the instructions detailed in [Step 4](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/Couchbase/Install_the_Couchbase_Monitors%2C_App%2C_and_view_the_Dashboards#Step+4) of this [document](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors#Add_a_monitor).


##### Install the alerts using a Terraform script method.


1. **Generate a Sumo Logic access key and ID \
**Generate an access key and access ID for a user that has the Manage Monitors role capability in Sumo Logic using these[ instructions](https://help.sumologic.com/Manage/Security/Access-Keys#manage-your-access-keys-on-preferences-page). Identify which deployment your Sumo Logic account is in, using this [link](https://help.sumologic.com/APIs/General-API-Information/Sumo-Logic-Endpoints-by-Deployment-and-Firewall-Security).
2. **[Download and install Terraform 0.13](https://www.terraform.io/downloads.html) or later **
3. **Download the Sumo Logic Terraform package for Couchbase alerts \
**The alerts package is available in the Sumo Logic GitHub [repository](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/tree/main/monitor_packages/SquidProxy). You can either download it through the “git clone” command or as a zip file.
4. **Alert Configuration  \
**After the package has been extracted, navigate to the package directory **terraform-sumologic-sumo-logic-monitor/monitor_packages/Couchbase/ \
 \
**Edit the **couchbase.auto.tfvars** file and add the Sumo Logic Access Key, Access Id, and Deployment from Step 1. \
 \
`access_id   = "<SUMOLOGIC ACCESS ID>" \
access_key  = "<SUMOLOGIC ACCESS KEY>" \
environment = "<SUMOLOGIC DEPLOYMENT>" \
 \
`The Terraform script installs the alerts without any scope filters, if you would like to restrict the alerts to specific farms or environments, update the variable **’couchbase_data_source’**. Custom filter examples:
    1. A specific cluster **<code>'db_cluster=couchbase.standalone.01'</code>.**
    2. All clusters in an environment **<code>'environment=standalone</code>**.
    3. For alerts applicable to all clusters that start with couchbase-standalone, your custom filter would be: <code>'db_cluster=couchbase-standalone*'</code>.
    4. For alerts applicable to a specific cluster within a production environment, your custom filter would be:

<code>db_system=couchbase</code> and <code>environment=standalone</code> (This assumes you have set the optional environment tag while configuring collection)

All monitors are disabled by default on installation, if you would like to enable all the monitors, set the parameter **monitors_disabled** to **false** in this file.

By default, the monitors are configured in a monitor **folder** called “**Couchbase**”, if you would like to change the name of the folder, update the monitor folder name in “folder” key at **<code>couchbase.auto.tfvars</code>** file.

If you would like the alerts to send email or connection notifications, configure these in the file **<code>couchbase_notifications.auto.tfvars</code>**. For configuration examples, refer to the next section.



1. **Email and Connection Notification Configuration Examples**

Modify the file **couchbase_notifications.auto.tfvars** and populate connection_notifications and email_notifications as per below examples.


###### **Pagerduty Connection Example: **
27



```
connection_notifications = [
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


Replace <CONNECTION_ID> with the connection id of the webhook connection. The webhook connection id can be retrieved by calling the [Monitors API](https://api.sumologic.com/docs/#operation/listConnections).

For overriding payload for different connection types, refer to this [document](https://help.sumologic.com/Manage/Connections-and-Integrations/Webhook-Connections/Set_Up_Webhook_Connections).


###### **Email Notifications Example: **
28



```
email_notifications = [
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



1. **Install the Alerts**
1. Navigate to the package directory terraform-sumologic-sumo-logic-monitor/monitor_packages/**Couchbase**/ and run **terraform init. **This will initialize Terraform and will download the required components.
2. Run **terraform plan **to view the monitors which will be created/modified by Terraform.
3. Run **terraform apply**.
1. **Post Installation**

If you haven’t enabled alerts and/or configured notifications through the Terraform procedure outlined above, we highly recommend enabling alerts of interest and configuring each enabled alert to send notifications to other users or services. This is detailed in Step 4 of [this document](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors#Add_a_monitor).


29
There are limits to how many alerts can be enabled - see the [Alerts FAQ](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors/Monitor_FAQ).


### Install the Sumo Logic App
30


This section demonstrates how to install the Couchbase App.

**To install the app:**

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.



1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.


31
Version selection is applicable only to a few apps currently. For more information, see the[ Install the Apps from the Library](https://help.sumologic.com/01Start-Here/Library/Apps-in-Sumo-Logic/Install-Apps-from-the-Library).

1. To install the app, complete the following fields.
    1. **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
    2. **Data Source.**
        * Choose **Enter a Custom Data Filter**, and enter a custom Couchbase cluster filter. Examples:
            1. For all Couchbase clusters \
`db_cluster=*`
            2. For a specific cluster: \
`db_cluster=couchbase.dev.01`
            3. Clusters within a specific environment: \
`db_cluster=couchbase.dev.01` and `environment=prod \
`(This assumes you have set the optional environment tag while configuring collection)
    3. **Advanced**. Select the **Location in the Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
    4. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or another folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.


### Dashboard Filter with Template Variables  

Template variables provide dynamic dashboards that rescope data on the fly. As you apply variables to troubleshoot through your dashboard, you can view dynamic changes to the data for a fast resolution to the root cause. For more information, see the[ Filter with template variables](https://help.sumologic.com/Visualizations-and-Alerts/Dashboard_(New)/Filter_with_template_variables) help page.


33
You can use template variables to drill down and examine the data on a granular level.


### Dashboards
34



#### Couchbase - Overview
35


The **Couchbase - Overview** dashboard provides an at-a-glance view of the health of the Couchbase clusters and servers, performance, and problems causing errors.

Use this dashboard to:



* Gain insights into information about the number of nodes, number of buckets, connections, number items, total bytes transferred.
* Determine errors in clusters: enjections, out of memory errors and error queries.
* Gain insights into information about the workload of the cluster: percent of used memory, percent of used CPU.

#### Couchbase - Bucket I/O

The **Couchbase -  Bucket I/O** dashboard provides an insight into the operators of buckets in clusters: the number of getting operations, the number of set operations, the number of delete operations, the bytes read/write.

Use this dashboard to:



* Get insights into information about the total amount of operations in buckets per second;  the number of delete misses operations, get operations, set operations, update operations in buckets per second.
* Get insights into information about the number of bytes read, bytes written over time.


38



#### Couchbase - Cluster Resources
39


The **Couchbase -  Cluster Resources** dashboard provides an insight into the resources of clusters: the memory resource usage, the CPU resource usage, the disk resource usage.

Use this dashboard to:



* Gain insights into the workload of Couchbase clusters such as the percent of CPU used, the percent of Memory used, the High Low watermark.
* Gain insights into the used resources of Couchbase clusters such as the Disk usage, the Swap space usage, the Memory available.
* Gain insights into the rate requests, rate of streaming requests on the management port.


40



#### Couchbase - DCP Queues
41


The **Couchbase -  DCP Queues** dashboard provides an insight into the DCP queues of buckets in couchbase clusters: the number of DCP connections, DCP senders, the number of items in DCP Queues.

Use this dashboard to:



* Gain insights into the operations of DCP queues. This helps you identify the performance of your clusters when your cluster rebalance


42



#### Couchbase - Disk Queues
43


The **Couchbase - Disk Queues **dashboard provides an insight into the DCP queues of buckets in couchbase clusters: the number of active items waiting to be written to disk, the number of items being put to disk queue, the average age of items in queues.

Use this dashboard to:



* Gain insights into the operations of disk queues. This helps you identify performance about read/write of your clusters.


44



#### Couchbase - vBucket
45


The **Couchbase -  vBucket** dashboard provides insights into the state of vBucket of buckets in couchbase clusters: the number of vBucket of buckets, the number items in vBuckets, the state of vBuckets.

Use this dashboard to:



* To determine the number and status of vBucket in your clusters.


46



#### Couchbase - XDCR
47


The **Couchbase -  XDCR** dashboard provides insights into replicate operations of buckets cross-cluster: the number of XDCR connections, the number of XDCR items remaining, the number of read-set-delete operations for XDCR.

Use this dashboard to:



* Gain insights into replicate operations of buckets cross-cluster


48



#### Couchbase - Errors
49


The **Couchbase -  Errors** dashboard provides insights into errors from error logs in couchbase servers and couchbase clusters: buckets not ready, nodes not responding, node down, error queries, last error logs.

Use this dashboard to:



* Quickly identify critical errors affecting your couchbase servers.
* Identify SQL error queries from clients.


50



#### Couchbase - Events
51


The **Couchbase -  Events** dashboard provides insights into events from couchbase servers and couchbase clusters: the number of login failure, login success from clients, add/remove node events, add/remove bucket events, rebalance events.

Use this dashboard to:



* To audit the activities happening in the cluster. This helps to determine what activities have occurred in the system, helping to control system security.


52



#### Couchbase - HTTP Access
53


The **Couchbase -  HTTP Access** dashboard provides insights into HTTP Rest API requests from clients to couchbase servers and couchbase clusters: the latency, HTTP codes, client agents, IP clients, errors with 4XX 5XX response code.

Use this dashboard to:



* To understand user behavior accessing clusters and servers through Rest API.


54