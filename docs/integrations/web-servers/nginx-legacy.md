---
id: nginx-legacy
title: Sumo Logic App for Nginx (Legacy)
sidebar_label: Nginx (Legacy)
description: Nginx is a web server that can be used as a reverse proxy, load balancer, mail proxy, and HTTP cache. The Sumo Logic App for Nginx (Legacy) helps you monitor webserver activity in Nginx.
---

import useBaseUrl from '@docusaurus/useBaseUrl';

<img src={useBaseUrl('img/integrations/web-servers/nginx-ingress.png')} alt="Web servers icon" width="75"/>

Nginx (Legacy) is a web server that can be used as a reverse proxy, load balancer, mail proxy, and HTTP cache.

The Sumo Logic App for Nginx (Legacy) support logs for Open Source Nginx, Nginx Plus, as well as Metrics for Open Source Nginx.

The Sumo Logic App for Nginx (Legacy) helps you monitor webserver activity in Nginx. The preconfigured dashboards provide information about site visitors, including the location of visitors, devices/operating systems, and browsers used; and information about server activity, including bots observed and error information.

#### Log and Metrics Types

The Sumo Logic App for Nginx assumes the NCSA extended/combined log file format for Access logs and the default Nginx error log file format for error logs.

All Dashboards (except the Error logs Analysis dashboard) assume the Access log format. The Error logs Analysis Dashboard assumes both Access and Error log formats, so as to correlate information between the two.

For more details on Nginx logs, see https://nginx.org/en/docs/http/ngx_http_log_module.html.

The Sumo Logic App for Nginx assumes Prometheus format Metrics for Requests and Connections. For Nginx Server metrics, Stub_Status Module from Nginx Configuration is used.

For more details on Nginx Metrics, see https://nginx.org/libxslt/en/docs/http/ngx_http_stub_status_module.html.


## Collect Logs and Metrics for Nginx (Legacy)

This page provides instructions for configuring log and metric collection for the Sumo Logic App for Nginx (Legacy).


### Collection Process Overview

Configuring log and metric collection for the Nginx (Legacy) App includes the following tasks:
* Collect Logs for Nginx
    * Non-Kubernetes


#### Collect Logs for Nginx

Nginx (Legacy) app supports the default access logs and error logs format.


##### Non-Kubernetes

This section provides instructions for configuring log collection for the Sumo Logic App for Nginx. Follow the below instructions to set up the Log collection.

1. Configure logging in Nginx
2. Configure a Collector
3. Configure a Source


#### 1. Configure logging in Nginx

Before you can configure Sumo Logic to ingest logs, you must configure the logging of errors and processed requests in NGINX Open Source and NGINX Plus. For instructions, refer to the following documentation:

[https://www.nginx.com/resources/admin-guide/logging-and-monitoring/](https://www.nginx.com/resources/admin-guide/logging-and-monitoring/)


###### 2. Configure a Collector

Use one of the following Sumo Logic Collector options:

1. To collect logs directly from the Nginx machine, configure an [Installed Collector](https://help.sumologic.com/03Send-Data/Installed-Collectors).
2. If you are using a service like Fluentd, or you would like to upload your logs manually, [Create a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors#Create_a_Hosted_Collector).


###### 3. Configure a Source

**For an Installed Collector**

To collect logs directly from your Nginx machine, use an Installed Collector and a Local File Source.

1. Add a [Local File Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Local-File-Source).
2. Configure the Local File Source fields as follows:
    * **Name.** (Required)
    * **Description.** (Optional)
    * **File Path (Required).** Enter the path to your error.log or access.log. The files are typically located in /var/log/nginx/error.log. If you are using a customized path, check the nginx.conf file for this information. If you are using Passenger, you may have instructed Passenger to log to a specific log using the passenger_log_file option.
    * **Source Host.** Sumo Logic uses the hostname assigned by the OS unless you enter a different hostname.
    * **Source Category.** Enter any string to tag the output collected from this Source, such as **Nginx/Access** or **Nginx/Error**. (The Source Category metadata field is a fundamental building block to organize and label Sources. For details see [Best Practices](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category).)
3. Configure the **Advanced** section:
    * **Enable Timestamp Parsing.** Select Extract timestamp information from log file entries.
    * **Time Zone.** Automatically detect.
    * **Timestamp Format.** The timestamp format is automatically detected.
    * **Encoding.** Select UTF-8 (Default).
    * **Enable Multiline Processing.**
        * **Error logs.** Select **Detect messages spanning multiple lines** and **Infer Boundaries - Detect message boundaries automatically**.
        * **Access** logs. These are single-line logs, uncheck **Detect messages spanning multiple lines**.
4. Click **Save**.

**For a Hosted Collector**

If you are using a service like Fluentd, or you would like to upload your logs manually, use a Hosted Collector and an HTTP Source.

1. Add an [HTTP Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/HTTP-Source).
2. Configure the HTTP Source fields as follows:
    * **Name.** (Required)
    * **Description.** (Optional)
    * **Source Host.** Sumo Logic uses the hostname assigned by the OS unless you enter a different hostname.
    * **Source Category.** Enter any string to tag the output collected from this Source, such as **Nginx/Access** or **Nginx/Error**. (The Source Category metadata field is a fundamental building block to organize and label Sources. For details see [Best Practices](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category).)
3. Configure the **Advanced** section:
    * **Enable Timestamp Parsing.** Select **Extract timestamp information from log file entries**.
    * **Time Zone.** For Access logs, use the time zone from the log file. For Error logs, make sure to select the correct time zone.
    * **Timestamp Format.** The timestamp format is automatically detected.
    * **Enable Multiline Processing.**
        * **Error** **logs**: Select **Detect messages spanning multiple lines** and **Infer Boundaries - Detect message boundaries automatically**.
        * **Access** **logs**: These are single-line logs, uncheck **Detect messages spanning multiple lines**.
4. Click **Save**.
5. When the URL associated with the HTTP Source is displayed, copy the URL so you can add it to the service you are using, such as Fluentd.


#### Sample Log Messages

**Access Log Example**

```
50.1.1.1 - example [23/Sep/2016:19:00:00 +0000] "POST /api/is_individual HTTP/1.1" 200 58 "-"
"python-requests/2.7.0 CPython/2.7.6 Linux/3.13.0-36-generic"
```


**Error Log Example**

```
2016/09/23 19:00:00 [error] 1600#1600: *61413 open() "/srv/core/client/dist/client/favicon.ico"
failed (2: No such file or directory), client: 101.1.1.1, server: _, request: "GET /favicon.ico
HTTP/1.1", host: "example.com", referrer: "https://abc.example.com/"
```

#### Create Field Extraction Rules

Field Extraction Rules (FERs) tell Sumo Logic which fields to parse out automatically. For instructions, see [Create a Field Extraction Rule](https://help.sumologic.com/Manage/Field-Extractions/Create-a-Field-Extraction-Rule).

Nginx assumes the NCSA extended/combined log file format for Access logs and the default Nginx error log file format for error logs.

Both the parse expressions can be used for logs collected from Nginx Server running on Local or container-based systems.

**FER for Access Logs**

Use the following Parse Expression:

```
| json field=_raw "log" as nginx_log_message nodrop
| if (isEmpty(nginx_log_message), _raw, nginx_log_message) as nginx_log_message
| parse regex field=nginx_log_message "(?<Client_Ip>\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})"
| parse regex field=nginx_log_message "(?<Method>[A-Z]+)\s(?<URL>\S+)\sHTTP/[\d\.]+\
"\s(?<Status_Code>\d+)\s(?<Size>[\d-]+)\s\"(?<Referrer>.*?)\"\s\"(?<User_Agent>.+?)\".*"
```


**FER for Error Logs**

Use the following Parse Expression:

```
| json field=_raw "log" as nginx_log_message nodrop
| if (isEmpty(nginx_log_message), _raw, nginx_log_message) as nginx_log_message
| parse regex field=nginx_log_message "\s\[(?<Log_Level>\S+)\]\s\d+#\d+:\s(?:\*\d+\s|)(?<Message>[A-Za-z][^,]+)(?:,|$)"
| parse field=nginx_log_message "client: *, server: *, request: \"* * HTTP/1.1\", host:
\"*\"" as Client_Ip, Server, Method, URL, Host nodrop
```

#### Sample Queries

This sample Query is from the **Requests by Clients** panel of the **Nginx (Legacy) - Overview** dashboard.


```
_sourceCategory = Labs/Nginx/Logs
| json field=_raw "log" as nginx_log_message nodrop
| if (isEmpty(nginx_log_message), _raw, nginx_log_message) as nginx_log_message
| parse regex field=nginx_log_message "(?<Client_Ip>\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})"
| parse regex field=nginx_log_message "(?<Method>[A-Z]+)\s(?<URL>\S+)\sHTTP/[\d\.]+\"\s(?<Status_Code>\d+)\s(?<Size>[\d-]+)\s\"(?<Referrer>.*?)\"\s\"(?<User_Agent>.+?)\".*"
| where _sourceHost matches "{{Server}}" and Client_Ip matches "{{Client_Ip}}" and Method matches "{{Method}}" and URL matches "{{URL}}" and Status_Code matches "{{Status_Code}}"
| count as count by Client_Ip
| sort count
```


## Installing the Nginx (Legacy) App

This section has instructions for installing the Sumo App for Nginx (Legacy). These instructions assume you have already set up the collection as described above.

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.

1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.

Version selection is applicable only to a few apps currently. For more information, see the [Install the Apps from the Library.](https://help.sumologic.com/01Start-Here/Library/Apps-in-Sumo-Logic/Install-Apps-from-the-Library)

1. To install the app, complete the following fields.
    1. **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
    2. **Data Source.** Select either of these options for the data source. 
        * Choose **Source Category**, and select a source category from the list. 
        * Choose **Enter a Custom Data Filter**, and enter a custom source category beginning with an underscore. Example: (`_sourceCategory=MyCategory`). 
    3. **Advanced**. Select the **Location in Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
2. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or other folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.


## Viewing the Nginx (Legacy) Dashboards

:::tip Filter with template variables    
Template variables provide dynamic dashboards that can rescope data on the fly. As you apply variables to troubleshoot through your dashboard, you view dynamic changes to the data for a quicker resolution to the root cause. You can use template variables to drill down and examine the data on a granular level. For more information, see [Filter with template variables](/docs/dashboards-new/filter-with-template-variables.md).
:::

### Overview

The **Nginx (Legacy) - Overview** dashboard provides an at-a-glance view of the NGINX server access locations, error logs along with connection metrics.

Use this dashboard to:
* Gain insights into originated traffic location by region. This can help you allocate computer resources to different regions according to their needs.
* Gain insights into your Nginx health using Critical Errors and Status of Nginx Server.
* Get insights into Active and dropped connections.

<img src={useBaseUrl('img/integrations/web-servers/NginxL-Overview.png')} alt="Nginx-Overview" />

### Error Logs Analysis

The **Nginx (Legacy) - Error Logs Analysis** dashboard provides a high-level view of log level breakdowns, comparisons, and trends. The panels also show the geographic locations of clients and clients with critical messages, new connections and outliers, client requests, request trends, and request outliers.

Use this dashboard to:
* Track requests from clients. A request is a message asking for a resource, such as a page or an image.
* Track and view client geographic locations generating errors.
* Track critical alerts and emergency error alerts.

<img src={useBaseUrl('img/integrations/web-servers/NginxL-Error-Logs-Analysis.png')} alt="NginxL-Error-Logs-Analysis" />

### Logs Timeline Analysis

The **Nginx (Legacy) - Logs Timeline Analysis** dashboard provides a high-level view of the activity and health of Nginx servers on your network. Dashboard panels display visual graphs and detailed information on traffic volume and distribution, responses over time, as well as time comparisons for visitor locations and server hits.

Use this dashboard to:
* To understand the traffic distribution across servers, provide insights for resource planning by analyzing data volume and bytes served.
* Gain insights into originated traffic location by region. This can help you allocate compute resources to different regions according to their needs.

<img src={useBaseUrl('img/integrations/web-servers/NginxL-Logs-Timeline-Analysis.png')} alt="Nginx Legacy" />

### Outlier Analysis

The **Nginx (Legacy) - Outlier Analysis** dashboard provides a high-level view of Nginx server outlier metrics for bytes served, number of visitors, and server errors. You can select the time interval over which outliers are aggregated, then hover the cursor over the graph to display detailed information for that point in time.

Use this dashboard to:
* Detect outliers in your infrastructure with Sumo Logic’s machine learning algorithm.
* To identify outliers in incoming traffic and the number of errors encountered by your servers.

You can use schedule searches to send alerts to yourself whenever there is an outlier detected by Sumo Logic.

<img src={useBaseUrl('img/integrations/web-servers/NginxL-Outlier-Analysis.png')} alt="Nginx Legacy" />

### Threat Intel

The **Nginx (Legacy) - Threat Intel** dashboard provides an at-a-glance view of threats to Nginx servers on your network. Dashboard panels display the threat count over a selected time period, geographic locations where threats occurred, source breakdown, actors responsible for threats, severity, and a correlation of IP addresses, method, and status code of threats.

Use this dashboard to:
* To gain insights and understand threats in incoming traffic and discover potential IOCs. Incoming traffic requests are analyzed using the [Sumo - Crowdstrikes](https://help.sumologic.com/07Sumo-Logic-Apps/22Security_and_Threat_Detection/Threat_Intel_Quick_Analysis/03_Threat-Intel-FAQ) threat feed.

<img src={useBaseUrl('img/integrations/web-servers/NginxL-Threat-Intel.png')} alt="Nginx Legacy" />

### Web Server Operations

The **Nginx (Legacy) - Web Server Operations** dashboard provides a high-level view combined with detailed information on the top ten bots, geographic locations, and data for clients with high error rates, server errors over time, and non 200 response code status codes. Dashboard panels also show information on server error logs, error log levels, error responses by a server, and the top URIs responsible for 404 responses.

Use this dashboard to:
* Gain insights into Client, Server Responses on Nginx Server. This helps you identify errors in Nginx Server.
* To identify geo locations of all Client errors. This helps you identify client location causing errors and helps you to block client IPs.

<img src={useBaseUrl('img/integrations/web-servers/NginxL-Web-Server-Operations.png')} alt="Nginx Legacy" />

### Visitor Access Types

The **Nginx (Legacy) - Visitor Access Types** dashboard provides insights into visitor platform types, browsers, and operating systems, as well as the most popular mobile devices, PC and Mac versions used.

Use this dashboard to:
* Understand which platform and browsers are used to gain access to your infrastructure.
* These insights can be useful for planning in which browsers, platforms, and operating systems (OS) should be supported by different software services.

<img src={useBaseUrl('img/integrations/web-servers/Nginx-Plus-Overview.png')} alt="Nginx Legacy" />

### Visitor Locations

The **Nginx (Legacy)- Visitor Locations** dashboard provides a high-level view of Nginx visitor geographic locations both worldwide and in the United States. Dashboard panels also show graphic trends for visits by country over time and visits by US region over time.

Use this dashboard to:
* Gain insights into geographic locations of your user base. This is useful for resource planning in different regions across the globe.

<img src={useBaseUrl('img/integrations/web-servers/NginxL-Visitor-Access-Types.png')} alt="Nginx Legacy" />

### Visitor Traffic Insight

The **Nginx (Legacy) - Visitor Traffic Insight** dashboard provides detailed information on the top documents accessed, top referrers, top search terms from popular search engines, and the media types served.

Use this dashboard to:
* Understand the type of content that is frequently requested by users.
* Help in allocating IT resources according to the content types.

<img src={useBaseUrl('img/integrations/web-servers/NginxL-Visitor-Traffic-Insight.png')} alt="Nginx Legacy" />


## Nginx (Legacy) Alerts

Sumo Logic has provided out of the box alerts available through [Sumo Logic monitors](https://help.sumologic.com/Visualizations-and-Alerts/Alerts/Monitors) to help you quickly determine if the Nginx server is available and performing as expected. These alerts are built based on logs and metrics datasets and have preset thresholds based on industry best practices and recommendations. They are as follows:

<table>
  <tr>
   <td><strong>Alert Name</strong>
   </td>
   <td><strong>Alert Description</strong>
   </td>
   <td><strong>Alert Condition</strong>
   </td>
   <td><strong>Recover Condition</strong>
   </td>
  </tr>
  <tr>
   <td>Nginx - Dropped Connections
   </td>
   <td>This alert fires when we detect dropped connections for a given Nginx server.
   </td>
   <td> &#62; 0
   </td>
   <td> &#60; &#61; 0
   </td>
  </tr>
  <tr>
   <td>Nginx - Critical Error Messages
   </td>
   <td>This alert fires when we detect critical error messages for a given Nginx server.
   </td>
   <td> &#62; 0
   </td>
   <td> &#60; &#61; 0
   </td>
  </tr>
  <tr>
   <td>Nginx - Access from Highly Malicious Sources
   </td>
   <td>This alert fires when an Nginx is accessed from highly malicious IP addresses.
   </td>
   <td> &#62; 0
   </td>
   <td> &#60; &#61; 0
   </td>
  </tr>
  <tr>
   <td>Nginx - High Client (HTTP 4xx) Error Rate
   </td>
   <td>This alert fires when there are too many HTTP requests (>5%) with a response status of 4xx.
   </td>
   <td> &#62; 0
   </td>
   <td> &#60; &#61; 0
   </td>
  </tr>
  <tr>
   <td>Nginx - High Server (HTTP 5xx) Error Rate
   </td>
   <td>This alert fires when there are too many HTTP requests (>5%) with a response status of 5xx.
   </td>
   <td> &#62; 0
   </td>
   <td> &#60; &#61; 0
   </td>
  </tr>
</table>