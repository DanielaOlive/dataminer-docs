---
uid: General_Main_Release_10.3.0_CU4
---

# General Main Release 10.3.0 CU4 – Preview

> [!IMPORTANT]
> We are still working on this release. Some release notes may still be modified or moved to a later release. Check back soon for updates!

> [!TIP]
> For information on how to upgrade DataMiner, see [Upgrading a DataMiner Agent](xref:Upgrading_a_DataMiner_Agent).

### Enhancements

#### Cassandra Cluster Migrator tool now supports TLS [ID_34852]

<!-- MR 10.3.0 [CU4] - FR 10.3.7 -->

The Cassandra Cluster Migrator tool (*SLCCMigrator.exe*) is now able to establish TLS connections towards the databases.

#### Enhanced performance when retrieving resources [ID_36129]

<!-- MR 10.3.0 [CU4] - FR 10.3.7 -->

Because of a number of enhancements, overall performance has increased when retrieving resources.

#### Failover: Obsolete CheckVIPs thread has been removed [ID_36253]

<!-- MR 10.2.0 [CU16]/10.3.0 [CU4] - FR 10.3.7 -->

In Failover setups using virtual IP addresses, once every minute the CheckVIPs thread would check whether the online Agent holds the correct IP addresses. However, due to the many locks it claimed to verify the current state of the IP addresses, it would delay other actions being executed in the system.

This obsolete thread has now been removed.

#### Service & Resource Management: Enhanced logic to determine which function DVEs to deactivate [ID_36299]

<!-- MR 10.3.0 [CU4] - FR 10.3.7 -->

Up to now, when the function manager needed to deactivate function DVEs because the threshold was reached, it could do so for resources that were needed for bookings being started of which the status was not yet "ongoing".

From now on, function DVEs will no longer be deactivated when they are part of a booking that is either confirmed or ongoing with a start time (minus hysteresis) in the past and an end time in the future.

#### DataMiner tasks in Windows Task Scheduler will now return 0 instead of error code 1 [ID_36393]

<!-- MR 10.2.0 [CU16]/10.3.0 [CU4] - FR 10.3.7 -->

The following scheduled tasks will now by default return 0 instead of error code 1.

- Skyline DataMiner Backup (DataMinerBackup.js)
- Skyline DataMiner Database Optimization (OptimizeDB.js)
- Skyline DataMiner LDAP Resync (ReloadLDAP.js)

### Fixes

#### Service & Resource Management: Contributing resources of which the contributing booking had ended would not be marked available [ID_35757]

<!-- MR 10.3.0 [CU4] - FR 10.3.7 -->

When updating an ongoing main booking that made use of a contributing resource of which the contributing booking had already ended, the ResourceManager would incorrectly mark the contributing resource as unavailable. As a result, the update would fail and the main booking would go into quarantine with reason "ContributingResourceNotAvailable".

Also, a *GetEligibleResources* call would not return the contributing resource.

#### Business Intelligence: Secondary index of certain SLA logger tables would not be created correctly [ID_36245]

<!-- MR 10.2.0 [CU16]/10.3.0 [CU4] - FR 10.3.7 -->

The secondary index of certain SLA logger tables would not be created correctly. As a result, certain rows in those tables would not get cleaned up and exceptions like the following would be added to the *SLDBConnection.txt* log file:

```txt
SLDataGateway.Types.DBGatewayException: CassandraConnection ExecuteQuery - exception while executing query: SELECT "array_active_service_alarms_id","array_active_service_alarms_time" FROM ELEMENTDATA_7102_1334_750 WHERE "array_active_service_alarms_rootid" = '0/0'; - Cannot execute this query as it might involve data filtering and thus may have unpredictable performance. If you want to execute this query despite the performance unpredictability, use ALLOW FILTERING
```

When SLAs were stored in a Cassandra cluster, none of their rows would get cleaned up.

#### Service & Resource Management: Problem when updating a booking while actions were being performed [ID_36268]

<!-- MR 10.3.0 [CU4] - FR 10.3.7 -->

When a booking was updated while actions were being performed, in some cases, it could become corrupted.

For example, when a booking was rescheduled while it was being started, it could end up in an *Ongoing* status with a start time somewhere in the future.

#### Client connection would be dropped because an SLNet request handled by SLHelper took too long to process [ID_36296]

<!-- MR 10.3.0 [CU4] - FR 10.3.7 -->

When it took SLHelper more than 15 minutes to process an SLNet request received from a client, a NATS exception was returned to the client. However, as this exception was not serialized, the client would not receive it, causing the client connection to get dropped.

From now on, NATS exceptions returned to a client following an SLHelper timeout will always be serialized.

#### Protocols: QAction syntax errors did not refer to the correct lines [ID_36301]

<!-- MR 10.2.0 [CU15]/10.3.0 [CU4] - FR 10.3.7 -->

Up to now, before a QAction was compiled, three compiler directives were added to its source code. As a result, all compilation errors would refer to incorrect line numbers.

From now on, the compiler directives will no longer be added to the source code. Instead, they will be passed to the compiler directly.

#### SLNetClientTest: Problem when trying to set up a connection using gRPC [ID_36322]

<!-- MR 10.3.0 [CU4] - FR 10.3.7 -->

When the *SLNetClientTest* tool tried to set up a connection using gRPC, a `MissingMethodException` exception could be thrown.

> [!WARNING]
> Always be extremely careful when using this tool, as it can have far-reaching consequences on the functionality of your DataMiner System.

#### Exported DVE child protocols would no longer be set as production after re-uploading a main DVE protocol version used as production version [ID_36334]

<!-- MR 10.2.0 [CU16]/10.3.0 [CU4] - FR 10.3.7 -->

When you re-uploaded a main DVE protocol with the same version as the one that was being used as production version, the exported child protocols would incorrectly no longer be set as production.

#### Protocols: Setting the type of an advanced port to SNMPv3 would cause the advanced port settings to get lost [ID_36400]

<!-- MR 10.2.0 [CU16]/10.3.0 [CU4] - FR 10.3.7 -->

When, while editing an element using a (production) protocol with an advanced port of type SNMPv1 or SNMPv2, you set the type of the advanced port to SNMPv3, then the advanced port settings would get lost when the production version of the protocol was set to another version that also did not have SNMPv3 configured. Moreover, when you tried to correct the advanced port settings of the element, an error would occur in SLDataMiner as soon as you applied the changes.
