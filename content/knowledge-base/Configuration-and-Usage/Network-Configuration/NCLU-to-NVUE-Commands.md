---
title: NCLU to NVUE Common Commands
weight: 300
---

Cumulus Linux version 4.4 introduces a new CLI called {{<kb_link latest="cl" url="System-Configuration/NVIDIA-User-Experience-NVUE.md" text="NVUE">}}. NVUE is a complete object model for Cumulus Linux, which makes translating configurations from one vendor to another much more reliable the first time you use Cumulus Linux and across Cumulus Linux versions.

This KB article describes how to translate common NCLU configurations to NVUE commands and bring you up to speed in using NVUE.

## Hostname and System

<table>
    <tr>
        <th>
        NCLU Command
        </th>
        <th>
        NVUE Command
        </th>
        <th>
        Comments
        </th>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add hostname &lt;hostname&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set platform hostname &lt;hostname&gt;</code>
        </td>
        <td>
        </td>       
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add syslog host [ipv4|ipv6] &lt;ip&gt; port [tcp|udp] &lt;port&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set service syslog default server &lt;ip&gt;</code>
        </td>
        <td rowspan="2" style="vertical-align : middle">
        The value <code>default</code> is the VRF the server is in.
        </td>      
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add time ntp server &lt;ip&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set service ntp default server &lt;ip&gt;</code>
        </td>   
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net pending</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv config diff empty pending</code>
        </td>   
        <td>
        NVUE can compare between configuration types. </br> When any config type compared to <code>empty</code>, it shows only the type being compared.
        </td>   
    </tr>
    <tr>
        <td rowspan="2" style="vertical-align : middle">
        <code>net commit</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv config apply</code>
        </td>   
        <td rowspan="2" style="vertical-align : middle">
        In NCLU, the running-config equals the startup-config. </br>NVUE separates running and startup configs: </br> <code>nv config apply</code> - apply configuration as running (without saving as startup) </br> <code>nv config save</code> - save configuration as startup (without applying as running)
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv config save</code>
        </td>
    </tr>
    <tr>
        <td rowspan="3" style="vertical-align : middle">
        <code>net show configuration</code>
        </td>
        <td style="vertical-align : middle">
        <code>cat /etc/nvue.d/startup.yaml</code>
        </td>   
        <td rowspan="3" style="vertical-align : middle">
        By using the <code>net show configuration commands</code>, you can view the configuration as actual NCLU commands.</br> NVUE is based on a single startup-config YAML file, you can view it using Linux <code>cat</code> command or using NVUE commands.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv config diff empty startup</code>
        </td>   
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv config diff empty applied</code>
        </td>   
    </tr>
</table>


## Interfaces

<table>
    <tr>
        <th>
        NCLU Command
        </th>
        <th>
        NVUE Command
        </th>
        <th>
        Comments
        </th>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; </code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; type &lt;interface-type&gt;</code>
        </td>
        <td style="vertical-align : middle">
        NVUE allows to create any interface-type under the interface object. </br>NCLU is not an object model, so this command only allows to create &lt;swp&gt; interface-type. For other interface-types, you need to use different commands.
        </td>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; [ipv4|ipv6] address &lt;ip/mask&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; ip address &lt;ip/mask&gt;</code>
        </td>
        <td style="vertical-align : middle">
        In NVUE, you set IPv4 and IPv6 addresses with the same command. </br> To configure secondary IP address, use the same command.</br>To replace existing IP address, delete it first using: </br> NCLU - <code>net del interface &lt;interface&gt; [ipv4|ipv6] address &lt;ip/mask&gt;</code></br>NVUE - <code>nv unset interface &lt;interface&gt; ip address &lt;ip/mask&gt;</code>
        </td>      
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; mtu &lt;mtu&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; link mtu &lt;mtu&gt;</code>
        </td>
        <td style="vertical-align : middle">
        The dafault MTU in Cumulus Linux is 9216B.
        </td>   
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; link speed &lt;speed&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; link speed &lt;speed&gt;</code>
        </td>
        <td>
        </td>  
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; link fec &lt;fec-mode&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; link fec &lt;fec-mode&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td>  
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net [add|del] interface &lt;interface&gt; link down &lt;fec-mode&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; link state [up|down]</code>
        </td>
        <td style="vertical-align : middle">
        The dafault state for interfaces is UP. 
        </td> 
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add loopback lo</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface lo</code>
        </td>
        <td style="vertical-align : middle">
        The loopback interface in Cumulus Linux called "lo". 
        </td> 
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; breakout &lt;breakout-option&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; link breakout &lt;breakout-option&gt;</code>
        </td>
        <td>
        Multiple breakout options exist. To veiw all options run:</br> NVUE - <code>nv set interface &lt;interface&gt; link breakout -h</code></br> NCLU - <code>net add interface &lt;interface&gt; breakout &lt;press TAB&gt;
        </td>   
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; alias &lt;description-text&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; alias &lt;description-text&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td> 
    </tr>
</table>

## Bonds and Port Channels

Linux uses the term `bond` to represent `port-channels`.

<table>
    <tr>
        <th>
        NCLU Command
        </th>
        <th>
        NVUE Command
        </th>
        <th>
        Comments
        </th>
    </tr>
    <tr>
        <td rowspan="2" style="vertical-align : middle">
        <code>net add bond &lt;bond-name&gt; bond slaves &lt;interfaces&gt; </code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;bond-name&gt; bond memeber &lt;interfaces&gt;</code>
        </td>
        <td rowspan="2" style="vertical-align : middle">
        In NCLU, bond is created by enslaving ports to it (or seetting <code>bond mode</code>). You create bond and add memebers in a sinble command.</br>In NVUE, you can create bond in the same way or without adding memebers by creating it using the <code>type bond</code> interface command. In addition, by naming bond interface as "bond...", its type will be defined automatically. e.g. <code>nv set interface bond1</code></br>NOTE: You define bonds with a name that must start with a letter. 
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;bond-name&gt; type bond</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bond &lt;bond-name&gt; bond mode balance-xor</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;bond-name&gt; bond mode static</code>
        </td>
        <td style="vertical-align : middle">
        The default bond mode in Cumulus LInux is <code>lacp</code> (<code>802.3ad</code> in NCLU).
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bond &lt;bond-name&gt; bond lacp-rate slow</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;bond-name&gt; bond lacp-rate slow</code>
        </td>
        <td style="vertical-align : middle">
        The default bond lacp-rate in Cumulus LInux is <code>fast</code>.
        </td>
    </tr>
</table>

## Layer 2 and VLANs

Cumulus Linux interfaces are layer 3 routed interfaces by default. To make an interface a layer 2 switchport, you must add the interface to the default bridge called `bridge` when using NCLU or `br_default` in NVUE:

{{< tabs "bridge commands ">}}
{{< tab "NCLU">}}
```
cumulus@switch:~$ net add bridge bridge ports <interface>
```
{{</ tab >}}
{{< tab "NVUE">}}
```
cumulus@switch:~$ nv set interface <interface> bridge domain br_default
```
{{</ tab >}}
{{</ tabs >}}

<table>
    <tr>
        <th>
        NCLU Command
        </th>
        <th>
        NVUE Command
        </th>
        <th>
        Comments
        </th>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; bridge access &lt;vlan-id&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default access &lt;vlan-id&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bridge bridge ports &lt;interface&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default</code>
        </td>
        <td style="vertical-align : middle">
        Ports you add to a bridge are trunk ports by default (all vlans allowed).
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add inerface &lt;interface&gt; bridge trunk vlans &lt;vlan-id|vlan-list&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default vlan &lt;vlan-id|vlan-list|all&gt;</code>
        </td>
        <td style="vertical-align : middle">
        To allow all vlans on the trunk port: </br> NCLU - <code>net add inerface &lt;interface&gt; bridge trunk</code> </br> NVUE - <code>nv set interface &lt;interface&gt; bridge domain br_default</code>  
        </td>
    </tr>
        <td style="vertical-align : middle">
        <code>net add inerface &lt;interface&gt; stp portadminedge</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default stp admin-edge on</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    </tr>
        <td style="vertical-align : middle">
        <code>net add inerface &lt;interface&gt; stp portnetwork</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default stp network on</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    </tr>
        <td style="vertical-align : middle">
        <code>net add inerface &lt;interface&gt; stp bpduguard</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default stp bpdu-guard on</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    </tr>
        <td style="vertical-align : middle">
        <code>net add inerface &lt;interface&gt; stp portbpdufilter</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default stp bpdu-filter on</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    </tr>
        <td style="vertical-align : middle">
        <code>net add bridge stp treeprio &lt;stp-priority&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set bridge domain br_default stp priority &lt;stp-priority&gt;</code>
        </td>
        <td style="vertical-align : middle">
        Cumulus Linux only supports {{<kb_link latest="cl" url="Layer-2/Spanning-Tree-and-Rapid-Spanning-Tree.md" text="RSTP." >}}
        </td>
    </tr>
</table>

## MLAG

In MLAG configuration, Cumulus Linux uses *peer link* (bond between peers) to sync the MLAG pair. To keep MLAG pairs in sync when a direct connection fails, Cumulus Linux uses *mlag backup IP*.

For more information about MLAG, refer to the {{<kb_link latest="cl" url="Layer-2/Multi-Chassis-Link-Aggregation-MLAG.md" text="Multi-Chassis Link Aggregation - MLAG" >}} page on the Cumulus Linux User Guide.

<table>
    </tr>
        <td style="vertical-align : middle">
        <code>net add bond &lt;bond-name&gt; clag id &lt;number&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;bond-name&gt; bond mlag id &lt;number|auto&gt;</code>
        </td>
        <td style="vertical-align : middle">
        You should create bond interfaces prior setting mlag id. The <code>mlag id</code> must match the bond interface on both MLAG peers connected to the same host. Using <code>&lt;auto&gt;</code> determines the ID based on the the MAC address of the end host.
        </td>
    </tr>
    </tr>
        <td rowspan="4" style="vertical-align : middle">
        <code>net add clag peer sys-mac &lt;mac&gt; interface &lt;peerlink-members&gt; [primary|secondary] backup-ip &lt;ip&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set mlag mac-address &lt;mac|auto&gt;</code>
        </td>
        <td rowspan="4" style="vertical-align : middle">
        NCLU MLAG configuration requires to set all paramaters in a single command where the <code>backup-ip &lt;ip&gt;</code> is optional.</br>NVUE MLAG configurat6ion separated into several commands, it allows easier changes to MLAG global parameters. It also supports <code>&lt;auto&gt;</code> MAC address generation.</br>Cumulus Linux requires a unique bond for the peerlink and an associated <code>peer-ip</code> definition.   
        </td>
    </tr>
        <td style="vertical-align : middle">
        <code>nv set interface peerlink bond member &lt;interfaces&gt;</code>
        </td>
    </tr>
    </tr>
        <td style="vertical-align : middle">
        <code>nv set mlag peer-ip linklocal</code>
        </td>
    </tr>
    </tr>
        <td style="vertical-align : middle">
        <code>nv set mlag backup &lt;ip&gt;</code>
        </td>
    </tr>
</table>

## Layer 3 Routing Protocols

Most NVUE BGP commands require the VRF to be included in the command. The examples below include the `default` VRF name since it is pre-defined in the system, but any VRF name can be used. You can still configure some of the global BGP parameters (enable/disable BGP, set the ASN and the router ID, configure BGP graceful restart, and shutdown the router) without specifying a VRF. Then all VRFs enherit these settings automatically unless you set specific settings on the VRF.</br> In NCLU, by default, all configuration is done globally on the system's default VRF, unless other VRF specified (NCLU doesn't have pre-defined VRF named `default`). Custom VRFs don't enherit the global BGP settings and must be configured separately.

<table>
    <tr>
        <th>
        NCLU Command
        </th>
        <th>
        NVUE Command
        </th>
        <th>
        Comments
        </th>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add &lt;interface&gt; vrf &lt;name&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; ip vrf &lt;name&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp [vrf &lt;name&gt;] autonomous-system &lt;leaf|spine|ASN&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set [vrf &lt;default|name&gt;] router bgp autonomous-system &lt;leaf|spine|none|ASN&gt;</code>
        </td>
        <td style="vertical-align : middle">
        In NVUE, when a single AS is in use for all VRFs, the <code>[vrf &lt;name&gt;]</code> not required to create the instance. Its settings will be automatically applied to all VRFs including the <code>default</code> VRF.</br> If <code>&lt;none&gt;</code> ASN option used globally, then ASN must be set for every VRF.</br>For more information about the <code>&lt;leaf|spine&gt;</code> options, check out {{<kb_link latest="cl" url="layer-3/Border-Gateway-Protocol-BGP/#auto-bgp" text="Auto BGP" >}} section in the Cumulus Linux User Guide.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp [vrf &lt;name&gt;] router-id &lt;ipv4&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set [vrf &lt;default|name&gt;] router bgp router-id &lt;ipv4&gt;</code>
        </td>
        <td style="vertical-align : middle">
        In NVUE, when a single AS is in use for all VRFs, the <code>&lt;vrf &lt;name&gt;&gt;</code> not required in the command. It will be gloabally applied to all VRFs including the <code>default</code> VRF.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp [vrf &lt;name&gt;] neighbor &lt;ip|interface&gt; remote-as &lt;internal|external|ASN&gt; </code>
        </td>
        <td style="vertical-align : middle">
        <code> set vrf &lt;default|name&gt; router bgp peer &lt;ip|interface&gt; remote-as &lt;internal|external|ASN&gt;</code>
        </td>
        <td style="vertical-align : middle">
        NVUE requires to specify a VRF when adding BGP peer. Cumulus Linux supports {{<kb_link latest="cl" url="layer-3/Border-Gateway-Protocol-BGP/#bgp-unnumbered" text="BGP Unnambered" >}} peer configuration.</br>The ASN can be a number, or <code>internal</code> for a neighbor in the same AS or <code>external</code> for a neighbor in a different AS.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp [vrf &lt;name&gt;] neighbor &lt;name&gt; peer-group &lt;attributes&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;default|name&gt; router bgp peer-group &lt;name&gt; &lt;attributes&gt;</code>
        </td>
        <td style="vertical-align : middle">
        NCLU requires to create peer-group with </br><code>net add bgp &lt;vrf &lt;name&gt;&gt; neighbor &lt;name&gt; peer-group</code> prior configuring its attributes.</br>NVUE allows you to create peer-group and set its attributes in a single command.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp [vrf &lt;name&gt;] [ipv4|ipv6] unicast network &lt;ipv4|ipv6/mask&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;default|name&gt; router bgp address-family &lt;ipv4-unicast|ipv6-unicast&gt; static-network &lt;ipv4|ipv6/mask&gt;</code>
        </td>
        <td style="vertical-align : middle">
        In NCLU, address-family is optional and can be determined by the type of the ip-address advertised.</br>In NVUE, you must specify the address-family to advertise network into it.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp [vrf &lt;name&gt;] &lt;ipv4|ipv6&gt; unicast redistribute &lt;connected|static|ospf|kernel&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;default|name&gt; router bgp address-family &lt;ipv4-unicast|ipv6-unicast&gt; redistribute &lt;connected|static|ospf|kernel&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    <tr>
        <td rowspan="2" style="vertical-align : middle">
        <code>net add routing prefix-list &lt;ipv4|ipv6&gt; &lt;name&gt; seq &lt;seq&gt; &lt;permit|deny&gt; &lt;ipv4|ipv6/lenght|any&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set router policy prefix-list &lt;name&gt; rule &lt;seq&gt; action &lt;permit|deny&gt;</code>
        </td>
        <td rowspan="2" style="vertical-align : middle">
        NCLU allows to configure prefix-list action and match in a single command. </br>NVUE does it in two commands. The default prefix-list type in NVUE is IPv4. But, you can set IPv6 prefix-list using the <code>nv set router policy prefix-list &lt;name&gt; type ipv6</code> command. 
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv set router policy prefix-list &lt;name&gt; rule &lt;seq&gt; match &lt;prefix/lenght&gt;</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp [vrf &lt;name&gt;] [ipv4|ipv6] neighbor &lt;ip|interface&gt; prefix-list &lt;name&gt; &lt;in|out&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;default|name&gt; router bgp peer &lt;ip|interface&gt; address-family &lt;ipv4-unicast|ipv6-unicast&gt; policy &lt;inbound|outbound&gt; prefix-list &lt;name&gt;</code>
        </td>
        <td style="vertical-align : middle">
        In NCLU, when no address-family specified, the default is IPv4-unicast.
        </td>
    </tr>
    <tr>
        <td rowspan="2" style="vertical-align : middle">
        <code>net add routing route-map &lt;name&gt; &lt;permit|deny&gt; &lt;seq&gt; match ip address prefix-list &lt;name&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set router policy route-map &lt;name&gt; rule &lt;seq&gt; action &lt;permit|deny&gt;</code>
        </td>
        <td rowspan="2" style="vertical-align : middle">
        NCLU allows to configure route-map action and match in a single command. NVUE does it with two commands. 
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv set router policy route-map &lt;name&gt; rule &lt;seq&gt; match ip-prefix-list &lt;name&gt;</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp [vrf &lt;name&gt;] [ipv4|ipv6|evpn] neighbor &lt;ip|interface&gt; route-map &lt;name&gt; &lt;in|out&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;default|name&gt; router bgp peer &lt;ip|interface&gt; address-family &lt;ipv4-unicast|ipv6-unicast|l2vpn-evpn&gt; policy &lt;inbound|outbound&gt; route-map &lt;name&gt;</code>
        </td>
        <td style="vertical-align : middle">
        In NCLU, when no address-family specified, the default is IPv4-unicast.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add routing route &lt;ipv4|ipv6/mask&gt; &lt;next-hop|interface&gt; [vrf &lt;name&gt;] </code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;default|name&gt; router static &lt;route&gt; via &lt;next-hop&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
</table>

## VXLAN and EVPN 

Cumulus Linux supports both single and traditional VXLAN devices. With a traditional VXLAN device, each VNI is a separate device (for example, vni10, vni20, vni30). 
With a single VXLAN device, a set of VNIs represent a single device model. The single VXLAN device has a set of attributes that belong to the VXLAN construct.</br> 
Individual VNIs include a VLAN to VNI mapping and you can specify which VLANs map to the associated VNIs. Single VXLAN device simplifies the configuration and reduces the overhead by replacing multiple traditional VXLAN devices with a single VXLAN device.

Using NCLU you can configure only the traditional (multiple) VXLAN device. With NVUE, you can configure only the single VXLAN device. Some of the VXLAN commands looks similar on both CLIs, but as the implementations is different, some of them aren't.</br>

Below are a few similar VXLAN and EVPN commands, for full configuration and more detailed information, check out the {{<kb_link latest="cl" url="Network-Virtualization/" text="Network Virtualization" >}} section in the Cumulus Linux User Guide.

<table>
    <tr>
        <th>
        NCLU Command
        </th>
        <th>
        NVUE Command
        </th>
        <th>
        Comments
        </th>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add loopback lo clag vxlan-anycast-ip &lt;ip&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set nve vxlan mlag shared-address &lt;ip&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vxlan &lt;name&gt; vxlan id &lt;number&gt;</code>
        </td>
        <td rowspan="2" style="vertical-align : middle">
        <code>nv set bridge domain br_default vlan &lt;number&gt; vni &lt;number&gt;</code>
        </td>
        <td rowspan="2" style="vertical-align : middle">
        Using NCLU, you have to create the VXLAN device(/s) and assigln it to VLAN in the bridge.</br>In NVUE, the single VXLAN device is created by mapping the VLAN to VNI on the bridge. 
        </td>
    </tr>
    <tr>
        <td>
        <code>net add vxlan &lt;name&gt; bridge access &lt;vlan-id&gt;</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add loopback lo vxlan local-tunnelip &lt;ip&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set nve vxlan source address &lt;ip&gt;</code>
        </td>
        <td style="vertical-align : middle">
        In NCLU, it is possible to set local-tunnel IP seperatly per device using</br> <code>net add vxlan &lt;name&gt; vxlan local-tunnelip &lt;ip&gt;</code> command.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vlxan &lt;name&gt; bridge arp-nd-suppress on</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set nve vxlan arp-nd-suppress on</code>
        </td>
        <td rowspan="2" style="vertical-align : middle">
        In NCLU, all VXLAN related configuration must be configured on every device.</br> As NVUE has a single VXLAN device, the configuration is global for all VNIs.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vlxan &lt;name&gt; bridge bridge learning off</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set nve vxlan mac-learning off</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vlxan &lt;name&gt; stp bpduguard</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default stp bpdu-guard on</code>
        </td>
        <td rowspan="2" style="vertical-align : middle">
        In NCLU, spanning-tree configuration is done on each interface including the VXLAN devices.</br>in NVUE, this configuration is done on the bridge ports level only. 
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vlxan &lt;name&gt; stp portbpdufilter</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; bridge domain br_default stp admin-edge on</code>
        </td>
    </tr>
    <tr>
        <td rowspan="2" style="vertical-align : middle">
        <code>net add bgp l2vpn evpn neighbor &lt;ip-addr|interface|peer-group&gt; activate</code>
        </td>
        <td>
        <code>nv set evpn enable on</code>
        </td>
        <td rowspan="3" style="vertical-align : middle">
        In NVUE, EVPN-AF must be enabled globally and BGP proccess as well as the peer activation in it.</br>Unlike with NCLU, in NVUE you do not need enable the BGP control plane for all VNIs with the <code>advertise-all-vni</code> option.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;default|name&gt; router bgp address-family l2vpn-evpn enable on</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp l2vpn evpn advertise-all-vni</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;default|name&gt; router bgp peer &lt;ip-addr|interface|peer-group&gt; address-family l2vpn-evpn enable on</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add bgp vrf &lt;name&gt; l2vpn evpn advertise ipv4 unicast</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf &lt;name&gt; router bgp address-family ipv4-unicast route-export to-evpn</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vrf &lt;name&gt; vni &lt;number&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set vrf vrf &lt;name&gt; evpn vni &lt;number&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vlan &lt;number&gt; vrf &lt;name&gt;</code>
        </td>
        <td rowspan="3" style="vertical-align : middle">
        </td>
        <td rowspan="3" style="vertical-align : middle">
        In NVUE, L3 VNI doesn't need a separate VLAN allocation.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vxlan &lt;name&gt; bridge access &lt;vlan-id&gt;</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add vrf &lt;name&gt; vni &lt;number&gt;</code>
        </td>
    </tr>
</table>

<details>
<summary>Active-Active VXLAN Symmetric Routing - NVUE Configuration Example</summary>

``` 
nv set vrf default router bgp autonomous-system 65103
nv set vrf RED evpn vni 4001
nv set vrf BLUE evpn vni 4002
nv set vrf default router bgp router-id 10.10.10.3
nv set vrf default router bgp peer-group underlay
nv set vrf default router bgp peer-group underlay remote-as external
nv set vrf default router bgp peer peerlink.4094 type unnumbered
nv set vrf default router bgp peer peerlink.4094 peer-group underlay
nv set vrf default router bgp peer swp51 type unnumbered
nv set vrf default router bgp peer swp51 peer-group underlay
nv set vrf default router bgp peer swp52 type unnumbered
nv set vrf default router bgp peer swp52 peer-group underlay
nv set vrf default router bgp peer swp53 type unnumbered
nv set vrf default router bgp peer swp53 peer-group underlay
nv set vrf default router bgp peer swp54 type unnumbered
nv set vrf default router bgp peer swp54 peer-group underlay
nv set vrf default router bgp address-family ipv4-unicast redistribute connected
nv set vrf default router bgp peer-group underlay address-family l2vpn-evpn enable on
nv set vrf default router bgp address-family l2vpn-evpn enable on
nv set evpn enable on
nv set vrf RED router bgp autonomous-system 65103
nv set vrf RED router bgp router-id 10.10.10.3
nv set vrf RED router bgp address-family ipv4-unicast redistribute connected
nv set vrf RED router bgp address-family ipv4-unicast route-export to-evpn
nv set vrf BLUE router bgp autonomous-system 65103
nv set vrf BLUE router bgp router-id 10.10.10.3
nv set vrf BLUE router bgp address-family ipv4-unicast redistribute connected
nv set vrf BLUE router bgp address-family ipv4-unicast route-export to-evpn
nv set interface bond1 bond member swp1
nv set interface bond2 bond member swp2
nv set interface bond3 bond member swp3
nv set interface peerlink bond member swp49,swp50
nv set interface peerlink type bond
nv set interface bond1 bridge domain br_default access 10
nv set interface bond1 bond mlag id 1
nv set interface bond1-3 bond lacp-bypass on
nv set interface bond1-3 link mtu 9000
nv set interface bond1-3 bridge domain br_default stp bpdu-guard on
nv set interface bond1-3 bridge domain br_default stp admin-edge on
nv set interface bond2 bridge domain br_default access 20
nv set interface bond2 bond mlag id 2
nv set interface bond3 bridge domain br_default access 30
nv set interface bond3 bond mlag id 3
nv set interface peerlink bridge domain br_default
nv set interface bond1 bridge domain br_default
nv set interface bond2 bridge domain br_default
nv set interface bond3 bridge domain br_default
nv set bridge domain br_default vlan 10,20,30
nv set bridge domain br_default type vlan-aware
nv set mlag init-delay 10
nv set mlag backup 10.10.10.4
nv set mlag peer-ip linklocal
nv set mlag priority 1000
nv set mlag mac-address 44:38:39:BE:EF:BB
nv set interface swp1 link state up 
nv set interface swp2 link state up 
nv set interface swp3 link state up 
nv set interface swp49-50 link state up 
nv set interface swp51-54 link state up 
nv set nve vxlan mlag shared-address 10.0.1.34
nv set interface lo ip address 10.10.10.3/32
nv set nve vxlan source address 10.10.10.3
nv set interface vlan10 ip address 10.1.10.4/24
nv set interface vlan10 ip vrr address 10.1.10.1/24
nv set interface vlan10 ip vrr mac-address 00:00:00:00:00:10
nv set interface vlan10 ip vrr state up 
nv set interface vlan10 vlan 10
nv set interface vlan10 ip vrf RED
nv set interface vlan20 ip address 10.1.20.4/24
nv set interface vlan20 ip vrr address 10.1.20.1/24
nv set interface vlan20 ip vrr mac-address 00:00:00:00:00:20
nv set interface vlan20 ip vrr state up 
nv set interface vlan20 vlan 20
nv set interface vlan20 ip vrf RED
nv set interface vlan30 ip address 10.1.30.4/24
nv set interface vlan30 ip vrr address 10.1.30.1/24
nv set interface vlan30 ip vrr mac-address 00:00:00:00:00:30
nv set interface vlan30 ip vrr state up 
nv set interface vlan30 vlan 30
nv set interface vlan30 ip vrf BLUE
nv set vrf BLUE
nv set vrf RED
nv set nve vxlan arp-nd-suppress on
nv set bridge domain br_default vlan 10 vni 10
nv set bridge domain br_default vlan 20 vni 20
nv set bridge domain br_default vlan 30 vni 30
```

</details>

<details>
<summary>Active-Active VXLAN Symmetric Routing - NCLU Configuration Example</summary>

``` 
net add bgp autonomous-system 65103
net add vrf RED vni 4001
net add vrf BLUE vni 4002
net add bgp router-id 10.10.10.3
net add bgp neighbor underlay peer-group
net add bgp neighbor underlay remote-as external
net add bgp neighbor peerlink.4094 interface peer-group underlay
net add bgp neighbor swp51 interface peer-group underlay
net add bgp neighbor swp52 interface peer-group underlay
net add bgp neighbor swp53 interface peer-group underlay
net add bgp neighbor swp54 interface peer-group underlay
net add bgp ipv4 unicast redistribute connected
net add bgp l2vpn evpn  neighbor underlay activate
net add bgp l2vpn evpn  advertise-all-vni
net add bgp vrf RED autonomous-system 65103
net add bgp vrf RED router-id 10.10.10.3
net add bgp vrf RED ipv4 unicast redistribute connected
net add bgp vrf RED l2vpn evpn  advertise ipv4 unicast
net add bgp vrf BLUE autonomous-system 65103
net add bgp vrf BLUE router-id 10.10.10.3
net add bgp vrf BLUE ipv4 unicast redistribute connected
net add bgp vrf BLUE l2vpn evpn  advertise ipv4 unicast
net add bond bond1 bond slaves swp1
net add bond bond2 bond slaves swp2
net add bond bond3 bond slaves swp3
net add bond peerlink bond slaves swp49,swp50
net add vxlan vni10 vxlan id 10
net add vxlan vni20 vxlan id 20
net add vxlan vni30 vxlan id 30
net add vxlan vniBLUE vxlan id 4002
net add vxlan vniRED vxlan id 4001
net add bond bond1 bridge access 10
net add bond bond1 clag id 1
net add bond bond1-3 bond lacp-bypass-allow
net add bond bond1-3 mtu 9000
net add bond bond1-3 stp bpduguard
net add bond bond1-3 stp portadminedge
net add bond bond2 bridge access 20
net add bond bond2 clag id 2
net add bond bond3 bridge access 30
net add bond bond3 clag id 3
net add bridge bridge ports vni10,vni20,vni30,vniRED,vniBLUE,peerlink,bond1,bond2,bond3
net add bridge bridge vids 10,20,30
net add bridge bridge vlan-aware
net add interface peerlink.4094 clag args --initDelay 10
net add interface peerlink.4094 clag backup-ip 10.10.10.4
net add interface peerlink.4094 clag peer-ip linklocal
net add interface peerlink.4094 clag priority 1000
net add interface peerlink.4094 clag sys-mac 44:38:39:BE:EF:BB
net add loopback lo clag vxlan-anycast-ip 10.0.1.34
net add loopback lo ip address 10.10.10.3/32
net add loopback lo vxlan local-tunnelip 10.10.10.3
net add vlan 10 ip address 10.1.10.4/24
net add vlan 10 ip address-virtual 00:00:00:00:00:10 10.1.10.1/24
net add vlan 10 vrf RED
net add vlan 20 ip address 10.1.20.4/24
net add vlan 20 ip address-virtual 00:00:00:00:00:20 10.1.20.1/24
net add vlan 20 vrf RED
net add vlan 30 ip address 10.1.30.4/24
net add vlan 30 ip address-virtual 00:00:00:00:00:30 10.1.30.1/24
net add vlan 30 vrf BLUE
net add vlan 4001 hwaddress 44:38:39:BE:EF:BB
net add vlan 4001 vrf RED
net add vlan 4002 hwaddress 44:38:39:BE:EF:BB
net add vlan 4002 vrf BLUE
net add vxlan vni10 bridge access 10
net add vxlan vni10,20,30,vniBLUE,vniRED bridge arp-nd-suppress on
net add vxlan vni10,20,30,vniBLUE,vniRED bridge learning off
net add vxlan vni10,20,30,vniBLUE,vniRED stp bpduguard
net add vxlan vni10,20,30,vniBLUE,vniRED stp portbpdufilter
net add vxlan vni20 bridge access 20
net add vxlan vni30 bridge access 30
net add vxlan vniBLUE bridge access 4002
net add vxlan vniRED bridge access 4001
```

</details>


## Access Control Lists (ACLs)

ACLs in Cumulus Linux are based on Linux iptables so keep this in mind:
- There is no implicit deny. ACLs must end in a `match any` and `action deny` rule to drop all unmatched traffic.
- There is no support for wildcard masks. You must list subnets individually.
In addition to NCLU commands, you can configure ACLs stright by setting ebtables and iptables rules. To ease ACL management on the system, you can use a tool called `cl-acltool`.

For more information, refer to the {{<kb_link latest="cl" url="System-Configuration/Netfilter-ACLs/_index.md" text="Netfilter - ACLs" >}} section of the Cumulus Linux User Guide.

<table>
    <tr>
        <th>
        NCLU Command
        </th>
        <th>
        NVUE Command
        </th>
        <th>
        Comments
        </th>
    </tr>
    <tr>
        <td rowspan="3" style="vertical-align : middle">
        <code>net add acl &lt;ipv4|ipv6|mac&gt; &lt;name&gt; &lt;action&gt; &lt;attributes&gt; [&lt;value&gt;]</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set acl &lt;name&gt; type &lt;ipv4|ipv6|mac&gt;</code>
        </td>
        <td rowspan="3" style="vertical-align : middle">
        NCLU allows ACL configuration in one line, but doesn't have sequance numbers. To change sequance numbers, you have to edit <code>nclu_acl.conf</code> file.</br>NVUE ACL configuration must be using separate commands and it links the source, destination, and actions with the &lt;name&gt; &lt;seq&gt; values.
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv set acl &lt;name&gt; rule &lt;seq&gt; match &lt;ip|mac&gt; &lt;attributes&gt; &lt;value&gt;</code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>nv set acl &lt;name&gt; rule &lt;seq&gt; action &lt;action&gt; [&lt;attributes&gt;] </code>
        </td>
    </tr>
    <tr>
        <td style="vertical-align : middle">
        <code>net add interface &lt;interface&gt; acl &lt;ipv4|ipv6|mac&gt; &lt;name&gt; &lt;inbound|outboung&gt;</code>
        </td>
        <td style="vertical-align : middle">
        <code>nv set interface &lt;interface&gt; acl &lt;name&gt; &lt;inbound|outboung&gt;</code>
        </td>
        <td style="vertical-align : middle">
        </td>
</table>

## Where to Start?

To make the transition from NCLU to NVUE configuration easier, you can use this {{<exlink url="https://air.nvidia.com/migrate/" text="NVUE Migration Tool">}} to convert your NCLU to NVUE configuration by uploading the cl-support file.