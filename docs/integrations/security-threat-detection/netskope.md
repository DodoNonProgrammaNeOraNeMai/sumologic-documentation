---
id: netskope
title: Netskope
sidebar_label: Netskope
description: The Netskope App created by Sumo Logic provides visibility into security posture for your applications, as well as allowing you to determine the overall usage of software and SaaS applications in your environment. Netskope is a Cloud Access Security Broker (CASB) hosted in the cloud, primarily used to enforce security policies for cloud-based resources.
---

import useBaseUrl from '@docusaurus/useBaseUrl';

<img src={useBaseUrl('img/integrations/security-threat-detection/netskope.png')} alt="thumbnail icon" width="75"/>

The Netskope App provides visibility into the security posture of your applications and helps you determine the overall usage of software and SaaS applications.

Netskope is a Cloud Access Security Broker (CASB) hosted in the cloud. The Netskope product is primarily used for enforcing security policies for cloud-based resources, such as Box and Microsoft Office 365. Customers purchase a CASB to address cloud service risks, enforce security policies, and comply with regulations, even when cloud services are beyond their perimeter and out of their direct control.


#### Log Types
1


The Netskope App provides a collector source for pulling all the events and alerts from Netskope in real-time via API calls and ingests them into the Sumo Logic platform through our Hosted collector.

For more information on Netskope, refer to the Netskope [documentation](https://www.netskope.com/platform/how-it-works).


## Collect Logs for Netskope


To collect logs from the Netskope platform, if you are not using the Sumo Logic FedRamp deployment, use the [new Cloud to Cloud Integration for Netskope](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-to-Cloud_Integration_Framework/Netskope_Source) to create the source and use the same source category while installing the app.


2
The sections below are deprecated for non-FedRamp Sumo Logic deployments. If you are using the Sumo Logic FedRamp deployment, use the sections below to configure the collection for this app.


### Collection Overview (DEPRECATED)
3


Sumo Logic provides a collector agent that pulls logs from Netskope with API calls. You can configure the list of events and alerts to be collected, but by default all events and alerts are collected.

The events and alerts are forwarded to the Sumo Logic HTTP endpoint in JSON format. The configuration from a yaml file, which is stored in the home directory, is used. By default the collection starts from last 7 days, but this setting is configurable.

The Netskope App has the following components:



* **Application Usage:** Insights into application usage; specifically by devices, users, users and traffic patterns.
* **Security Alerts:** Visibility into Netskope security alerts and violations and the ability to identify effects of a breach.  


### Step 1: Adding a Hosted Collector and HTTP Source (DEPRECATED)
4


This section demonstrates how to add a hosted Sumo Logic collector and HTTP Logs and Metrics source, to collect events for Netskope


##### Prerequisite
5


Before creating the HTTP source, identify the Sumo Logic Hosted Collector you want to use, or create a new Hosted Collector as described in the following task.


6
When you configure the HTTP Source, make sure to save the HTTP Source Address URL. You will need this to configure in configuration file.

**To add a hosted collector and HTTP source, do the following:**



1. To create a new Sumo Logic Hosted Collector, perform the steps in [Configure a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors/Configure-a-Hosted-Collector).
2. Add an [ HTTP Logs and Metrics Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/HTTP-Source).
3. In **Advanced Options for Logs**, under Timestamp Format, click **Specify a format** and enter the following:
* Specify **Format** as `epoch`
* Specify **Timestamp locator** as `\"timestamp\": (.*),`


7




1. Click **Add**.


### Step 2: Getting a token from the Netskope Portal (DEPRECATED)
8


Netskope REST APIs use an auth token to make authorized calls to the API. This section demonstrates how to obtain a token from the Netskope user interface (UI).

**To obtain a Netskope auth token, do the following:**

1. Login to Netskope as the Tenant Admin.
2. Go to the API portion of the Netskope, **Settings > Tools > Rest API**.
3. Copy the existing token to your clipboard, or you can generate a new token and copy that token.


### Configuring a Sumo Logic Netskope collector (DEPRECATED)
9


This section provides walkthrough instructions that demonstrate how to configure a Sumo Logic Netskope collector.


10
The sumologic netskope collector is compatible with Python 3.7 and Python 2.7. It has been tested on Ubuntu 18.04 LTS and Debian 4.9.130.

**To create a Sumo Logic Netskope collector, do the following:**



1. Login to a Linux machine.
2. Install the collector using the following command.


11


If pip (python package installer) is not installed on your system, see the pip [docs](https://pip.pypa.io/en/stable/installing/) for instructions on how to download and install pip.
```
pip install sumologic-netskope-collector
```



1. Download the [netskope.yaml](https://s3.amazonaws.com/appdevstore/netskope.yaml) configuration file and place in the home directory.  
2. Edit the netskope.yaml file in the following way:
    1. Replace &lt;SUMO HTTP SOURCE ENDPOINT> with the Sumo Logic HTTPS Source endpoint you created in [Step 1](https://help.sumologic.com/07Sumo-Logic-Apps/22Security_and_Threat_Detection/Netskope/Collect_logs_for_Netskope#Step_1:_Add_a_Hosted_Collector_and_HTTP_Source).
    2. Replace &lt;Netskope API Token> with the Netskope API token you created in [Step 2](https://help.sumologic.com/07Sumo-Logic-Apps/22Security_and_Threat_Detection/Netskope/Collect_logs_for_Netskope#Step_2:_Getting_a_token_from_the_Netskope_Portal).
    3. Replace &lt;Netskope Domain> with your Netskope domain name. \
 \
Example of an edited netskope.yaml file:


```yml
Netskope:
 TOKEN: "ExampleTokenGxrtwdshciB7gHR7efDQbZPW"
 NETSKOPE_EVENT_ENDPOINT: https://example.goskope.com/api/v1/events
 NETSKOPE_ALERT_ENDPOINT: https://example.goskope.com/api/v1/alerts

SumoLogic:
 SUMO_ENDPOINT: "https://collectors.sumologic.com/receiver/v1/http/ZaVnC4dhaxxExampleEndpointxxx==="

```


1. Create a cron job to run the collector every 5 minutes, (use the crontab -e option) and add the following line:

```
*/5 * * * *  /usr/bin/python -m \
sumonetskopecollector.netskope > /dev/null 2>&1
```



### Updating the Sumo Logic Netskope collector (DEPRECATED)
12


Sumo Logic periodically makes changes to the collector. To make sure your collector is up-to-date, perform the following tasks on each machine running the collector:
* Check the latest version of the Netskope collector on this page: [https://pypi.org/project/sumologic-netskope-collector/#history](https://pypi.org/project/sumologic-netskope-collector/#history)
* Check the version of the collector you have installed by running the following command: `pip show sumologic-netskope-collector`
* If you are not running the latest collector, do the following:
1. Disable your cron job.
2. Stop all existing collector processes.
3. Run the following command to upgrade your collector: `pip install sumologic-netskope-collector --upgrade`
4. Enable the cron job.


### Advanced Configuration (DEPRECATED)
13


The following table explains the configuration file parameters and their usage.


<table>
  <tr>
   <td>Parameter</td>
   <td>Usage</td>
  </tr>
  <tr>
   <td>EVENT_TYPES</td>
   <td>List of events to fetch from Netskope:
<ul>
<li>page</li>
<li>application</li>
<li>audit</li>
<li>infrastructure</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>ALERT_TYPES</td>
   <td>List of alerts to fetch from Netskope:
<ul>
<li>Malware</li>
<li>Malsite</li>
<li>Compromised Credential</li>
<li>Anomaly</li>
<li>DLP</li>
<li>Watchlist</li>
<li>Quarantine</li>
<li>Policy</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>BACKFILL_DAYS
   </td>
   <td>Number of days <em>before</em> the event collection will start. If the value is 1, then events are fetched from yesterday to today.
   </td>
  </tr>
  <tr>
   <td>PAGINATION_LIMIT</td>
   <td>Number of events to fetch in a single API call
   </td>
  </tr>
  <tr>
   <td>LOG_FORMAT</td>
   <td>Log format used by the python logging module to write logs in a file
   </td>
  </tr>
  <tr>
   <td>ENABLE_LOGFILE</td>
   <td>Set to TRUE to write all logs and errors to a log file
   </td>
  </tr>
  <tr>
   <td>ENABLE_CONSOLE_LOG</td>
   <td>Enables printing logs in a console
   </td>
  </tr>
  <tr>
   <td>LOG_FILEPATH</td>
   <td>Path of the log file used when ENABLE_LOGFILE is set to TRUE
   </td>
  </tr>
  <tr>
   <td>NUM_WORKERS</td>
   <td>Number of threads to spawn for API calls</td>
  </tr>
  <tr>
   <td>MAX_RETRY</td>
   <td>Number of retries to attempt in case of request failure
   </td>
  </tr>
  <tr>
   <td>BACKOFF_FACTOR</td>
   <td>A backoff factor to apply between attempts after the second try. If the backoff_factor is 0.1, then sleep() will sleep for [0.0s, 0.2s, 0.4s, ...] between retries.
   </td>
  </tr>
  <tr>
   <td>TIMEOUT</td>
   <td>Request time out used by the requests library</td>
  </tr>
  <tr>
   <td>TOKEN</td>
   <td>API Token used for API calls authentication</td>
  </tr>
  <tr>
   <td>SUMO_ENDPOINT</td>
   <td>HTTP source endpoint url created in Sumo Logic
   </td>
  </tr>
</table>



### Sample log message
14



```json
{
           "dstip": "74.125.239.150",
           "dst_location": "Mountain View",
           "app": "Google Gmail",
           "_insertion_epoch_timestamp": 1547391690,
           "site": "Google Gmail",
           "src_location": "Pomerol",
           "organization_unit": "",
           "object_type": "Mail",
           "id": 3764,
           "app_session_id": 4252577042,
           "category": "Webmail",
           "dst_region": "California",
           "userkey": "Tanja.Barton@kkrlogistics.com",
           "dst_country": "US",
           "src_zipcode": "33500",
           "ur_normalized": "tanja.barton@kkrlogistics.com",
           "type": "nspolicy",
           "object": "Welcome Novak Dimitrov",
           "srcip": "77.194.46.1",
           "dst_latitude": 37.405991,
           "timestamp": 1547400222,
           "src_region": "Gironde",
           "dst_longitude": -122.078514,
           "alert": "no",
           "to_user": "ns-india@microsoft.com, hrglobal@microsoft.com",
           "user": "Tanja.Barton@kkrlogistics.com",
           "from_user": "bloomberg@bloomberg.com",
           "device": "Windows PC",
           "org": "kkrlogistics.com",
           "src_country": "FR",
           "traffic_type": "CloudApp",
           "dst_zipcode": "N/A",
           "count": 2,
           "src_latitude": 44.9333,
           "url": "https://mail.google.com/",
           "page_id": 2641483218,
           "sv": "unknown",
           "ccl": "excellent",
           "cci": 92,
           "activity": "Send",
           "userip": "127.0.0.1",
           "src_longitude": -0.2,
           "_id": "5df996d5b66a9ea963e812ce",
           "os": "Windows 8",
           "browser": "Internet Explorer",
           "appcategory": "Webmail"
       }
   ]
}
```


### Sample Query  
15

The following query sample was is from the Total Sessions panel of the Application Overview Dashboard.

```sql
_sourceCategory="netskope_events" "no" "nspolicy"
| json "_id", "alert", "type", "srcip", "dstip", "appcategory", "app", "os", "user", "device",
"acked", "site", "timestamp", "ccl", "activity", "browser", "object", "object_type", "from_user",
"to_user", "app_session_id" as alert_id, is_alert, type, src_ip, dest_ip, appcategory, app, os,
user, device, acked, site, timestamp, ccl, activity, browser, object, object_type, from_user,
to_user, app_session_id  nodrop
| where is_alert="no" and type="nspolicy"
| count by app_session_id
| count
```



## Install the Netskope App and view the Dashboards


This page demonstrates how to install the Netskope App, and provides examples and descriptions for each of the dashboards. The Netskope App has the following components:

* **Application Usage:** Insights into application usage; specifically by devices, users, users and traffic patterns.
* **Security Alerts:** Visibility into Netskope security alerts and violations and the ability to identify effects of a breach.  


### Install the App
16


This section provides instructions for installing the Netskope App.

**To install the Netskope App, do the following:**

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.

1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.

17
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


### Dashboard filters
18


**Each dashboard has a set of filters** that you can apply to the entire dashboard, as shown in the following example. Click the funnel icon in the top dashboard menu bar to display a scrollable list of filters that are applied across the entire dashboard.


19
You can use filters to drill down and examine the data on a granular level.


20


**Each panel has a set of filters** that are applied to the results for that panel only, as shown in the following example. Click the funnel icon in the top panel menu bar to display a list of panel-specific filters.


21



## Dashboard Categories
22


The Netskope dashboards are grouped by their component in the following two category folders:



* Application Usage
* Security Alerts


### Netskope - Application Overview Dashboard
23


**Netskope - Application Overview Dashboard** provides a high-level view of user activity, user geographic location by source IP, total sessions, applications used, distribution and activity of applications, and application trends over time.

Use this dashboard to:



* Monitor number of users, sessions, and sites using the applications, and find out the popular apps by user and app category.
* Track spikes in application usage over time.


24



### Netskope - Application Users Dashboard
25


**Netskope - Application Users Dashboard** provides a high-level view of application events, total sessions, user activity and geographic location by source IP and destination IP. This dashboard also shows visual breakdowns of distributions by operating system, browser, device, and user activity.

Use this dashboard to:



* Monitor recent user activities, track user locations, and find out the top users affected by alerts.
* Determine user classifications by browsers, devices, operating system (OS).


26



### Netskope - Application Details Dashboard
27


**Netskope - Application Details Dashboard** provides a high-level view of data for unique applications used, as well as top applications by alerts, bytes, and average page duration. This dashboard also provides a visual breakdown of applications by category, devices by user access, and network usage over time.

Use this dashboard to:



* Monitor the top applications generating alerts.
* Find out detailed information about application usage in terms of  page duration, user counts, upload and download bytes.




28



### Netskope - Alert Overview Dashboard
29


**Netskope - Alert Overview Dashboard** provides a high-level view of your alert data by type, geographic location of source IPs, total and top alerts, alerts by user, recent alerts, and alert trends over time.

Use this dashboard to:



* Track users affected by alerts.
* Monitor abnormal spikes, alert locations, and recent alerts.


30



### Netskope - Alert Details Dashboard
31


**Netskope - Alert Details Dashboard** provides a visual presentation of alert analytics, including the geographic locations of suspicious source and destination IPs, a time compare of alters, alert outlier trends over time, alerts by application, and recent alerts with a poor cloud confidence level.

Use this dashboard to:



* Compare alerts over time and anomalies in alert rates.
* Track which applications are producing the most alerts over time.


32



### Netskope - Data Loss Prevention Dashboard
33


**Netskope - Data Loss Prevention Dashboard** provides a high-level view of data loss prevention (DLP) analytics, including incidents by policy over time, incidents by severity and application, incidents by operating system (OS) and browser. This dashboard also shows data on DLP rules, top profiles, incident count, and users affected.

 Use this dashboard to:



* Track users and applications affected by DLP incidents.
* Monitor High Severity DLP incidents.
* Determine objects with critical severity.


34



### Netskope - Compromised Credentials Dashboard
35


**Netskope - Compromised Credentials Dashboard** provides easily accessible analytics on compromised credentials, including the number of users with compromised credentials, a breach count and top breaches, and source info. This dashboard also provides data on recent compromised credentials, apps used by users after a credentials breach, and user activities after a credentials breach.

Use this dashboard to:



* Track credential breaches along with their source.
* Monitor user activities.
* Monitor application usage after credentials have been breached.


36



### Netskope - Malware Dashboard
37


**Netskope - Malware Dashboard** provides a high-level view of total malwares detected, total apps and users affected, total files infected, top source IPs and malware types, and the top users affected. This dashboard also provides data malware incidents by app and severity, affected file types, apps used on infected machines, and the user activity on infected machines.

Use this dashboard to:



* Determine applications and users affected by malware.
* Monitor user activity on affected machines.


38



### Netskope - Anomalies Dashboard
39


**Netskope - Anomalies Dashboard** provides an at-a-glance view of anomalies on your environment, including the number of anomalies, users affected, anomalies over time, anomalies by app, alert name, and risk level. It also includes data on top users by anomaly risk level and recent anomalies by high risk level.

Use this dashboard to:



* Monitor anomalies in users activities.
* Track anomalies with high risk levels.


40