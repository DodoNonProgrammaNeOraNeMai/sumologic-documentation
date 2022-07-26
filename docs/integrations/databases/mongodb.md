---
id: mongodb
title: Sumo Logic App for MongoDB
sidebar_label: MongoDB
description: The Sumo Logic App for MongoDB provides insight into your MongoDB environment, allowing you to track overall system health, queries, logins and connections, errors and warnings, replication, and sharding.
---

import useBaseUrl from '@docusaurus/useBaseUrl';

<img src={useBaseUrl('img/integrations/databases/mongodb.png')} alt="DB icon" width="100"/>

MongoDB is a source-available cross-platform document-oriented database program.


Log Types

The app supports Logs and Metrics from the open source version of MongoDB. The App is tested on the 4.4.4 version of MongoDB.

The MongoDB logs are generated in files as configured in the configuration file `/var/log/mongodb/mongodb.log`. For more details on MongoDB logs, see[ this](https://docs.mongodb.com/manual/reference/log-messages/) link.

The Sumo Logic App for MongoDB supports metrics generated by the[ MongoDB plugin for Telegraf](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mongodb). The app assumes prometheus format Metrics.


## Collect Logs and Metrics for MongoDB

This page provides instructions for configuring log and metric collection for the Sumo Logic App for MongoDB.


### Collection Process Overview

Configuring log and metric collection for the MongoDB App includes the following tasks:

* Step 1: Configure Fields in Sumo Logic.
* Step 2: Configure Collection for MongoDB
    * [Collect Logs and Metrics for Non-Kubernetes environments.](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/Collect-Logs-for-MongoDB/Collect_MongoDB_Logs_and_Metrics_for_Non-Kubernetes_environments)
    * [Collect Logs and Metrics for Kubernetes environments.](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/Collect-Logs-for-MongoDB/Collect_MongoDB_Logs_and_Metrics_for_Kubernetes_environments)


#### Step 1: Configure Fields in Sumo Logic

Create the following Fields in Sumo Logic prior to configuring collection. This ensures that your logs and metrics are tagged with relevant metadata, which is required by the app dashboards. For information on setting up fields, see the [Fields](https://help.sumologic.com/Manage/Fields) help page.

If you are using MongoDB in a non-Kubernetes environment create the fields:

* component
* environment
* db_system
* db_cluster

If you are using MongoDB in a Kubernetes environment create the fields:



* pod_labels_component
* pod_labels_environment
* pod_labels_db_system
* pod_labels_db_cluster


#### Step 2: Configure Collection for MongoDB

* [Collect Logs and Metrics for Non-Kubernetes environments.](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/Collect-Logs-for-MongoDB/Collect_MongoDB_Logs_and_Metrics_for_Non-Kubernetes_environments)
* [Collect Logs and Metrics for Kubernetes environments.](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/Collect-Logs-for-MongoDB/Collect_MongoDB_Logs_and_Metrics_for_Kubernetes_environments)


### Sample Log Message


```
{"t":{"$date":"2021-05-21T10:22:57.373+00:00"},"s":"I","c":"NETWORK","id":51800,"ctx":"conn500659","msg":"client metadata","attr":{"remote":"127.0.0.1:49472","client":"conn500659","doc":{"application":{"name":"MongoDB Shell"},"driver":{"name":"MongoDB Internal Client","version":"4.4.4"},"os":{"type":"Linux","name":"PRETTY_NAME=\"Debian GNU/Linux 10 (buster)\"","architecture":"x86_64","version":"Kernel 4.4.0-62-generic"}}}}
```



### Sample Query  


Dashboard: MongoDB - Errors and Warnings, Panel: Errors by Component


```
environment=* db_cluster=* db_system=mongodb  | json "log" as _rawlog nodrop
| if (isEmpty(_rawlog), _raw, _rawlog) as _raw
| json field=_raw "t.$date" as timestamp
| json field=_raw "s" as severity
| json field=_raw "c" as component
| json field=_raw "ctx" as context
| json field=_raw "msg" as msg
| where severity in ("E")
| count by component
```



### Collect MongoDB Logs and Metrics for Kubernetes environments

In a Kubernetes environment, we use the Telegraf Operator, which is packaged with our Kubernetes collection. You can learn more about it[ here](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/01_Telegraf_Collection_Architecture).The diagram below illustrates how data is collected from MongoDB in a Kubernetes environment. In the architecture shown below, there are four services that make up the metric collection pipeline: Telegraf, Prometheus, Fluentd and FluentBit.

The first service in the pipeline is Telegraf. Telegraf collects metrics from MongoDB. Note that we’re running Telegraf in each pod we want to collect metrics from as a sidecar deployment for example, Telegraf runs in the same pod as the containers it monitors. Telegraf uses the MongoDB input plugin to obtain metrics. (For simplicity, the diagram doesn’t show the input plugins.) The injection of the Telegraf sidecar container is done by the Telegraf Operator. We also have Fluentbit that collects logs written to standard out and forwards them to FluentD, which in turn sends all the logs and metrics data to a Sumo Logic HTTP Source.

Follow the below instructions to set up the metric collection:



1. Configure Metrics Collection
    1. Setup Kubernetes Collection with the Telegraf operator
    2. Add annotations on your MongoDB pods
2. Configure Logs Collection
    3. Configure logging in MongoDB.
    4. Add labels on your MongoDB pods to capture logs from standard output.
    5. Collecting MongoDB Logs from a Log file.

**Prerequisites**

It’s assumed that you are using the latest helm chart version if not upgrade using the instructions [here](https://github.com/SumoLogic/sumologic-kubernetes-collection/blob/release-v2.0/deploy/docs/v2_migration_doc.md#how-to-upgrade).


### Step 1 Configure Metrics Collection



This section explains the steps to collect MongoDB metrics from a Kubernetes environment.

In a Kubernetes environment, we use the Telegraf Operator, which is packaged with our Kubernetes collection. You can learn more on this[ here](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/01_Telegraf_Collection_Architecture). Follow the steps listed below to collect metrics from a Kubernetes environment:



1. [Setup Kubernetes Collection with the Telegraf Operator.](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Install_Telegraf_in_a_Kubernetes_environment)
2. Add annotations on your MongoDB pods

    On your MongoDB Pods, add the following annotations:



```
annotations:
  telegraf.influxdata.com/class: sumologic-prometheus
  prometheus.io/scrape: "true"
  prometheus.io/port: "9273"
  telegraf.influxdata.com/inputs: |+
[[inputs.mongodb]]
  servers = ["mongodb://<username-CHANGEME>:<password-CHANGEME>@127.0.0.1:27017"]
  gather_perdb_stats = true
  gather_col_stats = true
  [inputs.mongodb.tags]
    environment="kubernetes"
    component="database"
    db_system="mongodb"
    db_cluster="mongodb_on_k8s"
```



    Please enter values for the following parameters (marked in bold above):



* telegraf.influxdata.com/inputs - This contains the required configuration for the Telegraf MongoDB Input plugin. Please refer[ to this doc](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/redis) for more information on configuring the MongoDB input plugin for Telegraf. Note: As telegraf will be run as a sidecar the host should always be localhost.
    * In the input plugins section - [inputs.MongoDB]:
        * servers - The URL to the MongoDB server. This can be a comma-separated list to connect to multiple MongoDB servers. Please see [this doc](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mongodb) for more information on additional parameters for configuring the MongoDB input plugin for Telegraf.
    * In the tags section - [inputs.MongoDB.tags]:
        * environment - This is the deployment environment where the MongoDB cluster identified by the value of **servers** resides. For example: dev, prod or qa. While this value is optional we highly recommend setting it.
        * db_cluster - Enter a name to identify this MongoDB cluster. This cluster name will be shown in the Sumo Logic dashboards.

    Here’s an explanation for additional values set by this configuration that we request you **please do not modify** as they will cause the Sumo Logic apps to not function correctly.

* telegraf.influxdata.com/class: sumologic-prometheus - This instructs the Telegraf operator what output to use. This should not be changed.
* prometheus.io/scrape: "true" - This ensures our Prometheus will scrape the metrics.
* prometheus.io/port: "9273" - This tells prometheus what ports to scrape on. This should not be changed.
* telegraf.influxdata.com/inputs
    * In the tags section i.e.  [inputs.mongodb.tags]
        * component: “database” - This value is used by Sumo Logic apps to identify application components.
        * db_system: “mongodb” - This value identifies the database system.

    For all other parameters please see [this doc](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Configuring_Telegraf) for more properties that can be configured in the Telegraf agent globally.

1. Sumo Logic Kubernetes collection will automatically start collecting metrics from the pods having the labels and annotations defined in the previous step.
2. Verify metrics in Sumo Logic.


### Step 2 Configure Logs Collection
9


This section explains the steps to collect MongoDB logs from a Kubernetes environment.



1. Add labels on your MongoDB pods to capture logs from standard output.

    Make sure that the logs from MongoDB are sent to stdout. For more details see this [doc](https://docs.mongodb.com/manual/reference/log-messages/).


    Follow the instructions below to capture MongoDB logs from stdout on Kubernetes.

1. Apply following labels to the MongoDB pods:


```
labels:
    environment: "prod"
    component: "database"
    db_system: "mongodb"
    db_cluster: "mongodb_prod_cluster01"
```



    Enter in values for the following parameters (marked in bold above):



* **environment.** This is the deployment environment where the MongoDB cluster identified by the value of **servers** resides. For example: dev, prod or qa. While this value is optional we highly recommend setting it.
* **db_cluster.** Enter a name to identify this MongoDB cluster. This cluster name will be shown in the Sumo Logic dashboards.

    Here’s an explanation for additional values set by this configuration that we request you **do not modify** as they will cause the Sumo Logic apps to not function correctly.

* **component: “database”**. This value is used by Sumo Logic apps to identify application components.
* **db_system: “mongodb”**. This value identifies the database system.

    For all other parameters see[ this doc](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf#Configuring_Telegraf) for more properties that can be configured in the Telegraf agent globally.

1. (Optional) Collecting MongoDB Logs from a Log File

    Follow the  steps below to capture MongoDB logs from a log file on Kubernetes.

1. Determine the location of the MongoDB log file on Kubernetes. This can be determined from the MongoDB.conf for your MongoDB cluster along with the mounts on the MongoDB pods.
2. Install the Sumo Logic [tailing sidecar operator](https://github.com/SumoLogic/tailing-sidecar/tree/main/operator#deploy-tailing-sidecar-operator).
3. Add the following annotation in addition to the existing annotations.


```
annotations:
  tailing-sidecar: sidecarconfig;<mount>:<path_of_MongoDB_log_file>/<MongoDB_log_file_name>
```



        Example:


```
annotations:
  tailing-sidecar: sidecarconfig;data:/mongo-prim-data/MongoDB.log

```



1. Make sure that the MongoDB pods are running and annotations are applied by using the command: **kubectl describe pod <MongoDB_pod_name>**
2. Sumo Logic Kubernetes collection will automatically start collecting logs from the pods having the annotations defined above.
1. Add an FER to normalize the fields in Kubernetes environments

    Labels created in Kubernetes environments automatically are prefixed with pod_labels. To normalize these for our app to work, we need to create a Field Extraction Rule if not already created for Database Application Components. To do so:

1. Go to **Manage Data > Logs > Field Extraction Rules**.
2. Click the + Add button on the top right of the table.
3. The following form appears:


10


1. Enter the following options:
* **Rule Name**. Enter the name as **App Observability - Database**.
* **Applied At.** Choose **Ingest Time**
* **Scope**. Select **Specific Data**
    * **Scope**: Enter the following keyword search expression: \
pod_labels_environment=* pod_labels_component=database     pod_labels_db_system=* pod_labels_db_cluster=*
* **Parse Expression**.Enter the following parse expression:


```
| if (!isEmpty(pod_labels_environment), pod_labels_environment, "") as environment
| pod_labels_component as component
| pod_labels_db_system as db_system
| pod_labels_db_cluster as db_cluster

```



1. Click **Save** to create the rule.


### Collect MongoDB Logs and Metrics for Non-Kubernetes environments

We use the Telegraf operator for MongoDB metric collection and Sumo Logic Installed Collector for collecting MongoDB logs. The diagram below illustrates the components of the MongoDB collection in a non-Kubernetes environment. Telegraf runs on the same system as MongoDB, and uses the[ MongoDB input plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mongodb) to obtain MongoDB metrics, and the Sumo Logic output plugin to send the metrics to Sumo Logic. Logs from MongoDB on the other hand are sent to either a Sumo Logic Local File source or Syslog source.


11


This section provides instructions for configuring metrics collection for the Sumo Logic App for MongoDB. Follow the below instructions to set up the metric collection:



1. Configure Metrics Collection
    1. Configure a Hosted Collector
    2. Configure an HTTP Logs and Metrics Source
    3. Install Telegraf
    4. Configure and start Telegraf
2. Configure Logs Collection
    5. Configure logging in MongoDB
    6. Configure Sumo Logic Installed Collector


### Step 1 Configure Metrics Collection
12




1. Configure a Hosted Collector

    To create a new Sumo Logic hosted collector, perform the steps in the[ Configure a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors/Configure-a-Hosted-Collector) section of the Sumo Logic documentation.

1. Configure an HTTP Logs and Metrics Source

    Create a new HTTP Logs and Metrics Source in the hosted collector created above by following[ these instructions. ](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/HTTP-Source)Make a note of the **HTTP Source URL**.

1. Install Telegraf

    Use the[ following steps](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/03_Install_Telegraf) to install Telegraf.

1. Configure and start Telegraf

    As part of collecting metrics data from Telegraf, we will use the [MongoDB input plugin](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mongodb) to get data from Telegraf and the [Sumo Logic output plugin](https://github.com/SumoLogic/fluentd-output-sumologic) to send data to Sumo Logic.


    Create or modify telegraf.conf and copy and paste the text below:  



```
[[inputs.mongodb]]
  servers = ["mongodb://<username-CHANGME>:<password-CHANGEME>@127.0.0.1:27017"]
  gather_perdb_stats = true
  gather_col_stats = true
  [inputs.mongodb.tags]
    environment="prod"
    component="database"
    db_system="mongodb"
    db_cluster="mongodb_on_premise"

[[outputs.sumologic]]
  url = "<URL Created in Step b>"
  data_format = "prometheus"
```

Enter values for the following parameters (marked in **bold** above):



* In the input plugins section - [inputs.mongodb]:
    * servers - The URL to the MongoDB server. This can be a comma-separated list to connect to multiple MongoDB servers. Please see [this doc](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/mongodb) for more information on additional parameters for configuring the MongoDB input plugin for Telegraf.
    * In the tags section - [inputs.mongodb.tags]
        * environment - This is the deployment environment where the MongoDB cluster identified by the value of **servers** resides. For example: dev, prod or qa. While this value is optional we highly recommend setting it.
        * db_cluster - Enter a name to identify this MongoDB cluster. This cluster name will be shown in the Sumo Logic dashboards.
* In the output plugins section - [outputs.sumologic]:
    * url - This is the HTTP source URL created in step 3. Please see [this doc](https://help.sumologic.com/03Send-Data/Collect-from-Other-Data-Sources/Collect_Metrics_Using_Telegraf/05_Configure_Telegraf_Output_Plugin_for_Sumo_Logic) for more information on additional parameters for configuring the Sumo Logic Telegraf output plugin.

    Here’s an explanation for additional values set by this Telegraf configuration that we request you **please do not modify** as they will cause the Sumo Logic apps to not function correctly.

* data_format - “prometheus” In the output plugins section - [outputs.sumologic] Metrics are sent in the Prometheus format to Sumo LogicComponent: “database” - In the input plugins section - [inputs.MongoDB]. This value is used by Sumo Logic apps to identify application components.
* gather_perdb_stats: “true” - When true, collect per database stats.
* gather_col_stats: “true” - When true, collect per collection stats.
*  For all other parameters please see [this doc](https://github.com/influxdata/telegraf/blob/master/etc/telegraf.conf) for more properties that can be configured in the Telegraf agent globally.

    Once you have finalized your telegraf.conf file, you can start or reload the telegraf service using instructions from the [doc](https://docs.influxdata.com/telegraf/v1.17/introduction/getting-started/#start-telegraf-service).


    At this point, MongoDB metrics should start flowing into Sumo Logic.



### Step 2 Configure Logs Collection
13



    This section provides instructions for configuring log collection for MongoDB running on a non-kubernetes environment for the Sumo Logic App for MongoDB.


    By default, MongoDB logs are stored in a log file. MongoDB also supports forwarding logs via Syslog.


    Sumo Logic supports collecting logs both via Syslog and a local log file. Utilizing Sumo Logic [Cloud Syslog](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-Syslog-Source) will require TCP TLS Port 6514 to be open in your network. Local log files can be collected via [Installed collectors](https://help.sumologic.com/03Send-Data/Installed-Collectors). Installed collector will require you to allow outbound traffic to [Sumo Logic endpoints](https://help.sumologic.com/APIs/General-API-Information/Sumo-Logic-Endpoints-by-Deployment-and-Firewall-Security) for collection to work. For detailed requirements for Installed collectors, see this [page](https://help.sumologic.com/01Start-Here/03About-Sumo-Logic/System-Requirements/Installed-Collector-Requirements).


    Based on your infrastructure and networking setup choose one of these methods to collect MongoDB logs and follow the instructions below to set up log collection:



1. Configure logging in MongoDB
2. Configure local log file or syslog collection
3. Configure a Collector
4. Configure a Source

    Detail instructions for each are as follows:

1. Configure logging in MongoDB

    MongoDB supports logging via the following methods: syslog, local text log files and stdout. MongoDB logs have four levels of verbosity. To select a level, set loglevel to one of:

* 0 is the MongoDB's default log verbosity level, to include[ Informational](https://docs.mongodb.com/manual/reference/log-messages/#std-label-log-severity-levels) messages.
* 1 to 5 increases the verbosity level to include[ Debug](https://docs.mongodb.com/manual/reference/log-messages/#std-label-log-severity-levels) messages.

    All logging settings are located in [MongoDB.conf](https://docs.mongodb.com/manual/reference/method/db.setLogLevel/).

1. Configure MongoDB to log to a Local file or syslog

    **Configuring MongoDB logs to go to log files**


    By default, MongoDB logs are stored in **/var/log/mongodb/mongodb.log**. The default directory for log files is listed in the MongoDB.conf file.


    To configure the log output destination to a log file, use one of the following settings, either in the[ configuration file](https://docs.mongodb.com/manual/reference/configuration-options/) or on the command-line:


        **Configuration file:**

* The[ systemLog.destination](https://docs.mongodb.com/manual/reference/configuration-options/#mongodb-setting-systemLog.destination) option for _file_

        **Command-line:**

* **the[ --logpath](https://docs.mongodb.com/manual/reference/program/mongod/#std-option-mongod.--logpath) option for[ mongod](https://docs.mongodb.com/manual/reference/program/mongod/#mongodb-binary-bin.mongod) for _file_**
* **the[ --logpath](https://docs.mongodb.com/manual/reference/program/mongos/#std-option-mongos.--logpath) option for[ mongos](https://docs.mongodb.com/manual/reference/program/mongos/#mongodb-binary-bin.mongos) for _file_**

    Logs from the MongoDB log file can be collected via a Sumo Logic [Installed collector](https://help.sumologic.com/03Send-Data/Installed-Collectors) and a [Local File Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Local-File-Source) as explained in the next section.


    **Configuring MongoDB logs to stream via syslog**


    To configure the log output destination to syslog, use one of the following settings, either in the[ configuration file](https://docs.mongodb.com/manual/reference/configuration-options/) or on the command-line:


        **Configuration file:**

* The[ systemLog.destination](https://docs.mongodb.com/manual/reference/configuration-options/#mongodb-setting-systemLog.destination) option for _syslog_

        **Command-line:**

* the[ --syslog](https://docs.mongodb.com/manual/reference/program/mongod/#std-option-mongod.--syslog) option for[ mongod](https://docs.mongodb.com/manual/reference/program/mongod/#mongodb-binary-bin.mongod) for _syslog_
* the[ --syslog](https://docs.mongodb.com/manual/reference/program/mongos/#std-option-mongos.--syslog) option for[ mongos](https://docs.mongodb.com/manual/reference/program/mongos/#mongodb-binary-bin.mongos) for _syslog_

    To capture MongoDB logs using syslog, configure a [syslog source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Syslog-Source) on an [Installed collector](https://help.sumologic.com/03Send-Data/Installed-Collectors) as explained in the next section.

1. Configuring a Collector

    To add an Installed collector, perform the steps as defined on the page[ Configure an Installed Collector.](https://help.sumologic.com/03Send-Data/Installed-Collectors)

1. Configuring a Source

    **To add a Local File Source source for MongoDB do the following**


    To collect logs directly from your MongoDB machine, use an Installed Collector and a Local File Source.

1. Add a[ Local File Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Local-File-Source).
2. Configure the Local File Source fields as follows:
* **Name.** (Required)
* **Description.** (Optional)
* **File Path (Required).** Enter the path to your error.log or access.log. The files are typically located in /var/log/mongodb/mongodb.log. If you are using a customized path, check the MongoDB.conf file for this information.
* **Source Host.** Sumo Logic uses the hostname assigned by the OS unless you enter a different host name
* **Source Category.** Enter any string to tag the output collected from this Source, such as **MongoDB/Logs**. (The Source Category metadata field is a fundamental building block to organize and label Sources. For details see[ Best Practices](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category).)
* **Fields. Set the following fields:**
    * **component = database**
    * **db_system = mongodb**
    * **db_cluster = <Your_MongoDB_Cluster_Name>**
    * **environment = <Environment_Name>, such as Dev, QA or Prod.**


14


1. Configure the **Advanced** section:
* **Enable Timestamp Parsing.** Select Extract timestamp information from log file entries.
* **Time Zone.** Choose the option, **Ignore time zone from log file and instead use**, and then select your MongoDB Server’s time zone.
* **Timestamp Format.** The timestamp format is automatically detected.
* **Encoding. **Select** **UTF-8 (Default).
* **Enable Multiline Processing.** Detect messages spanning multiple lines
    * Infer Boundaries - Detect message boundaries automatically
1. Click **Save**.

    **To add a Syslog Source source for MongoDB do the following**

1. Add a  [Syslog source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Syslog-Source) in the installed collector configured in the previous step.
2. Configure the Syslog Source fields as follows:
* **Name.** (Required)
* **Description.** (Optional)
* **Protocol**: UDP
* **Port**: 514 (as entered while configuring logging in Step b.)
* **Source Category.** Enter any string to tag the output collected from this Source, such as **MongoDB/Logs**. (The Source Category metadata field is a fundamental building block to organize and label Sources. For details see[ Best Practices](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category).)
* **Fields. **Set the following fields:
    * component = database
    * db_system = MongoDB
    * db_cluster = <Your_MongoDB_Cluster_Name>
    * environment = <Environment_Name>, such as Dev, QA or Prod.


15


1. Configure the **Advanced** section:
    * **Enable Timestamp Parsing.** Select Extract timestamp information from log file entries.
    * **Time Zone.** Choose the option, **Ignore time zone from log file and instead use**, and then select your MongoDB Server’s time zone.
    * **Timestamp Format.** The timestamp format is automatically detected.
    * **Encoding. **Select** **UTF-8 (Default).
2. Click **Save**.

At this point, MongoDB logs should start flowing into Sumo Logic.



## MongoDB Alerts


Sumo Logic provides out of the box alerts available via [Sumo Logic monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors). These alerts are built based on logs and metrics datasets and have preset thresholds based on industry best practices and recommendations.


**INSERT TABLE**



<!-- You have some errors, warnings, or alerts. If you are using reckless mode, turn it off to see inline alerts.
* ERRORs: 0
* WARNINGs: 0
* ALERTS: 28 -->


# Install the MongoDB Monitors, App, and View the Dashboards



1. **Last updated \
**Jun 29, 2021, 10:32 AM by Nishant
2. **Page restriction \
**Public
    * [ Page notifications Off](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/MongoDB-App-Dashboards#)
    *  
    * [Save as PDF](https://help.sumologic.com/@api/deki/pages/2221/pdf/Install%2bthe%2bMongoDB%2bMonitors%252C%2bApp%252C%2band%2bView%2bthe%2bDashboards.pdf?stylesheet=default)
    *  
    * [ Share](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/MongoDB-App-Dashboards#)

    Table of contents


This page has instructions for installing Sumo Logic Monitors for MongoDB, the app, and descriptions of each of the app dashboards. These instructions assume you have already set up collection as described in the [Collect Logs and Metrics for MongoDB](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/Collect-Logs-for-MongoDB) App page.


### Install Monitors
1


Sumo Logic has provided pre-packaged alerts available through [Sumo Logic monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) to help you proactively determine if a MongoDB cluster is available and performing as expected. These monitors are based on metric and log data and include pre-set thresholds that reflect industry best practices and recommendations. For more information about individual alerts, see [MongoDB Alerts](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/MongoDB_Alerts).

To install these monitors, you must have the **Manage Monitors** role capability.

You can install monitors by importing a JSON file or using a Terraform script.


2
There are limits to how many alerts can be enabled. For more information, see [Monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors#Rules) for details.


#### Method 1: Install Monitors by importing a JSON file
3




1. Download the [JSON file](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/blob/main/monitor_packages/MongoDB/MongoDB.json) that describes the monitors.
2. Replace `$$mongodb_data_source` with a custom source filter. To configure alerts for a specific database cluster, use a filter like `db_system=mongodb` or `db_cluster=dev-mongodb`. To configure the alerts for all of your clusters, set `$$mongodb_data_source` to blank ("").
3. Go to **Manage Data > Alerts > Monitors**.
4. Click **Add**.
5. Click **Import. \
**
4

6. On the** Import Content popup**, enter "MongoDB" in the Name field, paste in the JSON into the the popup, and click **Import**.  \

5

7. The monitors are created in a "MongoDB" folder. The monitors are disabled by default. See the [Monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) topic for information about enabling monitors and configuring notifications or connections.


#### Method 2: Install Monitors using a Terraform script
6


**Step 1: Generate a Sumo Logic access key and ID**

Generate an access key and access ID for a user that has the **Manage Monitors** role capability. For instructions see  [Access Keys](https://help.sumologic.com/Manage/Security/Access-Keys#Create_an_access_key_on_Preferences_page).

**Step 2: Download and install Terraform**

Download [Terraform 0.13](https://www.terraform.io/downloads.html) or later, and install it.

**Step 3: Download the Sumo Logic Terraform package for MongoDB monitors**

The alerts package is available in the Sumo Logic github [repository](https://github.com/SumoLogic/terraform-sumologic-sumo-logic-monitor/tree/main/monitor_packages/MongoDB). You can either download it using the `git clone` command or as a zip file.

**Step 4: Alert Configuration **

After extracting the package , navigate to the  `terraform-sumologic-sumo-logic-monitor/monitor_packages/MongoDB/` directory.

Edit the `MongoDB.auto.tfvars` file and add the Sumo Logic Access Key and Access ID from Step 1 and your Sumo Logic deployment. If you're not sure of your deployment, see [Sumo Logic Endpoints and Firewall Security](https://help.sumologic.com/APIs/General-API-Information/Sumo-Logic-Endpoints-by-Deployment-and-Firewall-Security).


```
access_id   = "<SUMOLOGIC ACCESS ID>"
access_key  = "<SUMOLOGIC ACCESS KEY>"
environment = "<SUMOLOGIC DEPLOYMENT>"
```


The Terraform script installs the alerts without any scope filters, if you would like to restrict the alerts to specific clusters or environments, update the `mongodb_data_source` variable. For example:


<table>
  <tr>
   <td>To configure alerts for...
   </td>
   <td>Set mongodb_data_source to something like...
   </td>
  </tr>
  <tr>
   <td>A specific cluster
   </td>
   <td><code>db_cluster &#61; mongodb.prod.01</code>
   </td>
  </tr>
  <tr>
   <td>All clusters in an environment
   </td>
   <td><code>environment &#61; prod</code>
   </td>
  </tr>
  <tr>
   <td>Multiple clusters using a wildcard
   </td>
   <td><code>db_cluster &#61; mongodb-prod*</code>
   </td>
  </tr>
  <tr>
   <td>A specific cluster within a specific environment
   </td>
   <td><code>db_cluster &#61; mongodb-1 and environment &#61; prod</code>

This assumes you have configured and applied Fields as described in Step 1: Configure Fields of the <em>Sumo Logic of the Collect Logs and Metrics for MongoDB</em> topic.
   </td>
  </tr>
</table>


All monitors are disabled by default on installation. To enable all of the monitors, set the `monitors_disabled `parameter to false.

By default, the monitors will be located in a "MongoDB" folder on the **Monitors** page. To change the name of the folder, update the monitor folder name in the `folder` variable in the `MongoDB.auto.tfvars` file.

If you want the alerts to send email or connection notifications, follow the instructions in the next section.

**Step 5: Email and Connection Notification Configuration Examples**

Edit the `MongoDB_notifications.auto.tfvars` file to populate the `connection_notifications` and `email_notifications` sections. Examples are provided below.

**Pagerduty connection example**

In the variable definition below, replace `&lt;CONNECTION_ID>` with the connection ID of the Webhook connection. You can obtain the Webhook connection ID by calling the [Monitors API](https://api.sumologic.com/docs/#operation/listConnections).


7
For information about overriding the payload for different connection types, see [Set Up Webhook Connections](https://help.sumologic.com/Manage/Connections-and-Integrations/Webhook-Connections/Set_Up_Webhook_Connections).


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


**Email notifications example**


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



##### Step 6: Install Monitors

1. Navigate to the `terraform-sumologic-sumo-logic-monitor/monitor_packages/MongoDB/` directory and run `terraform init`. This will initialize Terraform and download the required components.
2. Run `terraform plan` to view the monitors that Terraform will create or modify.
3. Run `terraform apply`.


### Install the Sumo Logic App

Now that you have set up collection for MongoDB, install the Sumo Logic App for MongoDB to use the preconfigured searches and [dashboards](https://help.sumologic.com/07Sumo-Logic-Apps/12Databases/MongoDB/MongoDB-App-Dashboards#Dashboards) to analyze your data.

**To install the app:**

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.



1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.


10
Version selection is applicable only to a few apps currently. For more information, see the [Install the Apps from the Library.](https://help.sumologic.com/01Start-Here/Library/Apps-in-Sumo-Logic/Install-Apps-from-the-Library)



1. To install the app, complete the following fields.
    1. **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
    2. **Data Source.** Select either of these options for the data source. 
        * Choose **Source Category**, and select a source category from the list. 
        * Choose **Enter a Custom Data Filter**, and enter a custom source category beginning with an underscore. Example: (_sourceCategory=MyCategory). 
    3. **Advanced**. Select the **Location in Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
2. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or other folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.


### Dashboards
11



12
If no events have occurred within the time range of the Panel, the Panel will be empty.


#### MongoDB - Overview
13


The **MongoDB - Overview** dashboard provides an at-a-glance view of MongoDB health, performance and problems causing errors.

Use this dashboard to:



* Identify Slow Queries impacting the performance.
* Gain insights into Replication and Sharding operations.
* Verify Page Faults generated to determine the root cause of the problems.


14



#### MongoDB - Resource
15


The **MongoDB - Resource** dashboard shows resource utilization by the MongoDB component.

Use this dashboard to:



* Determine Memory and Disk Usage.
* Identify potential resource constraints and issues.


16



#### MongoDB - Errors and Warnings
17


The **MongoDB - Errors and Warnings** dashboard shows errors and warnings by the MongoDB component.

Use this dashboard to:



* Determine components producing multiple errors or warnings.


18



#### MongoDB - Logins and Connections
19


The **MongoDB - Logins and Connections** dashboard shows geo location of client connection requests, failed connection logins by geo location, and count of failed login attempts.

Use this dashboard to:



* Determine potential hacking attempts.
* Determine location of attacks.


20



#### MongoDB - Queries
21


MongoDB queries include the following definitions:



* **MongoDB queries** include the following database commands: find, insert, remove, delete or update.
* **Slow queries** are defined as queries that take more than 100 milliseconds.
* **keysExamined** are the number of index keys that MongoDB scanned in order to carry out the operation.


22
From MongoDB - If keysExamined is much higher than nreturned, the database is scanning many index keys to find the result documents. Consider creating or adjusting indexes to improve query performance.


23



#### MongoDB - Replication
24


The **MongoDB - Replication** dashboard shows replication events, errors, warnings, and nodes.

Use this dashboard to:



* Identify Replication errors and warnings.
* Gain insights into Arbiter, Primary and Secondary node health.


25
This Dashboard will only show data if you have Replication setup for MongoDB.


26



#### MongoDB - Sharding
27


The MongoDB - Sharding dashboard dashboard shows sharding related errors, events, failures and number of chunks moving between shards.

Use this dashboard to:



* Identify Sharding errors and warnings.
* Gain insights into Chunk operations.


28