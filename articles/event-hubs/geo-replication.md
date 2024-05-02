 
# Geo replication (Public Preview)
 
There are two features that provide Geo-disaster recovery in Azure Event Hubs. There is Geo-disaster recovery (Metadata DR) that just provides replication of metadata and then a second feature, in public preview, Geo replication that provides replication of both metadata and the data itself. Neither geo-disaster recovery feature should be confused with Availability Zones. Regardless of if it is Metadata DR or Geo replication, both geo-graphic recovery features provide resilience between Azure regions such as East US and West US.  Availability Zone support provides resilience within a specific geographic region, such as East US. For more details on Availability Zones, please read the documentation here: Event Hubs Availability Zone support.
**High level feature differences**
The Metadata DR feature replicates configuration information for a namespace from a primary namespace to a secondary namespace.  It supports a one time only failover to the secondary region. During customer initiated failover, the alias name for the namespace is repointed to the secondary namespace and then the pairing is broken.  No data is replicated other than configuration information nor are RBAC assignments replicated.  
The Geo replication feature replicates configuration information and all of the data from a primary namespace to one or more secondary namespaces.  When a failover is performed by the customer, the selected secondary becomes the primary and the previous primary becomes a secondary. Users can perform a failover back to the original primary when desired. 
This rest of this document is focused on the Geo replication feature.  For details on the metadata DR feature, please read Event Hubs Geo-disaster recovery for metadata. 

## Geo replication 
The public preview of the Geo replication feature will initially only be supported for namespaces in Event Hubs self-serve scaling Dedicated clusters.  You can use the feature with existing namespaces though your namespaces can't have the following features enabled:
-	Large messages support (just now going to public preview)
-	VNet features (service endpoints or private endpoints)
-	Customer Managed Keys (CMK)
-	Managed Identity for Capture
 
Geo replication is initially only enabled in a subset of smaller regions. Additional regions will be enabled in the coming months.  Some of the key aspects of Geo Data Replication Public Preview are: 
-	Primary-secondary replication model – Geo replication is built on primary-secondary replication model, where at a given time there’s only one Primary namespace which is serving both event producers and event consumers. 
-	Event Hubs services perform fully managed byte-to-byte replication of metadata, event data and consumer offset across geo replicas adhering to the consistency levels configured at the namespace. 
-	Stable namespace FQDN – Upon successful configuration of a Geo replication enabled namespace, users can use the namespace FQDN in their client application and that is completely agnostic of the Geo replication regions and topology. 
-	Replication consistency – async or sync
-	User-managed failovers from primary to secondary region. 
The Geo replication feature replicates all data and metadata from the primary region to the selected secondary regions.  The namespace FQDN always points to the primary region.  

[img](../media/geo-replication/replication-a.png)
  
When a customer initiates a failover, the FQDN points to the region selected to be the new primary.  The old primary then becomes a secondary.    

[img](../media/geo-replication/replication-b.png)
 
Secondary regions can be added or removed at the customer's discretion. 
There are some current limitations worth noting:
-	There is no ability to support read-only views on secondary regions 
-	There is no automatic failover capability.  All failovers are customer initiated.
-	Secondary regions must be different from the primary region. You can't select another dedicated cluster in the same region.
-	Only one secondary is supported for public preview
 
## Replication Mode
There are two replication modes, synchronous and asynchronous.  It is very important to know the differences between the two modes and for almost all use cases, asynchronous replication should be used.  

**Asynchronous replication**
With asynchronous replication enabled, all messages are committed in the primary and then sent onwards to the secondary.  Users can configure an acceptable amount of lag time that the secondary has to catch-up.  If the lag for an active secondary grows beyond user configuration, the primary will throttle incoming publish requests.  

**Synchronous replication**
When synchronous replication is enabled, published events are replicated to the secondary which must confirm the message before it is committed in the primary. This means your application publishes at the rate it takes to publish, replicate, acknowledge and commit.  It also means that your application is tied to the availability of both regions.  If the secondary region goes down, messages will not be able to be acknowledged and committed.  

**Replication mode comparison**
With synchronous replication:
-	Latency is longer due to the distributed commit
- Availability is tied to the availability of two regions

On the other hand, synchronous replication provides the greatest assurance that your data is safe.  If you have synchronous replication, then when we commit it, then it is committed in all of the regions you have configured for Geo replication. It provides the best data assurance and reliability.
Enabling asynchronous replication doesn't have much impact on latency, and service availability isn't impacted by the loss of a secondary region. .  It doesn’t have the absolute guarantee that all regions have the data before we commit it like synchronous replication does.
Replication mode can be changed after Geo replication has been configured. You can go from synchronous to asynchronous or from asynchronous to synchronous.  If you go from synchronous to asynchronous, your latency, availability and reliability will improve.  If you go from asynchronous to synchronous, your secondary will be configured as synchronous after lag reaches zero. If you are running with a continual lag for whatever reason, then you may need to pause your publishers in order for lag to reach zero and your mode to be able to switch to synchronous.  
The reasons to have synchronous replication enabled, instead of asynchronous replication, are tied to the importance of the data, specific business needs or compliance reasons rather than availability and reliability of your application. 

## Secondary region selection
To enable the Geo replication feature you need to use a primary and secondary region where the Geo replication feature is enabled.  You need to have an Event Hubs cluster already existing in both the primary and secondary regions.  Both Event Hubs clusters should be scaled to the same number of Capacity Units (CUs).  
The Geo replication feature depends on being able to replicate published events from the primary to the secondary region. If the secondary region is on another continent, it will have a major impact on replication lag from the primary to the secondary region.  If using Geo replication for availability and reliability reasons, you are best off with secondary regions being at least on the same continent where possible. To get a better understanding of the latency induced by geographic distance you can learn more from Azure network round-trip latency statistics | Microsoft Learn.
Geo replication management
The Geo replication feature enables customers to configure a secondary region to replicate configuration and data to.  Customers can:
•	Configure Geo replication- Secondary regions can be configured on any existing namespace in a self-serve dedicated cluster in a region with the Geo replication feature set enabled. It can also be configured on the same dedicated clusters during namespace creation. To select a secondary region, you must have a dedicated cluster available in that secondary region and the secondary region also must have the Geo replication feature set enabled.  
•	Configure the replication consistency - Synchronous and asynchronous replication is set when Geo replication is configured but can also be switched afterwards.
•	Trigger failover - All failovers are customer initiated.  
•	Remove a secondary - If at any time you want to remove the geo-pairing between primary and secondary regions, you can do so and the data in the secondary region will be deleted.  
 
## Monitoring data replication
Users can monitor the progress of the replication job by monitoring the replication lag metric in Application Metrics logs.
-	Enable Application Metrics logs in your Event Hubs namespace following Monitoring Azure Event Hubs - Azure Event Hubs | Microsoft Learn. 
-	Once Application Metrics logs are enabled, you need to produce and consume data from namespace for a few minutes before you start to see the logs. 
-	To view Application Metrics logs, navigate to Monitoring section of Event Hubs and click on the ‘Logs’ blade. You can use the following query to find the replication lag (in seconds) between the primary and secondary namespaces. 
o	AzureDiagnostics
| where TimeGenerated > ago(1h)
| where Category == "ApplicationMetricsLogs"
| where ActivityName_s == "ReplicationLag
-	The column count_d indicates the replication lag in seconds between the primary and secondary region.
  
[img](../media/geo-replication/replication-monitoring.png)
 
 ## Publishing Data 
Event publishing applications can publish data to geo replicated namespaces via stable namespace FQDN of the geo replicated namespace. The event publishing approach is the same as the non-Geo DR case and no changes to data plane SDKs or Kafka client applications are required. 
Event publishing may not be available during the following circumstances: 
-	During Failover grace period, the existing primary region rejects any new events that are published to Event Hubs.  
-	When replication lag between primary and secondary regions reaches the max replication lag duration, the publisher ingress workload may get throttled. 
Publisher applications cannot directly access any namespaces in the secondary regions. 

**Consuming Data**
Event consuming applications can consume data using the stable namespace FQDN of a geo replicated namespace. The consumer operations are not supported, from when the failover is initiated until it is completed. 

### Checkpointing/Offset Management
Event consuming applications can continue to maintain offset management as they would do it with a single namespace. 

**Kafka**
Offset are committed to Event Hubs directly and offsets are replicated across regions. Therefore, consumers can start consuming from where it left off in the primary region. 

**Event Hubs SDK/AMQP**
Clients that use the Event Hubs SDK need to upgrade to the April 2024 version of the SDK.  The latest version of the Event Hubs SDK supports failover with an update to the checkpoint.  The checkpoint is managed by users with a checkpoint store such as Azure Blob storage or a custom storage solution.  If there is a failover, the checkpoint store must be available from the secondary region so that clients can retrieve checkpoint data and avoid loss of messages.

