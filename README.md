# SolidFire Nagios Plugin

## Summary

This is a refreshed, Python 3-based version of an old SolidFire plugin for Nagios (written in Python 2). 

There's a lot of room for improvement because SolidFire has made alot of progress since this plugin was written, but I have no plans to make improvements at this time. The main purpose of this refresh is to provide a starting point to Nagios users who have NetApp HCI or SolidFire and NetApp partners who serve them.

If you're interested in other monitoring integrations for NetApp SolidFire or HCI please check out the monitoring section of [awesome-solidfire](https://github.com/scaleoutsean/awesome-solidfire).

## Instructions

- Run it with python3: `./checkSolidFire.py (MVIP|MIP) PORT USERNAME PASSWORD (MVIP|NODE)`
- Positional arguments:
  - MVIP or MIP: if checking cluster, provide Management Virtual IP, if node, provide node Management IP
  - PORT: 443 for cluster, 442 for node
  - USERNAME: cluster admin with reporting (or better) role. It is recommended to create a reporting role for this plugin.
  - PASSWORD: admin password
  - MVIP or NODE: "mvip" for SolidFire cluster, "node" for individual node

## Requirements

- Element OS v11+ (NetApp HCI & SolidFire)
- Python 3.6+

## Known Issues and Workarounds

- Usability: Max. iSCSI Session Count used to be calculated as `NumberOfEnsembleNodes * 700 * 90%`. Since Nagfire v2.1, that was changed to `NumberOfActiveNodesWithStorageRole -1) * 700 * 90%`:
  - Two storage node SolidFire clusters (new since earlier this year) still have 1-3 Ensemble Nodes, but only 1 may be able to provide them (if one node fails)
  - For larger clusters (3-10 nodes), one node is deducted from active node count so that in the case a node fails, your cluster doesn't end up maxed out in terms of iSCSI sessions, which is probably preferred to old behavior. On 11+ node clusters users would have a bit extra slack and early warning since we already use 90% of the supported maximum of 700
  - For two-node clusters, maxSessions (with 90% of a 700 session maximum) is 630. If a node fails, you'd end up with 0 active nodes and so this would trigger iSCSI alarms. Since we'd get them anyway (node down, etc.), the new formula makes sense
  - If you prefer the old behavior or if v2.1 gives you any other problems, please continue to use v2.0 or edit the script
- Security: HTTPS certificate validation is disabled. You may edit that out if you need validation to work.
- Security: since SolidFire v12 you can create a read-only cluster admin. It is strongly recommended to create a 'nagios' account for this application (with only Read role) on SolidFire cluster
- Usability: various formulae haven't been updated since v1.7 (Element OS v5). Tested limits have generally increased since v5.

## Sample CLI Output

- Python 3.6.9 and Element OS: v11.3 (Element Demo VM, singleton node)

```shell
$ python3 checkSolidFire.py 192.168.1.30 443 admin admin mvip
/tmp/cluster-192.168.1.30.txt
+---------------------------------------------------------------+
| SolidFire Monitoring Plugin v2.0 2020/01/31                   |
+---------------------------------------------------------------+
| Cluster                        | 192.168.1.30                 |
| Version                        | 11.3.0.14235                 |
| Disk Activity                  | No*                          |
| Read Bytes                     | 114359578112                 |
| Write Bytes                    | 309075653120                 |
| Utilization %                  | 0.0                          |
| iSCSI Sessions                 | 2                            |
| Cluster Faults                 | 2019-12-11T13:43:33 The sum  |
|                                | of all minimum QoS IOPS      |
|                                | (6500) is greater than the   |
|                                | expected IOPS (3000) of the  |
|                                | cluster. The minimum QoS can |
|                                | not be maintained for all    |
|                                | volumes simultaneously in    |
|                                | this condition. Adjust QoS   |
|                                | settings on one or more      |
|                                | volumes to not exceed        |
|                                | available cluster IOPS.*     |
| Cluster Name                   | taiwan                       |
| Ensemble Members               | [192.168.103.29]             |
| Execution Time                 | Fri Jan 31 13:13:30 2020     |
| Exit State                     | *Critical                    |
+---------------------------------------------------------------+

$ python3 checkSolidFire.py 192.168.1.29 442 admin admin node
+---------------------------------------------------------------+
| SolidFire Monitoring Plugin v2.0 2020/01/31                   |
+---------------------------------------------------------------+
| Node Status                    | Active                       |
| Cluster Name                   | taiwan                       |
| MVIP                           | 192.168.1.30                 |
| Execution Time                 | Fri Jan 31 13:13:34 2020     |
| Exit State                     | OK                           |
+---------------------------------------------------------------+
```

## Change Log

- v2.1 (2020/09/26)
  - Change formula for max iSCSI sessions to increase HA and reliability for 2-10 node clusters

- v2.0 (2020/01/31)
  - Fix node checks
  - Silence shell warnings from unverified HTTPS connections
  - Tiny formatting change for console output

- v2.0b (2020/01/31)
  - Port v1.17 to Python 3
  - Replace urllib with requests
  - Use Element API endpoint v11
  - Lower maximum session count (needs validation, may need to be increased for larger clusters)

## License and Trademarks

See the LICENSE file.

NetApp, SolidFire, and the marks listed at www.netapp.com/TM are trademarks of NetApp, Inc.
