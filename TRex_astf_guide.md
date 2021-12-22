# TRex Advanced Stateful (ASTF) Mode Quick Guide

This guide assumes that you have already completed the [TRex Tutorial](https://github.com/open-traffic-generator/snappi-trex/blob/main/docs/trex-tutorial.md) and that TRex is set up and installed.

## Running TRex in ASTF Mode

To run TRex in Advanced Stateful Mode, you require the `--astf` tag when running TRex.
```
sudo ./t-rex-64 -i --astf
```

## ASTF Configurations

You can generate stateful traffic in TRex using the TRex ASTF Python API. The documentation on how to use this API can be found [here](https://trex-tgn.cisco.com/trex/doc/cp_astf_docs/index.html).

Here is a sample script using the TRex ASTF Python API that comes with TRex. Simply run the following command to generate some stateful traffic. 
```
python3 /opt/trex/v2.90/automation/trex_control_plane/interactive/trex/examples/astf/astf_async.py
```
<br> 

Otherwise, you can create traffic configurations in `yaml` files. Such configuration files may look like such.

```
- duration : 1.0
  generator :
          distribution : "seq"
          clients_start : "16.0.0.1"
          clients_end   : "16.0.0.10"
          servers_start : "48.0.0.1"
          servers_end   : "48.0.0.3"
          dual_port_mask : "1.0.0.0"
          tcp_aging      : 1
          udp_aging      : 1
  cap_ipg    : true
  cap_info :
     - name: cap2/dns.pcap
       cps : 10.0                        <1>
     - name: avl/delay_10_http_browsing_0.pcap
       cps : 2.0                         <1>
```

Fields can be changed in order for different configurations.

`clients_start`: Specifies the starting IP address of clients.

`clients_end`: Specifies the ending IP address of servers. Servers' IP addresses will increment from `clients_start` to `clients_end`.

`servers_start`: Specifies the starting IP address of clients.

`servers_end`: Specifies the ending IP address of servers. Servers' IP addresses will increment from `servers_start` to `servers_end`.

<br>

Now, to apply the configuration, you must run TRex using the `-f` flag.
Example:
```
sudo ./t-rex-64 -f [yaml file path]
```

EDIT: You can no longer use YAML configurations for ASTF mode. You may only use python traffic profiles. (Same exact usage as the above command but with --astf tag).

```
sudo ./t-rex-64 --astf -f [python profile file path]
```