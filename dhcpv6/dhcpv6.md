# DHCP routing v6 configuration on cisco devices

## stateless dhcpv6 server configuration

dcpv6 server configuration is done by following steps:

1. Enable the DHCPv6 service on the router.
2. Configure the DHCPv6 pool with the desired parameters.
3. Configure the interface to use DHCPv6.
4. (Optional) Configure additional options such as DNS servers or domain names.
5. Verify the configuration and ensure that clients can obtain addresses.

```bash
enable
conf
host r1
pv6 unicast-routing
ipv6 dhcp pool IPV6-STATELESS
    dns-server 2001:db8:acad:1::254
    domain-name example.com
    exit
interface g0/1
    description Link to LAN
    ipv6 add fe80:: link-local
    ipv6 add 2001:db8:acad:1::1/64
    ipv nd other-config-flag
    ipv dhcp server IPV6-STATELESS
    no shut
end
```

## stateless dhcpv6 client configuration

To configure a stateless DHCPv6 client on a Cisco device, you need to enable IPv6 on the interface and configure it to use DHCPv6 for address assignment. Here’s how you can do it:

```bash
    enable
    conf t
    hostname R3
    ipv6 unicast-routing
    interface g0/1
    no sh
    ipv6 address enable
    ipv address autoconfig
end

sh ipv6 int br
Em0/0                  [administratively down/down]
    unassigned
GigabitEthernet0/0     [administratively down/down]
    unassigned
GigabitEthernet0/1     [up/up]
    FE80::A2E0:AFFF:FEF6:6251
    2001:DB8:ACAD:1:A2E0:AFFF:FEF6:6251
Serial0/0/0            [administratively down/down]
    unassigned
Serial0/0/1            [administratively down/down]
    unassigned
sh ipv dhc in g0/1
GigabitEthernet0/1 is in client mode
  Prefix State is IDLE (0)
  Information refresh timer expires in 23:58:18
  Address State is IDLE
  List of known servers:
    Reachable via address: FE80::
    DUID: 00030001A0E0AFF2E420
    Preference: 0
    Configuration parameters:
      DNS server: 2001:DB8:ACAD:1::254
      Domain name: example.com
      Information refresh time: 0
  Prefix Rapid-Commit: disabled
  Address Rapid-Commit: disabled
```

## Stateful dhcpv6 server configuration checks

```bash
show ipv6 dhcp pool
DHCPv6 pool: IPV6-STATEFUL
  Address allocation prefix: 2001:DB8:ACAD:1::/64 valid 172800 preferred 86400 (2 in use,  0 conflicts)
  DNS server: 2001:4860:4860::8888
  Domain name: example.com
  Active clients: 2

show ipv6 dhcp binding
Client: FE80::192F:6FBC:9DB:B749
  DUID: 0001000125148183005056B327D6
  Username : unassigned
  VRF : default
  IA NA: IA ID 0x03000C29, T1 43200, T2 69120
    Address: 2001:DB8:ACAD:1:A43C:FD28:9D79:9E42
            preferred lifetime 86400, valid lifetime 172800
            expires at Sep 27 2019 09:10 AM (171192 seconds)
Client: FE80::2FC:BAFF:FE94:29B1
  DUID: 0003000100FCBA9429B0
  Username : unassigned
  VRF : default
  IA NA: IA ID 0x00060001, T1 43200, T2 69120
    Address: 2001:DB8:ACAD:1:B4CB:25FA:3C9:747C
            preferred lifetime 86400, valid lifetime 172800
            expires at Sep 27 2019 09:29 AM (172339 seconds)
R1#

```

## DHCPv6 relay configuration

To configure a DHCPv6 relay on a Cisco device, you need to enable IPv6 routing and configure the interface to forward DHCPv6 messages to the DHCPv6 server. Here’s how you can do it:

```bash
enable
conf t
hostname R2
ipv6 unicast-routing
interface g0/1
    description Link to LAN
    ipv6 address 2001:db8:acad:1::2/64
    ipv6 dhcp relay destination 2001:db8:acad:1::1
    no shut
interface g0/2
    description Link to WAN
    ipv6 address 2001:db8:acad:2::1/64
    no shut
end
```

## DHCPv6 relay verification

To verify the DHCPv6 relay configuration, you can use the following commands:

```bash
show ipv6 dhcp relay
DHCPv6 Relay Information:
  Interface: GigabitEthernet0/1
  Relay Destination: 2001:DB8:ACAD:1::1
  Relay Source: FE80::A2E0:AFFF:FEF6:6251
  Relay State: Enabled
  Relay Statistics:
    Packets Forwarded: 0
    Packets Dropped: 0
  Relay Options:
    Option 0: 0x00000000
    Option 1: 0x00000000
```

To check the DHCPv6 relay statistics, you can use:

```bash
show ipv6 dhcp relay statistics
DHCPv6 Relay Statistics:
  Packets Forwarded: 0
  Packets Dropped: 0
  Packets Received: 0
  Packets Sent: 0
  Relay Options:
    Option 0: 0x00000000
    Option 1: 0x00000000
```
