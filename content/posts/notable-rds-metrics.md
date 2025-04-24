+++
title = 'Notable Rds Metrics'
date = 2025-04-24T18:05:27+02:00
draft = false
+++
# Notable RDS Cloudwatch Metrics

Cloudwatch is AWS managed monitoring solution which provides insights about Amazon Services. For RDS it provides multiple metrics. Some of the notable metrics can be used for troubleshooting for EBS;
- `DiskQueueDepth`: This metric gives information about IOPS operations. It is better to keep it close to 1 for 1000 IOPS. If your EBS has 4000 IOPS that means this value can be up to 4. More of them means other programs are using the EBS store block all of the availables IOPS.Then your request must wait in queue.
- `ReadIOPS`: How many IOPS are used for reading
- `WriteIOPS`: How many IOPS are used for writing
- `TotalIOPS`: Basically sum of ReadIOPS and WriteIOPS. You can use thii to measure your IOPS limits in your EBS
- `ReadThroughput`: Total Read Throughput. It is also important to check throghput alogside with IOPS. You can check your total throghput in your RDS console.
- `WriteThroughput`: Total Write Throughput.

In RDS there are other important metrics which can be used in many scenarios.