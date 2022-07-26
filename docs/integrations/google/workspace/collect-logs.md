---
id: collect-logs
title: Collect Logs for Google Workspace
sidebar_label: Collect Logs
description: tk
---


This procedure explains how to collect logs from Google Workspace and ingest them into Sumo Logic.


## Log Types

**Google Workspace Apps** each have a log that records actions in JSON format. The logs are all structurally similar—most have an ID, actor, and an IP Address. The differences are in the events section of the JSON where the actions are recorded.

**Google Workspace Alert Center** alerts are in JSON format. Most of the alerts have a few common fields. The differences are in the data section of the JSON where the alert type specific details are recorded. For more information, see this Google Workspace[ Alert document](https://developers.google.com/admin-sdk/alertcenter/reference/alert-types).


## Configure Log Collection

You can configure two types of log collection:

* [Google Workspace](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/01Configure_Collection_for_Google_Workspace_Audit_Source): Monitors and analyzes the activity across all the Google Workspace Apps in one place. You can configure collection for each Google App for which you want to analyze events:
    * Google Admin
    * Google Drive
    * Google Login
    * Google Token
* [Google Workspace Alert Center](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center): Provides full visibility into alerts from Google Workspace apps, allowing you to investigate and correlate alerts and monitor potential threats. You can configure the list alerts to be collected. The alerts are forwarded to Sumo Logic’s HTTP endpoint in JSON format.


## Configure Collection for Google Workspace Audit Source

This section provides instructions for configuring log collection for Google Workspace with Audit Source. Click a link to jump to a topic:

* [About Source Configuration](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/01Configure_Collection_for_Google_Workspace_Audit_Source#About_Source_Configuration)
* [Google Authentication and Authorization](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/01Configure_Collection_for_Google_Workspace_Audit_Source#Google_Authentication_and_Authorization)
* [Configure a Collector](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/01Configure_Collection_for_Google_Workspace_Audit_Source#Configure_a_Collector)
* [Configure Google Workspace Apps Audit Sources](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/01Configure_Collection_for_Google_Workspace_Audit_Source#Configure_Google_Workspace_Apps_Audit_Sources)
* [Field Extraction Rules](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/01Configure_Collection_for_Google_Workspace_Audit_Source#Field_Extraction_Rules)
* [Sample Log Message](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/01Configure_Collection_for_Google_Workspace_Audit_Source#Sample_Log_Message)
* [Query Samples](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/01Configure_Collection_for_Google_Workspace_Audit_Source#Query_Samples)


### About Source Configuration  

Currently, the source name for Google Workspace is still **G Suite Apps Audit Source**, which will be changed/updated shortly.

Configure one [G Suite Apps Audit Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Google/G_Suite_Apps_Audit_Source) for each Google App from which you want to collect events:

* Google Admin
* Google Calendar
* Google Drive
* Google Login
* Google Token

Google Workspace Drive Audit events are only logged for files owned by users with Google Workspace Business, Enterprise, or Drive Enterprise licenses.

When you configure your Source Categories, you can configure and use them in two different ways.

**One Single Source Category for all Sources.** For users who are setting up the Google Workspace Apps Audit Source for the first time, we suggest that you use the same single Source Category for each Google Apps Audit Source. For example, **google_apps**.

**Different Source Categories for each Source.** You may configure a different Source Category for each Source, but we recommend that you use a naming convention for the Source Categories that allows you to apply a wildcard. For example, naming your Source Categories as follows would allow you to refer to all of them with the query **google_app***.
* google_app_admin
* google_app_calendar
* google_app_drive
* google_app_login
* Google_app_token

A Google Workspace Apps Audit Source uses the [Google Apps Reports API](https://developers.google.com/admin-sdk/reports/v1/get-start/getting-started) to ingest all audit logs via watchpoints. Activity from the following Google apps are supported in Sumo's Google Workspace App:
* Admin
* Calendar
* Drive
* Login
* Token

Only one Source should be configured per app. In other words, you might set up one Source to collect Calendar audit logs, another to collect Token audit logs, and so on.


### Google Authentication and Authorization  

This Source uses OAuth to integrate with the Google Apps Reports API. Therefore, your Google Apps credentials are never stored by Sumo Logic, and we have no visibility into the details of your Google Apps account.  Sumo Logic only stores OAuth tokens that are generated after authentication and authorization.

When creating or modifying a Google Apps Audit Source, you will be required to authenticate with Google using the credentials of a user that has access rights to the account, and to the Reports API. See Google's [Reports API: Prerequisites](https://developers.google.com/admin-sdk/reports/v1/guides/prerequisites) documentation for more details. During Google's OAuth consent flow, you will also be asked to grant the Sumo Logic app permission to use the Reports API.


Authentication must be with a new Google Workspace Apps Audit Source, we do not support re-authenticating existing sources.


### Configure a Collector

Configure a [Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors/Configure-a-Hosted-Collector) for Google Apps Audit.


### Configure Google Workspace Apps Audit Sources  
15


When you have set up a Hosted Collector and have your credentials ready, you're all set to configure the Sources. Perform the steps below for each Google Workspace App you want to monitor.  Before you configure the Sources, choose one of the source category strategies described in [About Source Configuration](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace#About_Source_Configuration), above.


16
We recommend that you use the same single Source Category for each Google Workspace App Audit Source. For example, **google_apps**.

**To configure a Google Workspace Apps Audit Source, do the following:**



1. Configure a **G Suite Apps Audit** Source.
2. Configure the Source fields:
    1. **Name**. (Required) A name is required.
    2. **Description**. Optional.
    3. **Application**. Select the app that you’d like this Source to collect data from.
    4. **Source Category**. (Required)   
    5. **Sign in with Google**. Click to give permission to Sumo Logic to set up watchpoints using the Google Workspace Apps Reports API. Click **Accept**.
3. Click **Save**.


#### Google Workspace App Audit Known Issues  
17


The Google API has a few known issues that cannot be changed by Sumo Logic.

**Google Workspace license requirement**. Google Workspace Drive Audit events are only logged for files owned by users with Google Workspace Business, Enterprise, or Drive Enterprise licenses.

**Authentication token limit**. Google limits an application (such as Sumo Logic) to 25 active authentication tokens per Google Workspace Apps account. According to Google’s documentation, the oldest token is invalidated if a 26th token is created. However, during testing, we found that once the 26th token is issued, all previous 25 tokens become invalid. In this situation, the only workaround is to delete and recreate all G Suite Apps Audit Sources in Sumo Logic.

**Duplicate records**. The following situations might result in the collection of duplicate log messages:



* **Complex events**. When a complex an event is logged that contains multiple sub-events, such as a new calendar entry, a JSON object is created to log the event. That object will have an array of event details for each included action (such as inviting guests). When this happens, duplicate event logs might be created for each sub-action. So, if there is one event with three sub actions, the exact same message event data might be duplicated three times, most likely due to a bug in the Google API.
* **Watchpoint expiration**. Google API watchpoints expire after about one week. Unfortunately, there does not appear to be a method for refreshing the expiration of a watchpoint. Sumo Logic must keep track of when each watchpoint expires, and in very close sequence, create a new watchpoint and kill the old watchpoint. This results in a slight overlap, typically only a few seconds, when there are two watchpoints for the same application. This might result in duplicate logs during that overlapping period, both of which are collected (which is preferable to the possibility of losing some data).

**Service Availability**. Logging is dependent on the availability of Google services. In some cases, apps may stop producing logs for a period of time. We have observed this during our development and QA testing.

To provide feedback on these limitations and known issues, contact Google support or your Google account contact.


### Field Extraction Rules

* **Name**. A relevant name, such as "Google"
* **Scope**. _sourceCategory=google*
* **Parse Expression**.  

```
| json "id","actor","events"  \
| json field=actor "email", "profileId" \
| json field=id "applicationName"
```


### Sample Log Message  

```json
{
   "kind": "admin#reports#activity",
   "id": {
      "time": "2017-02-10T19:14:24.519Z",
      "uniqueQualifier": "-123",
      "applicationName": "token",
      "customerId": "ABC123"
   },
   "etag": "\"xyz\"",
   "actor": {
      "email": "sumo@sumologic.com",
      "profileId": "123456789"
   },
   "events": [
      {
         "name": "authorize",
         "parameters": [
            {
               "name": "client_id",
               "value": "123.apps.googleusercontent.com"
            },
            {
               "name": "app_name",
               "value": "Dialpad"
            },
            {
               "name": "scope",
               "multiValue": [
                  "https://www.googleapis.com/sumo/userinfo.email",
                  "https://www.googleapis.com/sumo/userinfo.profile",
                  "https://www.google.com/sumo/feeds",
                  "https://www.googleapis.com/sumo/sumo.me"
               ]
            }
         ]
      }
   ]
}
```



### Query Samples


**Top 10 Apps by Count**


```
_source=google_* token
| json "id","actor", "events"
| json field=actor "email", "profileId"
| json field=id "applicationName"
| where applicationName="token"
| parse regex field=events "\[{\"name\":\"(?<token_action>.*?)\",\"parameters\"" nodrop
| parse regex field=events "{\"name\":\"app_name\",\"value\":\"(?<app_name>.*?)\"\}" nodrop
| count by app_name
| top 10 app_name by _count
```


**Logins from Multiple IPs**

```
_sourceCategory=google*
| json "actor","ipAddress"
| json "events"
| json field=actor "email", "profileId"
// Needed because a group by operator is required in dashboards
| count by email, ipAddress
| join (count by ipAddress, email) as t1, (count_distinct(ipAddress) by email) as t2 on t1.email=t2.email
| where t2__count_distinct >1
| t1_email as email
| t1_ipAddress as ipAddress
| count by email
| sort by _count desc, email asc
```



## Configure Collection for Google Workspace Alert Center

This section explains how to collect logs from Google Workspace Alert Center and ingest them into Sumo Logic for use with the Google Workspace App predefined dashboards and searches. Click a link to jump to a topic:

* [Alert types](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center#Alert_types)
* [Collection overview](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center#Collection_overview)
* [Add a Hosted Collector and HTTP Source](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center#Add_a_Hosted_Collector_and_HTTP_Source)
* [Configure collection for Google Workspace Alert Center](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center#Configure_collection_for_Google_Workspace_Alert_Center)
* [Sample Log Message](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center#Sample_Log_Message)
* [Query Sample](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center#Query_Sample)


### Alert types

All the alerts are in JSON format. Most of the alerts have few common fields like alertId, customerId, createTime, source, type and data. The differences are in the data section of the JSON where the alert type specific details are recorded. For more information about different alert types refer this Google Workspace[ Alert document](https://developers.google.com/admin-sdk/alertcenter/reference/alert-types).


### Collection overview
22


Sumo Logic provides a serverless solution which pulls logs from Google Workspace with API calls. You can configure the list alerts to be collected, but by default all alerts are collected. The alerts  are then forwarded to Sumo Logic’s HTTP endpoint in JSON format. By default the collection starts from the current date and time, but this setting is also configurable as detailed in the [Advanced configuration](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center#Advanced_configuration) section.


23



### Add a Hosted Collector and HTTP Source
24


This section demonstrates how to add a hosted Sumo Logic collector and HTTP Logs and Metrics source, to collect alerts for Google Workspace Alert Center.


#### Prerequisite
25


Before creating the HTTP source, identify the Sumo Logic Hosted Collector you want to use, or create a new Hosted Collector as described in the following task.


26
When you configure the HTTP Source, make sure to save the HTTP Source Address URL. You will need this to configure in configuration file.

**To add a hosted collector and HTTP source, do the following:**



1. Create a new Sumo Logic Hosted Collector by performing the steps in [Configure a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors/Configure-a-Hosted-Collector).
2. Add an  HTTP Logs and Metrics Source.
3. In the Advanced Options for Logs, under Timestamp Format, click **Specify a format** and enter the following information in the respective fields:
* **Format:**


```
yyyy-MM-dd'T'HH:mm:ss.SSS'Z'
```



**Timestamp locator:**


```
\"createTime\":(.*),
```



1. Click **Test** and paste in a test log line when prompted to do so.
2. If the test is successful, click **Save**.
3. Make a note of the URL of the new source.


### Configure collection for Google Workspace Alert Center  

In this section, we explore various mechanisms to collect findings from Google Workspace Alert Center and send them to Sumo Logic, where they are shown in dashboards as part of the Google Workspace App. You can configure a Google Workspace Alert Center collector in Google Cloud Platform (GCP), or via a script on a Linux machine. Choose the method that is best suited for you:



* [Google Cloud Platform (GCP) collection](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center/Configure_Google_Cloud_Platform_Collection_for_Google_Workspace_Alert_Center)
* [Script based collection](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center/Configure_Script-Based_Collection_for_Google_Workspace_Alert_Center)


### Sample Log Message

This section provides a sample Google Workspace Alert Center log message.


```json
{
 "alertId": "2b49ec18-2f92-4d58-acca-45994b740848",
 "createTime": "TIMESTAMP",
 "customerId": "03gm7p8e",
 "data": {
   "@type": "type.googleapis.com/google.apps.alertcenter.type.DomainWideTakeoutInitiated",
   "takeoutRequestId": "unique9gfd87ss",
   "email": "john@alertcenter1.bigr.name"
 },
 "deleteTime": "TIMESTAMP",
 "endTime": "TIMESTAMP",
 "metadata": {
   "alertId": "2b49ec18-2f92-4d58-acca-45994b740848",
   "customerId": "03gm7p8e",
   "status": "NOT_STARTED",
   "updateTime": "TIMESTAMP"
 },
 "source": "Domain wide takeout",
 "startTime": "TIMESTAMP",
 "type": "Customer takeout initiated"
}
```



### Query Sample
30


The query sample provided in this section is from the **Google Workspace Activity by Users with Compromised Credentials** panel of the **Google Workspace - Alert Center - Investigations Dashboard**.


```
_sourceCategory=googleworkspace_google_apps
| json "actor", "events", "id" nodrop
| json field=actor "email"
| json field=id "applicationName"
| where [subquery:_sourceCategory=googleworkspace_alerts "Leaked password"
 | json "alertId","customerId","source","type","data", "data.email" as alert_id, customer_id, source, type, data, email
 | where type="Leaked password"  
 | count by email
 | compose email  
]
| parse regex field=events "\"name\":\"(?<event_name>[^\"]+)\",\"type\":\"(?<event_type>[^\"]+)\"" multi
| formatDate(_messageTime,"MM-dd-yyyy HH:mm:ss:SSS") as event_time
| count by event_time, event_name, event_type, email, applicationName
| fields -_count
| sort by event_time
```



## Configure Google Cloud Platform Collection for Google Workspace Alert Center


### Google Cloud Platform (GCP) collection  
31


This section provides instructions for configuring Google Workspace Alert Center collection in your Google Cloud Platform environment. The Google Workspace Alert Center collector function fetches the findings from Google Workspace and sends them to Sumo Logic.

**To configure Google Workspace Alert Center collection in your GCP environment, do the following:**



1. Go to: **[https://console.cloud.google.com/cloudshell/](https://console.cloud.google.com/cloudshell/)**
2. Run the following command:


```
https://raw.githubusercontent.com/SumoLogic/sumologic-gsuitealertcenter/master/sumo_gsuite_alerts_collector_deploy.sh

```



1. Edit the **sumo_gsuite_alerts_collector_deploy.sh** bash script to configure following variables:
* **region: **The Region where the Google function will be deployed. For example: "us-central1"
* **project_id:** The project id of the project where the collector and all its resources will be deployed
* **delegated_email: **The valid email address of one of your org's Google Workspace super admin users.
* **Sumo_endpoint:  **The Sumo Logic HTTP endpoint created in Step 1
1. Run the following script:

```
sh sumo_gsuite_alerts_collector_deploy.sh

```


1.  In the command prompt, enter "**N**" for the following question "Allow unauthenticated invocations of new function"    as shown below.


32




1. Copy the **Client ID** displayed at the end of the script output. You will use the Client Name field when you [configure Google Workspace Alert Center to allow client API access](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center/Configure_Google_Cloud_Platform_Collection_for_Google_Workspace_Alert_Center#configure-google-workspace-alert-center-to-allow-client-api-acce) in the following task.
2. Go to the Cloud Datastore page of the project, with the Project ID you configured in the previous steps of this procedure, and create a database instance with the **Cloud Firestore in Datastore Mode** option. For more information, refer to the [Google Cloud Datastore documentation](https://cloud.google.com/datastore/docs/quickstart).


33



34
The script you used in this procedure configured the environment variables in the config file. You can update the variables by following the instructions Google's Using Environment Variables documentation, [Updating environment variables section](https://cloud.google.com/functions/docs/env-var).


### Configure Google Workspace Alert Center to allow client API access
35


This section explains how to configure Google Workspace Alert Center to allow API access.


36


Follow the step 2 and step 3 under “Set up the Alert Center API” [docs](https://developers.google.com/admin-sdk/alertcenter/guides/prerequisites) to enable alert center API and grant domain-wide access to the application.

If you are using the [Configure Google Cloud Platform Collection for Google Workspace Alert Center](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center/Configure_Google_Cloud_Platform_Collection_for_Google_Workspace_Alert_Center)

use the **Client ID** for the service account copied in Step 6 of the following [docs](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center/Configure_Google_Cloud_Platform_Collection_for_Google_Workspace_Alert_Center#google-cloud-platform-gcp-%C2%A0collection).

If you are using the [Configure Script-Based Collection for Google Workspace Alert Center](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center/Configure_Script-Based_Collection_for_Google_Workspace_Alert_Center) use the **Client ID **present in the JSON generated after adding the key in the service account.

**To configure Google Workspace Alert Center:**



1. Go to your G Suite domain's Admin console (see instructions on [signing in to your Admin console](https://support.google.com/a/answer/182076)), go to **Security -> Access and data control -> API Controls****.


37




1. In the newly opened window, click **Manage Domain-wide Delegation **at the bottom.


38




1. Click **Add new** button on the top.


39




1. Enter the **Client ID** for the service account copied in Step 2, then in the **OAuth Scopes** field enter the following: [https://www.googleapis.com/auth/apps.alerts ](https://www.googleapis.com/auth/apps.alerts)


40




1. Click **Authorise**.


### Adding new Alert types
41


In the future, if Google adds a new alert type do the following to add new alert types:



1. Go to the **gsuitealertcenterfunc **google cloud function console.
2. Click **Edit** at the top and then click **Next**.


42




1. In the editor, edit the **gsuitealertcenter.yaml** file and add the new alert types in ALERT_TYPES parameter from the “Alert type” column present in Google Workspace[ Alert types documentation](https://developers.google.com/admin-sdk/alertcenter/reference/alert-types).


43




1. Click **Deploy**.


### Advanced configuration  
44


This section provides a list of environment variables for Google Workspace Alert Center and their usage. For information on how to set these environment variables, refer to this [Google Cloud document](https://cloud.google.com/functions/docs/env-var).


<table>
  <tr>
   <td>Environment Variable
   </td>
   <td>Usage
   </td>
  </tr>
  <tr>
   <td>ALERT_TYPES</td>
   <td><p>"Customer takeout initiated"</p>
<p>"Misconfigured whitelist "</p>
<p>"User reported phishing"</p>
<p>"User reported spam spike"</p>
<p>"Suspicious message reported"</p>
<p>"Phishing reclassification"</p>
<p>"Malware reclassification"</p>
<p>"Leaked password"</p>
<p>"Suspicious login"</p>
<p>"Suspicious login (less secure app)"</p>
<p>"Suspicious programmatic login"</p>
<p>"User suspended"</p>
<p>"User suspended (spam)"</p>
<p>"User suspended (spam through relay)"</p>
<p>"User suspended (suspicious activity)"</p>
<p>"Google Operations"</p>
<p>"Government attack warning"</p>
<p>"Device compromised"</p>
<p>"Suspicious activity"</p></td>
  </tr>
  <tr>
   <td>BACKFILL_DAYS</td>
   <td>Number of days before the event collection will start. If the value is 1, then events are fetched from yesterday to today.</td>
  </tr>
  <tr>
   <td>PAGINATION_LIMIT</td>
   <td>Number of events to fetch in a single API call.</td>
  </tr>
  <tr>
   <td>LOG_FORMAT
   </td>
   <td>Log format used by the python logging module to write logs in a file.
   </td>
  </tr>
  <tr>
   <td>ENABLE_LOGFILE
   </td>
   <td>Set to TRUE to write all logs and errors to a log file.
   </td>
  </tr>
  <tr>
   <td>ENABLE_CONSOLE_LOG
   </td>
   <td>Enables printing logs in a console.
   </td>
  </tr>
  <tr>
   <td>LOG_FILEPATH
   </td>
   <td>Path of the log file used when ENABLE_LOGFILE is set to TRUE.
   </td>
  </tr>
  <tr>
   <td>NUM_WORKERS
   </td>
   <td>Number of threads to spawn for API calls.
   </td>
  </tr>
  <tr>
   <td>MAX_RETRY
   </td>
   <td>Number of retries to attempt in case of request failure.
   </td>
  </tr>
  <tr>
   <td>BACKOFF_FACTOR
   </td>
   <td>A backoff factor to apply between attempts after the second try. If the backoff_factor is 0.1, then sleep() will sleep for [0.0s, 0.2s, 0.4s, ...] between retries.
   </td>
  </tr>
  <tr>
   <td>TIMEOUT
   </td>
   <td>Request time out used by the requests library.
   </td>
  </tr>
  <tr>
   <td>SUMO_ENDPOINT
   </td>
   <td>HTTP source endpoint url created in Sumo Logic.
   </td>
  </tr>
</table>



### Troubleshooting the Google Cloud Platform Function  
45


This section shows you how to troubleshoot the function and resolve errors you may have encountered.

**To verify the function, do the following:**



1. Log in to your Google Cloud Platform account, navigate to the cloud function you created, and click **Testing**.
2. Click the **Test the function** button.


46




1. Click the **View Logs** button to view the function logs. If an environment variable was not set, you will see error messages similar to the following.


47




1. Set the missing environment variable to resolve the issue.

**To verify whether the cloud scheduler job is triggering the function:**



1. Enter **Cloud Scheduler** in the search bar and click.


48




1. Click **View** button under **Logs** column corresponding to the Cloud Scheduler job starting with sumogsuite as show below.


49




1. In the newly opened window, you should be able to see logs with no errors seen under **Severity**. If there is an error, you can see more details by clicking on the **Error** section under **Severity**.


50




1.


### Configure Script-Based Collection for Google Workspace Alert Center

This page provides instructions for deploying script based collection for Google Workspace  Alert Center. This script collects logs for the Sumo Logic Google Workspace Alert Center App.


51
The _sumologic-_gsuite_alertcenter_ script is compatible with python 3.7 and python 2.7. This has been tested on Ubuntu 18.04 LTS and Debian 4.9.130.


#### Prerequisites
52




* This task assumes you have successfully added a **Hosted Collector** and **HTTP source**, as described in [Configure Collection for Google Workspace Alert Center](https://help.sumologic.com/07Sumo-Logic-Apps/06Google/Google_Workspace/01Collect-Logs-for-Google-Workspace/02Configure_Collection_for_Google_Workspace_Alert_Center).
* The following tasks assume you are logged in to the user account with which you will install the collector. If you are not, use the following command to switch to that account: \
`sudo su &lt;user_name>`


### Configure the script on a Linux machine
53


This task shows you how to install the script on a Linux machine.


54
For python 3, use pip3 install **sumologic-gsuitealertcenter** (step 3) and **/usr/bin/python3 -m sumogsuitealertscollector.main** (step 6) in operating systems where default python is not python3.

**To deploy the script, do the following:**



1. Setup the Alert Center API as described in the following [Google documentation](https://developers.google.com/admin-sdk/alertcenter/guides/prerequisites). Assign the Cloud Datastore Owner role while creating a service account. Copy the Client ID present in the JSON generated after adding the key in the service account (it will be used further in step 3 while granting domain wide access).  \
 \

55



56
While creating the key in the service account, make a note of the location of the Service Account JSON file that has been downloaded to your computer. You will need this path later.



1. If **pip** is not already installed, follow the instructions in the [pip documentation](https://pip.pypa.io/en/stable/installing/) to download and install **pip**.
2. Log in to a Linux machine (compatible with either Python 3.7 or Python 2.7) and install the script using the following command.


57
For python 3, use **pip3 install** in the following command.

```bash
pip install sumologic-gsuitealertcenter

```



1. Create a configuration file named **gsuitealertcenter.yaml** in home directory by copying the following snippet.

```yaml
SumoLogic:
  SUMO_ENDPOINT: <SUMO LOGIC HTTP URL>

GsuiteAlertCenter:
  DELEGATED_EMAIL: "<email address of your org's Google Workspace super admin>"
  CREDENTIALS_FILEPATH: "<path to json Service Account JSON file>"

Collection:
  ENVIRONMENT: onprem
```



1. Add the **SUMO_ENDPOINT** and **CREDENTIALS_FILEPATH** (from step 1 above), and **DELEGATED_EMAIL** parameters, then save the file. For the** DELEGATED_EMAIL **parameter: if you do not have an email address of one of your super admins, you can use a service account email address instead if you [delegate domain-wide authority](https://developers.google.com/admin-sdk/directory/v1/guides/delegation) to that account.
2. Create a cron job for running the collector every 5 minutes by using **crontab -e** and adding the following line.


58
If you are using python3, for operating systems where the default python is not python3, use **/usr/bin/python3 -m sumogsuitealertscollector.main** in the following command.


```bash
*/5 * * * * /usr/bin/python -m sumogsuitealertscollector.main > /dev/null 2>&1
```



### Advanced configuration
59


This section provides a list of environment variables for Google Workspace Alert Center that you can define in the configuration file, as shown in this example. See the following table for explanations for each of the environment variables.


60


For information on how to set these environment variables, refer to this [Google Cloud document](https://cloud.google.com/functions/docs/env-var).


<table>
  <tr>
   <td>Environment Variable
   </td>
   <td>Usage
   </td>
  </tr>
  <tr>
   <td>ALERT_TYPES
   </td>
   <td>"Customer takeout initiated"
<p>"Misconfigured whitelist"</p>
<p>"User reported phishing"</p>
<p>"User reported spam spike"</p>
<p>"Suspicious message reported"</p>
<p>"Phishing reclassification"</p>
<p>"Malware reclassification"</p>
<p>"Leaked password"</p>
<p>"Suspicious login"</p>
<p>"Suspicious login (less secure app)"</p>
<p>"Suspicious programmatic login"</p>
<p>"User suspended"</p>
<p>"User suspended (spam)"</p>
<p>"User suspended (spam through relay)"</p>
<p>"User suspended (suspicious activity)"</p>
<p>"Google Operations"</p>
<p>"Government attack warning"</p>
<p>"Device compromised"</p>
<p>"Suspicious activity"</p>
   </td>
  </tr>
  <tr>
   <td>BACKFILL_DAYS
   </td>
   <td>Number of days before the event collection will start. If the value is 1, then events are fetched from yesterday to today.
   </td>
  </tr>
  <tr>
   <td>PAGINATION_LIMIT
   </td>
   <td>Number of events to fetch in a single API call.
   </td>
  </tr>
  <tr>
   <td>LOG_FORMAT
   </td>
   <td>Log format used by the python logging module to write logs in a file.
   </td>
  </tr>
  <tr>
   <td>ENABLE_LOGFILE
   </td>
   <td>Set to TRUE to write all logs and errors to a log file.
   </td>
  </tr>
  <tr>
   <td>ENABLE_CONSOLE_LOG
   </td>
   <td>Enables printing logs in a console.
   </td>
  </tr>
  <tr>
   <td>LOG_FILEPATH
   </td>
   <td>Path of the log file used when ENABLE_LOGFILE is set to TRUE.
   </td>
  </tr>
  <tr>
   <td>NUM_WORKERS
   </td>
   <td>Number of threads to spawn for API calls.
   </td>
  </tr>
  <tr>
   <td>MAX_RETRY
   </td>
   <td>Number of retries to attempt in case of request failure.
   </td>
  </tr>
  <tr>
   <td>BACKOFF_FACTOR
   </td>
   <td>A backoff factor to apply between attempts after the second try. If the backoff_factor is 0.1, then sleep() will sleep for [0.0s, 0.2s, 0.4s, ...] between retries.
   </td>
  </tr>
  <tr>
   <td>TIMEOUT
   </td>
   <td>Request time out used by the requests library.
   </td>
  </tr>
  <tr>
   <td>SUMO_ENDPOINT
   </td>
   <td>HTTP source endpoint url created in Sumo Logic.
   </td>
  </tr>
</table>



### Troubleshooting
61


This section shows you how to run the function manually and then verify that log messages are being sent from Alert Center.

**To run the function manually, do the following:**



1. Enter  one of the following commands:
  — For **python**, use this command:

```bash
python -m sumogsuitealertscollector.main
```

  — For **python3**, use this command:


```bash
python3 -m sumogsuitealertscollector.main
```



1. The script generates logs in **/tmp/sumoapiclient.log** by default. Check these logs to verify whether it’s getting triggered or not.
2. If you installed the collector as `root` user and then run it as a normal user, you will see an error message similar to the following because the config is not present in the home directory of the user running the collector. Switch to `root` the user and run the script again.


```
Traceback (most recent call last):
 File "/usr/local/lib/python2.7/dist-packages/sumogsuitealertscollector/main.py", line 190, in main
   ns = GSuiteAlertsCollector()
 File "/usr/local/lib/python2.7/dist-packages/sumogsuitealertscollector/main.py", line 29, in __init__
   self.config = Config().get_config(self.CONFIG_FILENAME, self.root_dir, cfgpath)
 File "/usr/local/lib/python2.7/dist-packages/sumogsuitealertscollector/common/config.py", line 22, in get_config
   self.validate_config(self.config)
 File "/usr/local/lib/python2.7/dist-packages/sumogsuitealertscollector/common/config.py", line 34, in validate_config
   raise Exception("Invalid config")
Exception: Invalid config
```



You can also avoid this error by running the script with config file path as first argument.



1. To verify if there are new messages generated by Alert Center, go to Google **Home > Security > Alert Center **and then do the following:



1. Check for an error message similar to the following. If you see this, verify that the **DELEGATED_EMAIL** in your configuration belongs to a valid Google Workspace super admin user (or a service account that's been [delegated domain-wide authority](https://developers.google.com/admin-sdk/directory/v1/guides/delegation)).


```
Traceback (most recent call last):
File "/usr/local/lib/python3.6/dist-packages/sumogsuitealertscollector/main.py", line 191, in main
ns.run()
File "/usr/local/lib/python3.6/dist-packages/sumogsuitealertscollector/main.py", line 162, in run
task_params = self.build_task_params()
File "/usr/local/lib/python3.6/dist-packages/sumogsuitealertscollector/main.py", line 152, in build_task_params
obj = self.set_new_end_epoch_time(alert_type, self.DEFAULT_START_TIME_EPOCH)
File "/usr/local/lib/python3.6/dist-packages/sumogsuitealertscollector/main.py", line 81, in set_new_end_epoch_time
response = self.alertcli.alerts().list(**params).execute()
File "/usr/local/lib/python3.6/dist-packages/googleapiclient/_helpers.py", line 130, in positional_wrapper
return wrapped(*args, **kwargs)
File "/usr/local/lib/python3.6/dist-packages/googleapiclient/http.py", line 851, in execute
raise HttpError(resp, content, uri=self.uri)
googleapiclient.errors.HttpError: <HttpError 400 when requesting
https://alertcenter.googleapis.com/v1beta1/alerts?pageSize=1&filter=create_time+%3E%3D+%222019-04-17T16%3A29%3A14.061731Z%22+AND+create_time+%3C%3D+%222019-04-18T16%3A27%3A14.417915Z%22+AND+type+%3D+%22Customer+takeout+initiated%22&orderBy=create_time+desc&alt=json
returned "Request contains an invalid argument.">

```



1. If you installed the collector as `root` user and then run it as a normal user, you will see an error message similar to the following because the config is not present in the home directory of user running the collector. Switch to `root` user and run the script again.


```
Traceback (most recent call last):
 File "/usr/local/lib/python2.7/dist-packages/sumogsuitealertscollector/main.py", line 190, in main
   ns = GSuiteAlertsCollector()
 File "/usr/local/lib/python2.7/dist-packages/sumogsuitealertscollector/main.py", line 29, in __init__
   self.config = Config().get_config(self.CONFIG_FILENAME, self.root_dir, cfgpath)
 File "/usr/local/lib/python2.7/dist-packages/sumogsuitealertscollector/common/config.py", line 22, in get_config
   self.validate_config(self.config)
 File "/usr/local/lib/python2.7/dist-packages/sumogsuitealertscollector/common/config.py", line 34, in validate_config
   raise Exception("Invalid config")
Exception: Invalid config
```



64
You can also avoid this error by running the script with config file path as first argument.