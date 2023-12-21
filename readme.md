# Datacom 2

## Table of Contents

```bash
╰─ tree                                        ─╯
.
├── 97113519-lab-1.pkt
├── 97113519-opdracht-implememt-a-small-network.pka
├── 97113519_20231207_eerste_opdracht.pkt
├── 97113519_who_hears_broadcast.pka
├── cli.txt
└── readme.md

1 directory, 6 files

```

## Research

Moet meer informatie over hoofdstuk Who hears what.

### Create Vlan

```bash
S1# configure terminal
S1(config)# vlan 20
S1(config-vlan)# name student
S1(config-vlan)# end

```

### Assign interface

```bash
S1# configure terminal
S1(config)# interface fa0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 20
S1(config-if)# end
```

### QOS Voice

```bash
S3(config)# vlan 20
S3(config-vlan)# name student
S3(config-vlan)# vlan 150
S3(config-vlan)# name VOICE
S3(config-vlan)# exit
S3(config)# interface fa0/18
S3(config-if)# switchport mode access
S3(config-if)# switchport access vlan 20
S3(config-if)# mls qos trust cos
S3(config-if)# switchport voice vlan 150
S3(config-if)# end
```

## Sourcesi
