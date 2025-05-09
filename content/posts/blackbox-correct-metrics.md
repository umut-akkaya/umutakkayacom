---
title: 'Blackbox Correct Metrics'
date: 2025-05-10T00:49:28+02:00
draft: false
type: 'page'
author: Umut AKKAYA
---
# Up Does NOT Mean The Site is OK

When you setup blackbox exporter you might think metric `up` can be used to detect website failure. However `up` only corresponds the job entry of blackbox exporter not he job itself. It means job is alive and running. You should use `probe_success` to determine the module check fails or succeeds. You set up following alert for Alertmanager.

```shell
probe_success{job="blackbox-exporter"} == 0
```