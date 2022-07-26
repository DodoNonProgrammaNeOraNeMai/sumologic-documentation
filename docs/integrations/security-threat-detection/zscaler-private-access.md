---
id: zscaler-private-access
title: Zscaler Private Access
sidebar_label: Zscaler Private Access
description: Zscaler Private Access
---

import useBaseUrl from '@docusaurus/useBaseUrl';


The Zscaler Private Access App collects logs from Zscaler using the Log Streaming Service (LSS) to populate pre-configured searches and Dashboards. The dashboards provide easy-to-access visual insights into user behaviors, security, connector status, and risk.

#### Log Types
1

The Sumo Logic App for Zscaler Private Access uses LSS to send the following logs, as documented [here](https://help.zscaler.com/zpa/about-log-streaming-service):

* App Connector Status: Information related to an App Connector's availability and connection to ZPA. To learn more, see[ App Connector Status Log Fields](https://help.zscaler.com/zpa/connector-status-log-fields).
* User Activity: Information on end user requests to Applications. To learn more, see[ User Activity Log Fields](https://help.zscaler.com/zpa/user-activity-log-fields).
* User Status: Information related to an end user's availability and connection to ZPA. To learn more, see[ User Status Log Fields](https://help.zscaler.com/zpa/user-status-log-fields).
* Browser Access Logs: HTTP log information related to Browser Access. To learn more, see[ Browser Access Log Fields](https://help.zscaler.com/zpa/browser-access-log-fields) and[ About Browser Access](https://help.zscaler.com/zpa/about-BrowserAccess).
* Audit Logs: Session information for all admins accessing the ZPA Admin Portal. To learn more, see[ Audit Log Fields](https://help.zscaler.com/zpa/about-audit-log-fields) and[ About Audit Logs](https://help.zscaler.com/zpa/about-audit-logs).


## Collect Logs for the Zscaler Private Access (ZPA) App

Zscaler Private Access uses the Log Streaming Service (LSS), to stream logs from the Zscaler service and deliver them to the Sumo Logic Hosted collector via Syslog.

LSS is deployed using two components, a log receiver and a ZPA App Connector. LSS resides in ZPA and initiates a log stream through a ZPA Public Service Edge (formerly Zscaler Enforcement Node or ZEN). The App Connector resides in your company's enterprise environment. It receives the log stream and then forwards it to Sumo Logic Cloud Syslog.

To collect logs for Zscaler Private Access, perform these steps, detailed in the following sections:

1. Configure Sumo Logic Hosted Collector and a Cloud Syslog Source
2. Configure App Connector in ZPA
3. Deploy an App Connector on a Supported Platform
4. Configure Log Receivers in ZPA to send logs to Sumo Logic


2



#### Configure Sumo Logic Hosted Collector and a Cloud Syslog Source
3

To collect logs for ZPA, do the following in Sumo Logic:

1. Configure a [Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors).
2. Perform the steps in[ Configure a Cloud Syslog Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-Syslog-Source#Configure_a_Cloud_Syslog_Source). and configure the following Source fields:
    * **Name**. (Required) A name is required. Description is optional.
    * **Source Category**. (Required) [Provide a realistic Source Category example for this data type.] The Source Category metadata field is a fundamental building block to organize and label Sources. \
For details see[ Best Practices](https://help.sumologic.com/03Send-Data/01-Design-Your-Deployment/Best-Practices%3A-Good-Source-Category%2C-Bad-Source-Category).
3. In the Advanced section, specify the following configurations:
    * **Enable Timestamp Parsing**. True
    * **Time Zone**. Use time zone from log file. If none is detected use: Use Collector Default.
    * **Timestamp Format**. Auto Detect
4. In the Processing Rules for Logs section, add a Processing Rule:
    * **Name:** `Remove Syslog String`
    * **Filter**: `(<\d+\>1 - - - - - - {)`
    * **Type**: `Mask messages that match`
    * **Mask String**: `{`


4




5. Click **Save**.


5



6
Copy and paste the **Token, Host and Port** in a secure location. You will need these when you configure ZPA LSS.


#### Configure App Connector in ZPA
7


Configure a new [App Connector](https://help.zscaler.com/zpa/configuring-connectors) in ZPA. Copy the provisioning key created/selected during App Connector configuration.


8



#### Deploy an App Connector on a Supported Platform
9


After you add an [App Connector](https://help.zscaler.com/zpa/about-connectorprovisioningwizard), you must deploy it. Deployment consists of installing the App Connector and also enrolling the App Connector, which allows the App Connector to obtain a TLS client certificate that it must use to authenticate itself to the ZPA cloud. After deployment, the App Connector is ready to send logs to Sumo Logic.

Before you begin a deployment, read[ App Connector Deployment Prerequisites](https://help.zscaler.com/zpa/connector-deployment-prerequisites) which provides detailed information on VM image sizing and scalability, supported platform requirements, deployment best practices, and other essential guidelines.

The deployment process differs depending on the platform used for the App Connector. Zscaler recommends that App Connectors be deployed in pairs, to ensure continuous availability during software upgrades.

To deploy the App Connector, see the [Deployment Guide](https://help.zscaler.com/knowledge-base-categories/supported-platforms-connectors) for your platform.


#### Configure Log Receivers in ZPA to send logs to Sumo Logic
10


Once you have deployed the App Connector, configure log receivers to send logs to the Sumo Logic cloud syslog endpoint using the following steps:



1. Log into your ZPA system.
2. Go to **Administration** > **Log Receivers**.
3. Click **Add Log Receiver**.
4. In the **Add Log Receiver** window, configure the following tabs:
    1. [Log Receiver](https://help.zscaler.com/zpa/configuring-log-receiver#Step1)


11




1. **Name**: Enter a name for the log receiver. The name cannot contain special characters, with the exception of periods (.), hyphens (-), and underscores ( _ ).
2. **Description**: (Optional) Enter a description.
3. **Domain or IP Address**: Enter the Domain name from the Sumo Logic[ Cloud Syslog Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-Syslog-Source).
4. **TCP Port**: Enter the TCP port number from the Sumo Logic[ Cloud Syslog Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-Syslog-Source). Default: 6514
5. **TLS Encryption**: Select Enabled.
6. **Connector Groups**: Choose the App Connector groups that can forward logs to the receiver, and click **Done**. You can search for a specific group, click **Select All** to apply all groups, or click **Clear Selection** to remove all selections.
7. Click **Next**.
1. [Log Stream](https://help.zscaler.com/zpa/configuring-log-receiver#Step2)
    1. In the **Log Stream** tab, select a **Log Type** from the drop-down menu:
        1. **User Activity**: Information on end user requests to applications. To learn more, see[ User Activity Log Fields](https://help.zscaler.com/zpa/user-activity-log-fields).
        2. **User Status**: Information related to an end user's availability and connection to ZPA. To learn more, see[ User Status Log Fields](https://help.zscaler.com/zpa/user-status-log-fields).
        3. **Connector Status**: Information related to an App Connector's availability and connection to ZPA. To learn more, see[ App Connector Status Log Fields](https://help.zscaler.com/zpa/connector-status-log-fields).
        4. **Browser Access**: HTTP log information related to Browser Access. To learn more, see[ Browser Access Log Fields](https://help.zscaler.com/zpa/http-log-fields) and[ About Browser Access](https://help.zscaler.com/zpa/about-BrowserAccess).
        5. **Audit Logs**: Session information for all admins accessing the ZPA Admin Portal. To learn more, see[ About Audit Log Fields](https://help.zscaler.com/zpa/about-audit-log-fields) and[ About Audit Logs](https://help.zscaler.com/zpa/about-audit-logs).
    2. In the **Log Template** field, select **JSON.**
    3. The default **Log Stream Content **that is displayed will change based on the **Log Type** and **Log Template** you selected in previous steps. \
 \
You can also edit the log stream content within the text field in order to capture specific fields and create a **Custom **log template. To learn more, see[ Understanding the Log Stream Content Format](https://help.zscaler.com/zpa/understanding-log-stream-content-format). \
 \
Edit the the log stream content, paste the following text in the beginning of the template:

    ```
    <165>1 - - - - - - <Syslog Token>
    ```



    For **Syslog Token,** enter the token from the Sumo Logic[ Cloud Syslog Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-Syslog-Source). The token should end with **@41123**. This number is the Sumo Logic Private Enterprise Number (PEN).



12




1. You can define a streaming **Policy **for the log receiver. For example, you can create a policy where the receiver will only capture logs for a specified segment group or a specific set of session status error codes. The criteria you can use is dependent upon the **Log Type** you selected. For various options to define a streaming policy, see [ZPA help](https://help.zscaler.com/zpa/configuring-log-receiver#Step2).
2. Click **Next**.
1. Review
    1. In the **Review** tab, review your log receiver configuration, and click **Save**.


13




1. Repeat the previous steps for all the **Log Types**:
    1. **User Activity**: Information on end user requests to applications. To learn more, see[ User Activity Log Fields](https://help.zscaler.com/zpa/user-activity-log-fields).
    2. **User Status**: Information related to an end user's availability and connection to ZPA. To learn more, see[ User Status Log Fields](https://help.zscaler.com/zpa/user-status-log-fields).
    3. **Connector Status**: Information related to an App Connector's availability and connection to ZPA. To learn more, see[ App Connector Status Log Fields](https://help.zscaler.com/zpa/connector-status-log-fields).
    4. **Browser Access**: HTTP log information related to Browser Access. To learn more, see[ Browser Access Log Fields](https://help.zscaler.com/zpa/http-log-fields) and[ About Browser Access](https://help.zscaler.com/zpa/about-BrowserAccess).
    5. **Audit Logs**: Session information for all admins accessing the ZPA Admin Portal. To learn more, see[ About Audit Log Fields](https://help.zscaler.com/zpa/about-audit-log-fields) and[ About Audit Logs](https://help.zscaler.com/zpa/about-audit-logs).
2. The end result should look like below:


14




1. At this point, ZPA should start sending logs to Sumo Logic.


## Install the Zscaler Private Access App and View the Dashboards


This page has instructions for installing the Sumo App for Zscaler Private Access and descriptions of each of the app dashboards.


#### Install the App
15


Now that you have set up collection for HAProxy, you can install the HAProxy App to use the pre-configured searches and dashboard that provide insight into your data.

**To install the App, do the following:**

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.



1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.


16
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


#### Filter with template variables
17


Template variables provide dynamic dashboards that can re-scope data on the fly. As you apply variables to troubleshoot through your dashboard, you view dynamic changes to the data for a quicker resolution to the root cause. For more information, see the [Filter with template variables](https://help.sumologic.com/Visualizations-and-Alerts/Dashboard_(New)/Filter_with_template_variables) help page. You can use template variables to drill down and examine the data on a granular level.


#### Dashboards  
18



### ZPA - Overview
19


The **ZPA - Overview** Dashboard focuses on the overall health of the ZPA system.

**Use this dashboard to**:



* Gain insights into ZPA health.
* Manage ZPA connector health.


20





### ZPA - Audit
21


The **ZPA - Audit** Dashboard focuses the changes in the ZPA admin UI. It allows easy tracking and change management.

**Use this dashboard to**:



* Gain insights into ZPA configuration changes.
* Easily identify the mis-configurations for erratic behavior.


22





### ZPA - Connectors
23


The **ZPA - Connectors** Dashboard focuses on connector health and resource utilization.

**Use this dashboard to**:



* Gain insights into ZPA connector health.
* Identify and manage connectors erroring out or having resource constraints.


24



### ZPA - Performance
25


The **ZPA - Performance** Dashboard focuses on the performance of the connectors and the ZPA system.

**Use this dashboard to**:



* Gain insights into ZPA Performance.
* Manage ZPA connector setup times to determine potential issues.


26





### ZPA - User Activity
27


The **ZPA - User Activity** Dashboard focuses on the users activity.

**Use this dashboard to**:



* Gain insights into User activity.


28



### ZPA - Users
29


The **ZPA - Users** Dashboard focuses on the user details.

**Use this dashboard to**:



* Gain insights into User connections and Access.
* Manage Policy and Timeout blocks.


30