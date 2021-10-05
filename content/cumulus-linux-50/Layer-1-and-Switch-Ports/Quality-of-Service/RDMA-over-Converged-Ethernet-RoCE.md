---
title: RDMA over Converged Ethernet - RoCE
author: NVIDIA
weight: 322
toc: 3
---
[RoCE](## "RDMA over Converged Ethernet") enables you to write to compute or storage elements using [RDMA](## "Remote Direct Memory Access") over an Ethernet network instead of using host CPUs. RoCE relies on [ECN](## "Explicit Congestion Notification") and [PFC](## "Priority Flow Control") to operate. Cumulus Linux supports features that can enable lossless Ethernet for RoCE environments.

{{%notice note%}}
While Cumulus Linux can support RoCE environments, the end hosts must support the RoCE protocol.
{{%/notice%}}

RoCE helps you obtain a *converged network*, where all services run over the Ethernet infrastructure, including Infiniband apps.

## Enable RDMA over Converged Ethernet lossless (with PFC and ECN)

RoCE uses the Infiniband (IB) Protocol over converged Ethernet. The IB global route header rides directly on top of the Ethernet header. The lossless Ethernet layer handles congestion hop by hop.

To configure RoCE with PFC and ECN:

{{< tabs "roce lossless commands">}}
{{< tab "NCLU Commands">}}
```
cumulus@switch:~$ net add roce lossless
cumulus@switch:~$ net commit
```

{{< /tab >}}
{{< tab "NVUE Commands">}}

{{% notice note %}}
NVUE defaults to `roce mode lossless`. The command `nv set qos roce` and `nv set qos roce mode lossless` are equivalent.

If you enable `mode lossy`, configuring `nv set qos roce` without a `mode` does not change the RoCE mode. To change to lossless, you must configure `mode lossless`.
{{% /notice %}}

```
cumulus@switch:~$ nv set qos roce
cumulus@switch:~$ nv config apply
```
{{< /tab >}}
{{< /tabs >}}

{{%notice note%}}
{{<link url="Quality-of-Service#link-pause" text="Link pause">}} is another way to provide lossless ethernet; however, PFC is the preferred method. PFC allows more granular control by pausing the traffic flow for a given CoS group instead of the entire link.
{{%/notice%}}

## Enable RDMA over Converged Ethernet lossy (with ECN)

RoCEv2 requires flow control for lossless Ethernet. RoCEv2 uses the Infiniband (IB) Transport Protocol over UDP. The IB transport protocol includes an end-to-end reliable delivery mechanism and has its own sender notification mechanism.

RoCEv2 congestion management uses RFC 3168 to signal congestion experienced to the receiver. The receiver generates an RoCEv2 congestion notification packet directed to the source of the packet.

To configure RoCE with ECN:

{{< tabs "roce commands">}}
{{< tab "NCLU Commands">}}
```
cumulus@switch:~$ net add roce lossy
cumulus@switch:~$ net commit
```
{{< /tab >}}
{{< tab "NVUE Commands">}}
```
cumulus@switch:~$ nv set qos roce mode lossy
cumulus@switch:~$ nv config apply
```
{{< /tab >}}
{{< /tabs >}}

## Remove RoCE Configuration
To remove RoCE configurations:

{{< tabs "remove roce commands">}}
{{< tab "NCLU Commands">}}
```
cumulus@switch:~$ net del roce
cumulus@switch:~$ net commit
```
{{< /tab >}}
{{< tab "NVUE Commands">}}
```
cumulus@switch:~$ nv unset qos roce
cumulus@switch:~$ nv config apply
```
{{< /tab >}}
{{< /tabs >}}

## Verify RoCE Configuration

You can verify RoCE configuration with NVUE `nv show` commands.

To show detailed information about the configured buffers, utilization and DSCP markings, run the `nv show qos roce` command:

```
cumulus@switch:mgmt:~$ nv show qos roce
                    operational  applied   description
------------------  -----------  --------  ------------------------------------------------------
enable                           on        Turn the feature 'on' or 'off'.  The default is 'off'.
mode                lossless     lossless  Roce Mode
cable-length        100          100       Cable Length(in meters) for Roce Lossless Config
congestion-control
  congestion-mode   ECN                    Congestion config mode
  enabled-tc        0,3                    Congestion config enabled Traffic Class
  max-threshold     1.43 MB                Congestion config max-threshold
  min-threshold     146.48 KB              Congestion config min-threshold
pfc
  pfc-priority      3                      switch-prio on which PFC is enabled
  rx-enabled        enabled                PFC Rx Enabled status
  tx-enabled        enabled                PFC Tx Enabled status
trust
  trust-mode        pcp,dscp               Trust Setting on the port for packet classification
 
 
RoCE PCP/DSCP->SP mapping configurations
===========================================
        pcp  dscp                     switch-prio
    --  ---  -----------------------  -----------
    0   0    0,1,2,3,4,5,6,7          0
    1   1    8,9,10,11,12,13,14,15    1
    2   2    16,17,18,19,20,21,22,23  2
    3   3    24,25,26,27,28,29,30,31  3
    4   4    32,33,34,35,36,37,38,39  4
    5   5    40,41,42,43,44,45,46,47  5
    6   6    48,49,50,51,52,53,54,55  6
    7   7    56,57,58,59,60,61,62,63  7
 
 
RoCE SP->TC mapping and ETS configurations
=============================================
        switch-prio  traffic-class  scheduler-weight
    --  -----------  -------------  ----------------
    0   0            0              DWRR-50%
    1   1            0              DWRR-50%
    2   2            0              DWRR-50%
    3   3            3              DWRR-50%
    4   4            0              DWRR-50%
    5   5            0              DWRR-50%
    6   6            6              strict-priority
    7   7            0              DWRR-50%
 
 
RoCE pool config
===================
        name                   mode     size   switch-priorities  traffic-class
    --  ---------------------  -------  -----  -----------------  -------------
    0   lossy-default-ingress  Dynamic  50.0%  0,1,2,4,5,6,7      -
    1   roce-reserved-ingress  Dynamic  50.0%  3                  -
    2   lossy-default-egress   Dynamic  50.0%  -                  0,6
    3   roce-reserved-egress   Dynamic  inf    -                  3
 
 
Exception List
=================
        description
    --  -----------
```

To show detailed RoCE information about a single interface, run the `nv show interface qos roce status` command.

```
cumulus@switch:mgmt:~$ nv show interface swp16 qos roce status
                    operational    applied  description
------------------  -------------  -------  ---------------------------------------------------
congestion-control
  congestion-mode   ecn, absolute           Congestion config mode
  enabled-tc        0,3                     Congestion config enabled Traffic Class
  max-threshold     1.43 MB                 Congestion config max-threshold
  min-threshold     153.00 KB               Congestion config min-threshold
pfc
  pfc-priority      3                       switch-prio on which PFC is enabled
  rx-enabled        yes                     PFC Rx Enabled status
  tx-enabled        yes                     PFC Tx Enabled status
trust
  trust-mode        pcp,dscp                Trust Setting on the port for packet classification
mode                lossless                Roce Mode
 
 
RoCE PCP/DSCP->SP mapping configurations
===========================================
          pcp  dscp  switch-prio
    ----  ---  ----  -----------
    cnp   6    48    6
    roce  3    26    3
 
 
RoCE SP->TC mapping and ETS configurations
=============================================
          switch-prio  traffic-class  scheduler-weight
    ----  -----------  -------------  ----------------
    cnp   6            6              strict priority
    roce  3            3              dwrr-50%
 
 
RoCE Pool Status
===================
        name                   mode     pool-id  switch-priorities  traffic-class  size      current-usage  max-usage
    --  ---------------------  -------  -------  -----------------  -------------  --------  -------------  ---------
    0   lossy-default-ingress  DYNAMIC  2        0,1,2,4,5,6,7      -              15.16 MB  0 Bytes        16.00 MB
    1   roce-reserved-ingress  DYNAMIC  3        3                  -              15.16 MB  7.30 MB        7.90 MB
    2   lossy-default-egress   DYNAMIC  13       -                  0,6            15.16 MB  0 Bytes        16.01 MB
    3   roce-reserved-egress   DYNAMIC  14       -                  3              inf       7.29 MB        13.47 MB
```

To show detailed information about current buffer utilization as well as historic RoCE byte and packet counts, run the `nv show interface qos roce counters` command:

```
cumulus@switch:mgmt:~$ nv show interface swp16 qos roce counters
                               operational   applied  description
-----------------------------  ------------  -------  ------------------------------------------------------
rx-stats
  rx-non-roce-stats
    buffer-max-usage           144 Bytes              Max Ingress Pool-buffer usage for non-RoCE traffic
    buffer-usage               0 Bytes                Current Ingress Pool-buffer usage for non-RoCE traffic
    no-buffer-discard          55                     Rx buffer discards for non-RoCE traffic
    non-roce-bytes             56.52 MB               non-roce rx bytes
    non-roce-packets           462975                 non-roce rx packets
    pg-max-usage               144 Bytes              Max PG-buffer usage for non-RoCE traffic
    pg-usage                   0 Bytes                Current PG-buffer usage for non-RoCE traffic
  rx-pfc-stats
    pause-duration             0                      Rx PFC pause duration for RoCE traffic
    pause-packets              0                      Rx PFC pause packets for RoCE traffic
  rx-roce-stats
    buffer-max-usage           0 Bytes                Max Ingress Pool-buffer usage for RoCE traffic
    buffer-usage               0 Bytes                Current Ingress Pool-buffer usage for RoCE traffic
    no-buffer-discard          0                      Rx buffer discards for RoCE traffic
    pg-max-usage               0 Bytes                Max PG-buffer usage for RoCE traffic
    pg-usage                   0 Bytes                Current PG-buffer usage for RoCE traffic
    roce-bytes                 0 Bytes                Rx RoCE Bytes
    roce-packets               0                      Rx RoCE Packets
tx-stats
  tx-cnp-stats
    buffer-max-usage           16.02 MB               Max Egress Pool-buffer usage for CNP traffic
    buffer-usage               0 Bytes                Current Egress Pool-buffer usage for CNP traffic
    cnp-bytes                  0 Bytes                Tx CNP Packet Bytes
    cnp-packets                0                      Tx CNP Packets
    tc-max-usage               0 Bytes                Max TC-buffer usage for CNP traffic
    tc-usage                   0 Bytes                Current TC-buffer usage for CNP traffic
    unicast-no-buffer-discard  0                      Tx buffer discards for CNP traffic
  tx-ecn-stats
    ecn-marked-packets         693777677344           Tx ECN marked packets
  tx-pfc-stats
    pause-duration             0                      Tx PFC pause duration for RoCE traffic
    pause-packets              0                      Tx PFC pause packets for RoCE traffic
  tx-roce-stats
    buffer-max-usage           13.47 MB               Max Egress Pool-buffer usage for RoCE traffic
    buffer-usage               7.29 MB                Current Egress Pool-buffer usage for RoCE traffic
    roce-bytes                 92824.38 GB            Tx RoCE Packet bytes
    roce-packets               803785675319           Tx RoCE Packets
    tc-max-usage               16.02 MB               Max TC-buffer usage for RoCE traffic
    tc-usage                   7.29 MB                Current TC-buffer usage for RoCE traffic
    unicast-no-buffer-discard  663060754115           Tx buffer discards for RoCE traffic
```

## Related Information

- {{<exlink url="http://www.roceinitiative.org/roce-introduction/" text="RoCE introduction">}} - roceinitiative.org
- {{<exlink url="https://community.mellanox.com/s/article/understanding-rocev2-congestion-management" text="RoCEv2 congestion management">}} - community.mellanox.com