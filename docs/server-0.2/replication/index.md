---
layout: submenu
title: Replication
---

# Replication
OrigoDB supports multi-server replication using a single primary node and zero or more replicas in either standby or readonly mode. Commands are handled by the primary and synced with each replica over a tcp connection, either synchronously (default) or asynchronously. Read-only replicas handle queries while commands are rejected with a redirect message pointing at the current primary.

Replication can be used to

* Achieve high availability (HA) by having a synchronous standby instantly ready to take on the role of primary
* Spread read load across multiple replicas
* Maintain a backup at a remote location using asyncronous replication
* Bring data closer to clients using async or sync replication
* Take snapshots at an async replica to avoid blocking commands at the primary.

## Switchover
Switchover is when the primary and one of the replicas exchange roles. Switchover is performed manually using the web ui of either the primary or the replica. The transition is completed within milliseconds. Switchover allows system maintenance without downtime.

## Failover
Failover is the same as switchover except it being due to a failure at the at the primary. Promoting a replica to primary is performed manually using the web ui.

## Dynamic client reconfiguration
The native client has information about the current cluster configuration. In the event of a switchover, clients will be automatically redirected to the new primary. If a client loses connection with the primary it will attempt to reconnect to a replica.

## Readonly vs. Standby mode
A readonly replica will respond to queries and commands on the native interface. This is useful for distributing heavy read load across multiple servers. In standby mode the replicas native interface not active. Use standby mode to have a hot replica ready to instantly take on the role of primary. Standby mode requires no additional license.

## Sync vs. Async
Each replica can be configured with either synchronous or asynchronous replication. In sync mode the primary will wait for each replica to acknowledge each transaction. Sync mode provides 100% consistency and protection against data loss but adds latency
to each transaction.

In async mode, the primary puts commands in an internal queue and commits immediately, eliminating extra latency. The internal queue is processed by a background thread. Under load, async replicas will lag behind. If the primary were to crash the queued commands will never reach the replica. Promoting a replica to primary under this condition will result in lost transactions.

## Example use cases
There is no limit to the number of possible replicas. By using different configurations it's possible to achieve a number different goals. Here are some example use cases.

### Single Hot Standby
This is the normal high availability configuration. A primary and a single replica in standby sync mode both connected via gigabit LAN.

### Async read load scaling
A primary and one or more replicas in read only async mode for reads and one replica in read only sync mode for HA. The async replicas may lag behind under heavy load. How far they lag behind will depend on both the write load and the read load at each replica.

A search engine would be a good fit for this configuration. The eventual consistency won't be a problem, it means some users will be served slightly dated results. The crawler(s) might need a 100% consistent view. This can be achieved by directing their queries to the primary.

### Sync read load scaling
This is similar to the async read load scaling scenario but all the replicas are in sync. It won't scale as well because write throughput will diminish with increasing read load and for each replica added. Use this configuration when eventual consistency is unacceptable.

### Off site read replica
A replica in async read only mode connected over WAN/VPN can be used for analytics/reporting or to bring data closer to the user. Client applications can be configured to send commands to the primary and read from the local replica.

### Off site backup
A replica in async standby mode connected over LAN/WAN/VPN is a useful backup strategy.
