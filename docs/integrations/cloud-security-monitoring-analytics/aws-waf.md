---
id: aws-waf
title: Sumo Logic App for AWS WAF Cloud Security Monitoring and Analytics
sidebar_label: AWS WAF
---

import useBaseUrl from '@docusaurus/useBaseUrl';

AWS WAF (web application firewall) data is a rich source of security findings, as it allows you to monitor the HTTP and HTTPS requests that are forwarded to CloudFront and let you control overall access to your content. Each dashboard within this application takes a different lens on AWS WAF data, from traffic patterns to threat intelligence, allowing you to truly identify the needles in the haystack that drives critical security concerns within your AWS infrastructure.

## Collect Logs

To configure Collection for AWS WAF App, follow the instructions from https://help.sumologic.com/07Sumo-Logic-Apps/01Amazon_and_AWS/AWS_WAF/Collect_Logs_for_the_AWS_WAF_App

## Install the AWS WAF Security Analytics App

Now that you have set up collection for AWS WAF, install the Sumo Logic App for AWS AWS to use the pre-configured searches and dashboards.

**To install the app:**

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.

1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.

Version selection is applicable only to a few apps currently. For more information, see the Install the Apps from the Library.


1. To install the app, complete the following fields.
    1. **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
    2. **Data Source.** Select either of these options for the data source. 
        * Choose **Source Category**, and select a source category from the list. 
        * Choose **Enter a Custom Data Filter**, and enter a custom source category beginning with an underscore. Example: (_sourceCategory=MyCategory). 
    3. **Advanced**. Select the **Location in Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
2. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or another folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.


## Viewing Dashboards


### AWS WAF - Security Monitoring - Overview

See an overview of threats detected and traffic passing through AWS WAF.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/AWS-WAF-Security-Monitoring-Overview.png')} alt="AWS WAF dashboards" />

**Traffic Map. **Geolocation heat map of inbound and outbound traffic passing through the WAF.

**Traffic Trend.** Line chart comparing the volume of blocked and allowed connections.

**IP Count.** Line chart of unique IP addresses connecting over time.

**URI Hits.** Table of directory and file paths connected to sorted by frequency.

**All Traffic by Rule Type. **Column chart of connections by WAF rule type.

**HTTP Versions.** Donut chart showing the total number of connections broken down by HTTP versions.

**All Traffic by Rule ID.** Table showing connections sorted by most frequent rule ID.

**HTTP Methods.** Donut chart showing the total number of connections broken down by HTTP methods.


### AWS WAF - Security Analytics - Traffic

See details of threats allowed and blocked by AWS WAF.

img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/AWS-WAF-Security-Analytics-Traffic.png')} alt="AWS WAF dashboards" />

**Traffic by Geographic Location. **Each section contains the same panels with the only difference being traffic allowed or blocked.

**Traffic by Location. **Geolocation heatmap of locations. Zoom into the map for additional details of the location.

**Traffic by Country.** Column chart of connections by country over time. Multiple countries can be selected by clicking on one or more countries in the legend at the bottom.

**Anomalies Within Traffic.** Line chart of connections over time. The grey thresholds show three standard deviations based on the last ten means. Pink triangles show values outside the thresholds that represent anomalies.

**Traffic by Rule Type. **Donut chart of connections broken down by rule type.

**Traffic by Rule ID.** A table detailing rule IDs of connections sorted by frequency.


### AWS WAF - Security Analytics - Threat Intelligence

See details of allowed and blocked AWS WAF traffic that matches the built-in Sumo Logic threat IP list.

img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/AWS-WAF-Security-Analytics-Threat-Intelligence.png')} alt="AWS WAF dashboards" />

**Unique Threats Map.** Geolocation heatmap of connection locations.

**Threats Trend.** Line chart of connections over time.

**Threats by Actors. **Donut chart showing the ratios of connections attributed to particular threat actor groups.

**Traffic by Threat Confidence. **Donut chart showing the ratios of connections broken down by confidence levels.

**Threat Breakdown by Sources. **Donut chart showing the ratios of connections broken down by source categories.

**Traffic by Malicious IPs.** Table showing details of connections keyed off of remote IP address.