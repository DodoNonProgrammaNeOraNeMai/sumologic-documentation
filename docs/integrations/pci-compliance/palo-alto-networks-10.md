---
id: palo-alto-networks-10
title: PCI Compliance App for Palo Alto Networks 10
sidebar_label: Palo Alto Networks 10
---

The Sumo Logic App for Payment Card Industry (PCI) Compliance for Palo Alto Networks 10 offers dashboards to monitor firewall traffic activity for compliance with PCI requirements 01, 02, and 04.


#### Log types
1.gif "image_tooltip")

<table>
  <tr>
   <td><strong>Log type</strong>
   </td>
   <td><strong>Description</strong>
   </td>
   <td><strong>Supported log format</strong>
   </td>
   <td><strong>For more information</strong>
   </td>
  </tr>
  <tr>
   <td>Traffic
   </td>
   <td>Entries for the start and end of each session, including date and time; source and destination zones, addresses and ports; application name; security rule applied to the traffic flow; rule action (allow, deny, or drop); ingress and egress interface; number of bytes; and session end reason.
   </td>
   <td>Syslog
   </td>
   <td><a href="https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/monitoring/view-and-manage-logs/log-types-and-severity-levels/traffic-logs">Traffic Logs</a>
   </td>
  </tr>
</table>



## Collect Logs for PCI Compliance for Palo Alto Networks 10

This page provides instructions for configuring log collection for the Sumo Logic app for PCI Compliance for Palo Alto Networks 10, as well as sample log messages and a query example from an app dashboard.


### Collection process overview
2.gif "image_tooltip")


Configuring log collection for PCI Compliance for Palo Alto Networks 10 includes the following tasks:



1. Create a hosted collector with a Cloud Syslog source
2. Define the destination for the logs.
3. Configure syslog forwarding
4. Verify logs in Palo Alto Networks


3.png "image_tooltip")
You must have Palo Alto Networks Web administrative user permissions to successfully complete these tasks.


### Step 1. Create a hosted collector and Cloud Syslog source
4.gif "image_tooltip")


In this step you configure a hosted collector with a Cloud Syslog source that will act as Syslog server to receive logs and events from Palo Alto Networks devices.

**To configure a hosted collector with a Cloud Syslog source, do the following:**

1. Log in to Sumo Logic and [create a Hosted Collector](https://help.sumologic.com/03Send-Data/Hosted-Collectors#Create_a_Hosted_Collector).
2. Create a [Cloud Syslog Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Cloud-Syslog-Source) on the hosted collector, specifying the following:
    1. Enter a Source Name.
    2. Provide a Source Category: **NW/PAN/V10**
3. Click **Save**.
4. Copy the Token, host URL and TCP TLS Port to a safe place. You will need this information in the tasks that follow.


### Step 2. Define the destination for the logs
5.gif "image_tooltip")


In this step you create a server profile where you can define the log destination. This will be the host name, port and protocol (TLS) of the Sumo Logic Cloud Syslog source.

**To create a server profile specifying  the log destination, do the following:**



1.  Login to the Palo Alto Networks Web interface as an administrative user.
2. Select **Device tab > Server Profiles > Syslog**.
3. Click **Add** at the bottom of the screen and provide endpoint details and a profile name, such as Sumo_Logs_Profile01.
4. In the **Syslog Server Profile** window, select the **Servers** tab and click **Add**.
5. In the **Servers** window, specify the following information:
    1. **Name.** Sumo_CloudSyslog_EndPoint01
    2. **Syslog Server.** URL from [Step 1](https://help.sumologic.com/07Sumo-Logic-Apps/16PCI_Compliance/PCI_Compliance_for_Palo_Alto_Networks_10/Collect_Logs_for_PCI_Compliance_for_Palo_Alto_Networks_10#Step_1._Create_a_hosted_collector_and_Cloud_Syslog_source)
    3. **Transport**. SSL
    4. **Port**. Port from [Step 1](https://help.sumologic.com/07Sumo-Logic-Apps/16PCI_Compliance/PCI_Compliance_for_Palo_Alto_Networks_10/Collect_Logs_for_PCI_Compliance_for_Palo_Alto_Networks_10#Step_1._Create_a_hosted_collector_and_Cloud_Syslog_source)
    5. **Format**. IETF
    6. **Facility.** LOG_USER
6. In the **Syslog Server Profile** window, select the **Custom Log Format** tab, and use the following custom format for the [Traffic](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/traffic-log-fields.html) log type:
```
,$receive_time,$serial,$type,$subtype,,$time_generated,$src,$dst,$natsrc,$natdst,$rule,$srcuser,$dstuser,$app,$vsys,$from,$to,$inbound_if,$outbound_if,$logset,,$sessionid,$repeatcnt,$sport,$dport,$natsport,$natdport,$flags,$proto,$action,$bytes,$bytes_sent,$bytes_received,$packets,$start,$sec,$category,,$seqno,$actionflags,$srcloc,$dstloc,,$pkts_sent,$pkts_received,$session_end_reason,$dg_hier_level_1,$dg_hier_level_2,$dg_hier_level_3,$dg_hier_level_4,$vsys_name,$device_name,$action_source,$src_uuid,$dst_uuid,$tunnelid/$imsi,$monitortag/$imei,$parent_session_id,$parent_start_time,$tunnel,$assoc_id,$chunks,$chunks_sent,$chunks_received,$rule_uuid,$http2_connection <em><Token from [Step 1](https://help.sumologic.com/07Sumo-Logic-Apps/16PCI_Compliance/PCI_Compliance_for_Palo_Alto_Networks_10/07Sumo-Logic-Apps/16PCI_Compliance/PCI_Compliance_for_Palo_Alto_Networks_10/Collect_Logs_for_PCI_Compliance_for_Palo_Alto_Networks_10#Step_1._Create_a_hosted_collector_and_Cloud_Syslog_source)>
```
7. Click OK.
8. Commit the changes.


### Step 3. Configure syslog forwarding
6.gif "image_tooltip")


To configure syslog forwarding for each Traffic logs, follow the steps to [Configure Log Forwarding](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/configure-log-forwarding) as described in the Palo Networks documentation.


### Step 4. Verify logs in Palo Alto Networks
7.gif "image_tooltip")


In this step, you view logs using the Palo Alto Network Web interface to confirm the logs are generated on the firewall.

**To verify the logs in Palo Alto Networks, do the following:**



1. In the Palo Alto Networks UI, select **Monitor** > **Logs**.
2. Once the setup is done, log in to Sumo Logic.
3. To validate that the logs are flowing to Sumo Logic, run a query using the source category you configured during [Step 1](https://help.sumologic.com/07Sumo-Logic-Apps/16PCI_Compliance/PCI_Compliance_for_Palo_Alto_Networks_10/Collect_Logs_for_PCI_Compliance_for_Palo_Alto_Networks_10#Step_1._Create_a_hosted_collector_and_Cloud_Syslog_source), such as:
`_sourceCategory = NW/PAN/V10`


### Sample logs
8.gif "image_tooltip")


The PCI Compliance for Palo Alto Networks 10 App uses [Traffic](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/traffic-log-fields.html) logs.


```
Oct 09 10:19:15 SumPunFw07.sumotest.com 1,2019/10/09 10:19:15,001234567890002,TRAFFIC,drop,2304,2019/10/09 10:19:15,209.118.103.150,160.177.222.249,0.0.0.0,0.0.0.0,InternalServer,,,not-applicable,vsys1,inside,z1-FW-Transit,ethernet1/2,,All traffic,2019/10/09 10:19:15,0,1,63712,443,0,0,0x0,udp,deny,60,60,0,1,2019/10/09 10:19:15,0,any,0,0123456789,0x0,Netherlands,10.0.0.0-10.255.255.255,0,1,0,policy-deny,0,0,0,0,,SumPunFw07,from-policy,,,0,,0,,N/A,0,0,0,0,1202585d-b4d5-5b4c-aaa2-d80d77ba456e,0
```



### Sample Query
9.gif "image_tooltip")


This example query is from the **Allowed Network Activity by Direction** panel of the **Accepted and Rejected Traffic** dashboard.


```
_sourceCategory = Labs/PaloAltoNetworksv10 TRAFFIC allow
| csv _raw extract 1 as f1, 2 as Receive_Time, 3 as serialNum, 4 as type, 5 as subtype, 6 as f2, 7 as LogGenerationTime, 8 as src_ip, 9 as dest_ip, 10 as NAT_src_ip, 11 as NAT_dest_ip, 12 as ruleName, 13 as src_user, 14 as dest_user, 15 as app, 16 as vsys, 17 as src_zone, 18 as dest_zone, 19 as inbound_interface, 20 as outbound_interface, 21 as LogAction, 22 as f3, 23 as SessonID, 24 as RepeatCount, 25 as src_port, 26 as dest_port, 27 as NAT_src_port, 28 as NAT_dest_port, 29 as flags, 30 as protocol, 31 as action, 32 as bytes, 33 as bytes_sent, 34 as bytes_recv, 35 as Packets, 36 as StartTime, 37 as ElapsedTime, 38 as Category, 39 as f4, 40 as seqNum, 41 as ActionFlags, 42 as src_Country, 43 as dest_country, 44 as f5, 45 as pkts_sent, 46 as pkts_received, 47 as session_end_reason, 48 as Device_Group_Hierarchy_l1, 49 as Device_Group_Hierarchy_l2, 50 as Device_Group_Hierarchy_l3, 51 as Device_Group_Hierarchy_l4, 52 as vsys_Name, 53 as DeviceName, 54 as action_source, 55 as Source_VM_UUID, 56 as Destination_VM_UUID, 57 as Tunnel_ID_IMSI, 58 as Monitor_Tag_IMEI, 59 as Parent_Session_ID, 60 as parent_start_time, 61 as Tunnel, 62 as SCTP_Association_ID, 63 as SCTP_Chunks, 64 as SCTP_Chunks_Sent, 65 as SCTP_Chunks_Received, 66 as UUIDforrule, 67 as HTTP2Connection, 68 as AppFlapCount ,69 as PolicyID ,70 as LinkSwitches ,71 as SDWANCluster ,72 as SDWANDeviceType ,73 as SDWANClusterType ,74 as SDWANSite ,75 as DynamicUserGroupName ,76 as XFFAddress ,77 as SourceDeviceCategory ,78 as SourceDeviceProfile ,79 as SourceDeviceModel ,80 as SourceDeviceVendor ,81 as SourceDeviceOSFamily ,82 as SourceDeviceOSVersion ,83 as SourceHostname ,84 as SourceMACAddress ,85 as DestinationDeviceCategory ,86 as DestinationDeviceProfile ,87 as DestinationDeviceModel ,88 as DestinationDeviceVendor ,89 as DestinationDeviceOSFamily ,90 as DestinationDeviceOSVersion ,91 as DestinationHostname ,92 as DestinationMACAddress ,93 as ContainerID ,94 as PODNamespace ,95 as PODName ,96 as SourceExternalDynamicList ,97 as DestinationExternalDynamicList ,98 as HostID ,99 as UserDeviceSerialNumber ,100 as SourceDynamicAddressGroup ,101 as DestinationDynamicAddressGroup ,102 as SessionOwner ,103 as HighResolutionTimestamp ,104 as ASliceServiceType ,105 as ASliceDifferentiator
| where type = "TRAFFIC" and action="allow"
| where ((compareCIDRPrefix("172.16.0.0", src_ip, toInt(12)) or compareCIDRPrefix("192.168.0.0", src_ip, toInt(16)) or compareCIDRPrefix("10.0.0.0", src_ip, toInt(8)) and (!compareCIDRPrefix("172.16.0.0", dest_ip, toInt(12)) and !compareCIDRPrefix("192.168.0.0", dest_ip, toInt(16)) and !compareCIDRPrefix("10.0.0.0", dest_ip, toInt(8)))) or
(compareCIDRPrefix("172.16.0.0", dest_ip, toInt(12)) or compareCIDRPrefix("192.168.0.0", dest_ip, toInt(16)) or compareCIDRPrefix("10.0.0.0", dest_ip, toInt(8)) and (!compareCIDRPrefix("172.16.0.0", src_ip, toInt(12)) and !compareCIDRPrefix("192.168.0.0", src_ip, toInt(16)) and !compareCIDRPrefix("10.0.0.0", src_ip, toInt(8)))))
| if (compareCIDRPrefix("172.16.0.0", src_ip, toInt(12)) or compareCIDRPrefix("192.168.0.0", src_ip, toInt(16)) or compareCIDRPrefix("10.0.0.0", src_ip, toInt(8)), "outbound","inbound") as direction
| timeslice 5m
| count by _timeslice, direction | transpose row _timeslice column direction
```



## Install the PCI for Palo Alto Networks 10 App and View the Dashboards

### Install the Sumo Logic App
10.gif "image_tooltip")


Now that you have set up collection for PCI for Palo Alto Networks 10 app, you can install app and  use the preconfigured searches and dashboards that provide insight into your data.


11.png "image_tooltip")
This app supports PAN-OS v10.

**To install the app:**

Locate and install the app you need from the **App Catalog**. If you want to see a preview of the dashboards included with the app before installing, click **Preview Dashboards**.



1. From the **App Catalog**, search for and select the app**.**
2. Select the version of the service you're using and click **Add to Library**.


12.png "image_tooltip")
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
13.gif "image_tooltip")



##### PCI Req 01 - Accepted and Rejected Traffic
14.gif "image_tooltip")


**Dashboard description:** See analytics about accepted and rejected firewall traffic, including information about traffic direction; allowed, denied, and silently dropped traffic over time; and the top source and destination IPs for allowed and rejected traffic.

**Use case:** You can use this dashboard to monitor allowed and rejected traffic in accordance with PCI requirement 1. The dashboard helps you identify systems accepting or rejecting traffic, and identify inbound and outbound network connections trends.


15.png "image_tooltip")



##### PCI Req 01 - Traffic Direction Monitoring
16.gif "image_tooltip")


**Dashboard description:** View information about inbound and outbound traffic, including traffic to the cardholder data environment (CDE); details of inbound and outbound traffic; the top 10 hosts sending traffic; and the top 10 hosts receiving traffic.

**Use case: **You can use this dashboard to monitor connections made from external IPs to internal IPs. You can identify top internal IPs receiving traffic from external IPs or sending traffic to external IPs. Similarly, this dashboard helps you identify the amount of traffic generated by different IPs crossing your organization's network boundary.


17.png "image_tooltip")



##### PCI Req 01, 02 - Data Access Monitoring
18.gif "image_tooltip")


**Dashboard description:** See information about accepted network traffic by application; top TCP and UDP source and destination ports; multi-services detected on the same host; and information about possible port scan attacks, both allowed and rejected.

**Use case:** You can use this dashboard to monitor activities on ports, apps being used, and the protocols used to access apps. The panels help you identify possible port scan attacks, both horizontal and vertical. You can also identify if there are systems on which multiple services are executing.


19.png "image_tooltip")



##### PCI Req 02, 04 - Insecure Data In Transit
20.gif "image_tooltip")


**Dashboard description:** See analytics about insecure data in transit data, including data transmissions to or from the cardholder data environment (CDE) using an insecure protocol; breakdowns of which apps, hosts, and ports were involved with insecure traffic.   

**Use case:** You can use this dashboard to identify systems and hosts involved in insecure data transit. You can identify insecure connections and port protocols like ftp, telnet, http, rlogin, and so on. You can also identify successful and rejected network connections for insecure connections.


21.png "image_tooltip")