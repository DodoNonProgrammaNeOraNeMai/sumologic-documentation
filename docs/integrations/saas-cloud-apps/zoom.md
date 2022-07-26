---
id: zoom
title: Sumo Logic App for Zoom
sidebar_label: Zoom
---

import useBaseUrl from '@docusaurus/useBaseUrl';

<img src={useBaseUrl('img/integrations/saas-cloud-apps/zoom.png')} alt="DB icon" width="100"/>

Zoom unifies cloud video and audio conferencing, simple online meetings, and group messaging into one easy-to-use platform. The cloud platform facilitates collaboration across mobile devices, desktops, telephones, and room systems for an on-line meeting space you can depend on. Zoom allows you to stay connected wherever you go with face-to-face video, high quality screen sharing, and instant messaging.

The Sumo Logic App for Zoom provides visibility into how Zoom is being used across your organization, displaying analytics on performance, availability, security, and user activity. The app aggregates and reports on data so you can correlate and investigate trends and respond to incidents across all of your IT tools in a consistent and timely manner.


#### Log Types

Zoom uses Webhook events, that are documented in full on this [Zoom web page](https://marketplace.zoom.us/docs/api-reference/webhook-reference).

The Webhook events are grouped into the following core event types:

* Meeting Events
* Webinar Events
* Recording Events
* Zoom Room Events
* User Events
* Account Events


## Collect Logs for the Zoom App

This page shows you how to configure event collection for the Zoom App. Zoom uses Webhook events that are grouped into the following core event types:
* Meeting Events
* Webinar Events
* Recording Events
* Zoom Room Events
* User Events
* Account Events

For more information on Zoom Webhook events, see this[ Zoom web page](https://marketplace.zoom.us/docs/api-reference/webhook-reference).


#### Collection process overview

Configuring event collection for Zoom consists of the following tasks:

1. Adding a hosted collector and HTTP source.
2. Configuring Webhooks for event collection.


1.png "image_tooltip")
Some Webhook events may not be available based on the plan type. Refer to the prerequisites section (or each Webhook event type) on this [Zoom page](https://marketplace.zoom.us/docs/api-reference/webhook-reference/account-events/account-created#prerequisites) for account creation events.


##### Add a Hosted Collector and HTTP Source

This section demonstrates how to add a hosted Sumo Logic collector and HTTP Logs source, to collect logs for Zoom.

2.png "image_tooltip")

When you configure the HTTP Source, make sure to save the HTTP Source Address URL.

**To add a hosted collector and HTTP source, do the following:**

1. Do one of the following:
* If you already have a Sumo Logic Hosted Collector, identify the one you want to use.
* Create a new Hosted Collector as described in this document: [Configure a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors/Configure-a-Hosted-Collector).
1. Add an  HTTP source for logs, as described in this document: [HTTP Metrics and Logs Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/HTTP-Source).


##### Configure Webhooks for events collection


3.png "image_tooltip")
Some Webhook events may not be available based on the plan type. Refer to the Prerequisite section for each Webhook event type on this [Zoom page](https://marketplace.zoom.us/docs/api-reference/webhook-reference/account-events/account-created#prerequisites) for account-created event types.

This section shows you how to configure Webhooks to collect events from Zoom. For more information, see Zoom page [Create a Webhook-Only App](https://marketplace.zoom.us/docs/guides/getting-started/app-types/create-webhook-only-app).

**To configure Webhooks for Zoom events collection, do the following:**



1. Go to: [https://marketplace.zoom.us/](https://marketplace.zoom.us/) and login.
2. In the upper right corner, click **Develop > Build App**.
3. **Create** a Webhook Only App.
4. Specify the following App Information:
* **App Name**
* **Short Description**
* **Company Name**
* **Developer Name**
* **Developer Email Address**
1. Click **Continue**, and then enable** Event Subscriptions.**
2. Click **Add new event subscription **and provide the following information:
* **Subscription Name **(for example, Sumo Logic)
* **Event notification endpoint URL.** Provide the Sumo logic endpoint URL from this [step](https://help.sumologic.com/07Sumo-Logic-Apps/18SAAS_and_Cloud_Apps/Zoom/Collect_Logs_for_the_Zoom_App#Add_a_Hosted_Collector_and_HTTP_Source).
1. Click **Add events** and subscribe to all the Webhook Events.
2. Click Save and then click Continue.
3. **Activate** your newly created Webhook Only App.


#### Sample Log Message


```json
{
    "event":"meeting.participant_left",
    "payload":▼{
            "account_id":"eSqnB7aCS0KKx0_adadb1HQ",
            "object":▼{
                    "duration":60,
                    "start_time":"2020-04-01T19:24:06Z",
                    "timezone":"America/Denver",
                    "topic":"My Meeting",
                    "id":"981802874",
                    "type":2,
                    "uuid":"/m84vL38R3exBtjhvdWxMad==",
                            "participant":▼{
                            "leave_time":"2020-04-01T19:24:20Z",
                            "id":"FDGHUPeiSZGAa6pmYTlpiA",
                            "user_id":"16778240",
                            "user_name":"Test User"
                    },
                    "host_id":"FDGHUPeiSZADa6pmYTlpiA"
            }
    }
}
```



#### Query Sample


```
_sourceCategory=zoom
| json "event", "payload.object.start_time", "payload.object.topic", "payload.object.uuid", "payload.object.id", "payload.object.type", "payload.object.duration" as event, meeting_start_time, topic, meeting_instance_id, meeting_number, meeting_type, meeting_duration nodrop
| where event = "meeting.started"
| "Unknown" as meeting_type_desc
| if (meeting_type == 1, "Instant Meeting", meeting_type_desc) as meeting_type_desc
| if (meeting_type == 2, "Scheduled Meeting", meeting_type_desc) as meeting_type_desc
| if (meeting_type == 3, "Recurring Meeting with No Fixed Time", meeting_type_desc) as meeting_type_desc
| if (meeting_type == 4, "Meeting started with Personal Meeting ID", meeting_type_desc) as meeting_type_desc
| if (meeting_type == 8, "Recurring Meeting with Fixed Time", meeting_type_desc) as meeting_type_desc
| count by meeting_instance_id
| count
```



## Install the Zoom App and view the Dashboards

This page provides instructions for installing the Zoom App, as well as descriptions and examples for each of the dashboards.


### Install the App

Now that you have set up collection for the Zoom events, install the Sumo Logic App for Zoom to use the pre-configured dashboards that provide visibility into your environment.

**To install the app, do the following:**



1. Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.
2. From the **App Catalog**, search for and select the app**.**
3. To install the app, click **Add to Library** and complete the following fields.
    1. **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
    2. **Data Source.** Select either of these options for the data source. 
        * Choose **Source Category**, and select the source category associated with the Hosted Collector you configured earlier.
        * Choose **Enter a Custom Data Filter**, and enter a custom source category beginning with an underscore. Example: (_sourceCategory=MyCategory). 
    3. **Advanced**. Select the **Location in Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
4. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or other folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.


### Dashboard filters  

**Each dashboard has a set of filters** that you can apply to the entire dashboard, as shown in the following example. Click the funnel icon in the top dashboard menu bar to display a scrollable list of filters that narrow search results across the entire dashboard.


4.png "image_tooltip")


**Each panel has a set of filters** that are applied to the results for that panel only, as shown in the following example. Click the funnel icon in the top panel menu bar to display a list of panel-specific filters.


5.png "image_tooltip")



### Zoom - Overview

The Zoom - Overview dashboard provides an at-a-glance view of the state of your Zoom environment in terms of reliability, performance, user activity, and security by reporting on meetings, hosts, webinars, alerts and guest activity.

Use this dashboard to:



* Quickly identify and investigate Zoom issues your organization has been experiencing.
* Identify frequently used meeting-ids to prevent Zoom bombing.
* Assess the number of people in and out of your organization who are using Zoom and their level of activity.


6.png "image_tooltip")



### Zoom - Availability

The **Zoom - Availability** dashboard provides insights into meeting, webinar, and Zoom room alerts in your environment. A meeting alert event is triggered when a service issue is encountered during a meeting and a Zoom Room alert event is triggered when there is an issue related to a Zoom Room.  

Use this dashboard to:



* Quickly identify meeting issues such as unstable audio and video connections, and poor screen sharing quality.
* Quickly identify issues in a Zoom Room device such as low battery or connection issues.


7.png "image_tooltip")



### Zoom - User Activity

The **Zoom - User Activity** dashboard provides visibility into Zoom user presence and their activities. Panels display user trends, setting preferences, recording and screen sharing comparisons, as well as chat message details.

Use this dashboard to:



* Identify how users choose to appear in Zoom meetings Identify user setting changes.
* Determine the types of recordings most frequently used and the size of files generated to assess current resources and plan for growth.
* Analyze types of content shared during collaboration.


8.png "image_tooltip")



### Zoom - Guest Activity

The **Zoom - Guest Activity** dashboard provides visibility into the Zoom guest users, their activities, and trends. Panels also display detailed information on screen sharing with guest participants, meetings with regular guests, and those with the most guest participants.

Use this dashboard to:



* Monitor overall guest activity to assess resources.
* Determine the meeting topics that attracted the most guest participants.
* Identify which hosts had the most guest participants.


9.png "image_tooltip")



### Zoom - Administrator Activity

The **Zoom - Administrator Activity** dashboard provides insights into Administrative trends, user account activities, and user account trends.

Use this dashboard to:



* Audit activity by administrators.
* Quickly identify recent account and user changes.
* Monitor administrator activity trends to identify how to best optimize for the future.


10.png "image_tooltip")



### Zoom - Meeting Usage

The **Zoom - Meeting Usage** dashboard provides visibility into the number and types of Zoom meetings conducted, along with the hosts and participants of those meetings. Panels display meeting trends, as well as details on frequently used meeting numbers and hosts who have personal meeting rooms.

Use this dashboard to:



* Determine the level of collaboration occurring in your organization.
*  Monitor behavioral trends around how meetings are created, meeting duration, and  how often meetings end of time to plan for and allocate required resources.


11.png "image_tooltip")



### Zoom - Authentication

The Zoom - Authentication dashboard provides an insight into the number and type of logins, trends, and Zoom clients and devices used.

Use this dashboard to:



* Quickly identify types of devices and Zoom clients used to ensure users are not running vulnerable clients.
* Determine highest activity times for Zoom activity and collaboration  and plan accordingly.


12.png "image_tooltip")



### Zoom - Meeting Security

The **Zoom - Meeting Security** dashboard provides visibility into meeting security as it relates to frequently used meeting-id’s and personal meeting rooms, as well as monitor when meetings are updated in a way that don’t conform to security best practices.

Use this dashboard to:



* Identify frequently used meeting-ids and personal meetings rooms being used to prevent Zoom bombing.
* Quickly identify which meetings are being updated to bypass security best practices.


13.png "image_tooltip")



### Zoom - Webinars

The **Zoom - Webinars** dashboard provides visibility into the number and types of webinars, the participants, and trends. Panels also provide details on webinar authentications and comparisons of registered participants and those who actually participate.

Use this dashboard to:



* Determine the number and types of webinars and the participants who joined.
* Identify interest level, participation and assess the success of the webinars.


14.png "image_tooltip")