# SolidFire Nagios Plugin

## Summary

This is a refreshed, Python 3-based version of an old SolidFire plugin for Nagios (written in Python 2). 

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

## Known Issues

- Security: HTTPS certificate validation is disabled. You may edit that out if you need validation to work.
- Interface: HTTPS certificate validation creates warnings in console output. 
- Usability: various formulae haven't been updated since v1.7 (Element OS v5). 

## Sample CLI Output

- Python 3.6.9 and Element OS: v11.3 (Element Demo VM, singleton node)

```shell
$ python3 checkSolidFire.py 192.168.1.30 443 admin admin mvip
/tmp/cluster-192.168.1.30.txt
+----------------------------------------------------------------+
| SolidFire Monitoring Plugin v2.0b 2020/01/31                  |
+----------------------------------------------------------------+
| Cluster                        | 192.168.1.30                 |
| Version                        | 11.3.0.14235                 |
| Disk Activity                  | Yes                          |
| Read Bytes                     | 114344324608                 |
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
| Execution Time                 | Fri Jan 31 02:14:34 2020     |
| Exit State                     | *Critical                    |
+---------------------------------------------------------------+
```

## Change Log

- v2.0b (2020/01/31)
  - Port v1.17 to Python 3
  - Replace urllib with requests
  - Use Element API endpoint v11
  - Lower maximum session count (needs validation, may need to be increased for larger clusters)

## License

See LICENSE file.

