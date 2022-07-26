---
id: windows
title: Sumo Logic App for Windows Cloud Security Monitoring and Analytics
sidebar_label: Windows
---

import useBaseUrl from '@docusaurus/useBaseUrl';

The Cloud Security Monitoring & Analytics for Windows App offers pre-built dashboards and queries to help you track your Windows system, user accounts, login activity, and Windows updates.

This page provides instructions for configuring log collection for the Windows - Cloud Security Monitoring and Analytics App.

## Collecting Logs

### Log Types

The Windows - Cloud Security Monitoring and Analytics App uses Windows Security Event and System Event logs. It does not work with third-party logs.


### Configure a Collector and a Source

**To configure a collector and source, do the following:

1. Configure an [Installed Windows collector](https://help.sumologic.com/03Send-Data/Installed-Collectors/03Install-a-Collector-on-Windows) through the user interface or from the command line.
2. Configure either a local or remote Windows Event Log source. To configure a Windows Event Log source set the following:
    * **Event Format.** Select **Collect using JSON format. \

Collect using JSON format.** Events are formatted into JSON that is designed to work with Sumo Logic features, making it easier for you to reference your data.
    * **Event Collection Level.** When JSON format is selected you have to select Complete Message from the dropdown.


**Complete Message** will ingest the entire event content along with metadata.

For more information on local or remote Windows Event Log Source configuration, refer to [Local Windows Event Log Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Local-Windows-Event-Log-Source) and [Remote Windows Event Log Source](https://help.sumologic.com/03Send-Data/Sources/01Sources-for-Installed-Collectors/Remote-Windows-Event-Log-Source).


### Sample Log Messages

```
{"TimeCreated":"2020-10-12T07:31:14+000039800Z","EventID":"1102","Task":104,"Correlation":"","Keywords":"Audit
Success","Channel":"Security","Opcode":"Info","Security":"","Provider":{"Guid":"{fc65ddd8-d6ef-4962-83d5-6e5cfe9ce148}",
"Name":"Microsoft-Windows-Eventlog"},"EventRecordID":101802,"Execution":{"ThreadID":2896,"ProcessID":908},"Version":0,"Computer":
"WIN-6D5CO5AB123","Level":"Informational","EventData":{},"UserData":{"LogFileCleared":{"xmlns":"http://sz2016rose.ddns.net/win/2004/08/windows/eventlog",
"SubjectUserName":"Administrator","SubjectDomainName":"WIN-6D5CO5AB123","SubjectLogonId":"0x1971888","SubjectUserSid":"S-1-5-21-2020-10-12T07:31:14-203418232-2020-10-12T07:31:14-500"}},"Message":"The audit log was cleared.\r\nSubject:\r\n\tSecurity ID:\tWIN-6D5CO5AB123\\Administrator\r\n\tAccount Name:\tAdministrator\r\n\tDomain Name:\tWIN-6D5CO5AB123\r\n\tLogon ID:\t0x1971888"}
```



### Query Sample

The sample query is from the **Recent Policy Changes** panel from **Windows - Overview** dashboard.

```
_sourceCategory=Labs/windows-jsonformat ( "Audit Policy Change" or "System audit policy was changed" or *policy*change* or "Policy Change" or 4902 or 4904 or 4905 or 4906 or 4907 or 4912 or 4715 or 4719 or 4739)
| json "EventID", "Computer", "Message" as event_id, host, msg_summary nodrop
| parse regex field = msg_summary "(?<msg_summary>.*\.*)"
| where (event_id in ("4902", "4904", "4905", "4906", "4907", "4912", "4715", "4719", "4739") or msg_summary matches "System audit policy was changed*") and host matches "*"
| count by msg_summary | sort by _count, msg_summary asc
```

## Installing the Windows Cloud Security App

This page provides instructions for installing the Cloud Security Monitoring & Analytics for Windows App, along with examples of each of the App dashboards. The Cloud Security Monitoring & Analytics for Windows App offers pre-built dashboards and queries to help you track your Windows system, user accounts, login activity, and Windows updates.

Now that you have set up collection, install the Cloud Security Monitoring & Analytics for Windows App to use the pre-configured searches and [Dashboards](https://help.sumologic.com/07Sumo-Logic-Apps/04Microsoft-and-Azure/PCI_Compliance_for_Windows/PCI-Compliance-for-Windows-App-Dashboards#Dashboards) that provide insight into your data.  

**To install the app**:

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.


1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.

Version selection is applicable only to a few apps currently. For more information, see the [Install the Apps from the Library](https://help.sumologic.com/01Start-Here/Library/Apps-in-Sumo-Logic/Install-Apps-from-the-Library).



1. To install the app, complete the following fields.
    1. **App Name.** You can retain the existing name, or enter a name of your choice for the app. 
    2. **Data Source.** Select either of these options for the data source. 
        * Choose **Source Category**, and select a source category from the list. 
        * Choose **Enter a Custom Data Filter**, and enter a custom source category beginning with an underscore. Example: (_sourceCategory=MyCategory). 
    3. **Advanced**. Select the **Location in Library** (the default is the Personal folder in the library), or click **New Folder** to add a new folder.
2. Click **Add to Library**.

Once an app is installed, it will appear in your **Personal** folder, or other folder that you specified. From here, you can share it with your organization.

Panels will start to fill automatically. It's important to note that each panel slowly fills with data matching the time range query and received since the panel was created. Results won't immediately be available, but with a bit of time, you'll see full graphs and maps.



## Viewing Windows Security Dashboards

:::tip Filter with template variables    
Template variables provide dynamic dashboards that can rescope data on the fly. As you apply variables to troubleshoot through your dashboard, you view dynamic changes to the data for a quicker resolution to the root cause. You can use template variables to drill down and examine the data on a granular level. For more information, see [Filter with template variables](/docs/dashboards-new/filter-with-template-variables.md).
:::


### Security Monitoring - Inventory

**Windows - Security Monitoring - Inventory**: Utilize this dashboard to quickly assess system inventory and recent system reboots/restarts in order to understand device activity within your environment.

**Use case:** System inventory and system boots are leading indicators of potential security threats to be aware of, and that may require further attention.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Monitoring-Inventory.png')} alt="Windows cloud Security Analytics dashboards" />


### Security Monitoring - Critical Events

**Windows - Security Monitoring - Critical Events**: When audit logs are tampered, services are stopped, and ingestion delays go above ten seconds, these are all good indicators that there are action items to be taken to resolve issues within your Windows machines.

**Use case:** Evaluating unexpected critical events within Windows infrastructure allows for teams to stay on top of any necessary remedial steps.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Monitoring-Critical-Events.png')} alt="Windows cloud Security Analytics dashboards" />


### Security Analytics - Windows Updates

**Windows - Security Analytics - Windows Updates**: Rich visualizations indicate the ongoing flow of Windows updates within your organization, so that engineering teams are made aware of red flags or update schedules that require updating.

**Use case:** Assess overall trend lines via the dashboard, and dive into specific events and event types to understand specific update failures.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Analytics-Windows-Updates.png')} alt="Windows cloud Security Analytics dashboards" />

### Security Analytics - Windows Firewall

**Windows - Security Analytics - Windows Firewall**: This dashboard allows you to view Windows Firewall activity including Firewall Service Events, MPSSVC Rule Level Policy Changes, and Filtering Platform Policy Changes.

**Use case:** Filter by EventID or specific device to analyze traffic patterns within your Windows environments

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Analytics-Windows-Firewall.png')} alt="Windows cloud Security Analytics dashboards" />

### Security Analytics - Windows Defender

**Windows - Security Analytics - Windows Defender**: The Windows Defender app is designed to offer visibility into Defender Service Events and Defender Threat Events at the Computer and Trend level.

**Use case:** Understand cross-sections of service events and threat events, filtered down by specific devices to stay ahead of changing attack surfaces.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Analytics-Windows-Defender.png')} alt="Windows cloud Security Analytics dashboards" />


### Security Analytics - User Group Updates
**Windows - Security Analytics - User Group Updates**: User Group Updates are generally a good litmus test for a summarized trend of how successfully Windows groups are being updated and on a correct cadence depending on policy requirements.

**Use case:** Aligning group update schedules to existing policies within your organization, and informing future policy changes as well based on triangulation against security events tied to update changes.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Analytics-User-Group-Updates.png')} alt="Windows cloud Security Analytics dashboards" />


### Security Analytics - User Authentication
**Windows - Security Analytics - User Authentication**: This dashboard points to snapshots of trends for successful logins as well as unsuccessful logins.

**Use case:** Unsuccessful logins in particular will indicate potential threats including brute-force attempts.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Analytics-User-Authentication.png')} alt="Windows cloud Security Analytics dashboards" />


### Security Analytics - User Account Changes
**Windows - Security Analytics - User Account Changes**: The User Account Changes dashboard shows user accounts created, deleted, locked out, as well as password changes for a given account.

**Use case:** Begin with the summarized visuals in the left columns, and navigate to the right column details to understand specific computers and subjects involved in the given activity.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Analytics-User-Account-Changes.png')} alt="Windows cloud Security Analytics dashboards" />


### Security Analytics - TLS Certificates and Secure Channels

**Windows - Security Analytics - TLS Certificates and Secure Channels**: This dashboard indicates TLS Certificate and Secure Channel activity and associated computers, trends, and latest events.

**Use case:** By mapping changes in certificates and associated trends, teams can identify areas of improvement for current TLS Certificate deployments.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Analytics-TLS-Certificates-and-Secure-Channels.png')} alt="Windows cloud Security Analytics dashboards" />


### Security Analytics - Default Accounts Usage

**Windows - Security Analytics - Default Accounts Usage**: This dashboard allows you to filter Default Accounts Usage by EventID, Computer, SubjectUserName, and TargetUserName.

**Use case:** Honeycomb visuals also point to potential hotspots, or in other words specific computers that may require further attention relative to typical expected behavior within your organization.

<img src={useBaseUrl('img/integrations/cloud-security-monitoring-analytics/Windows-Security-Analytics-Default-Accounts-Usage.png')} alt="Windows cloud Security Analytics dashboards" />