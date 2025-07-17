# My homelab setup
_Nothing beats something to call your own, independent of clouds, vpn's or corporate logins and policies_

## The hardware

### vSphere lab servers
| Comp | Whitebox 1 | Dell SFF 1 | Dell SFF 2 | 
| --- | --- | --- | --- |
| CPU | Intel Core i5 12500 | Intel Core i3-10100 | Intel Core i3-10100 |
| Motherboard | ASUS PRO B660M-C-D4-CSM | Dell Optiplex | Dell Optiplex |
| Memory | 2x Crucial Pro 64GB 3200MT  | 2x 32GB DDR4 3200MT | 2x 32GB DDR4 3200MT |
| OS disk | Crucial MX100 256GB | Western Digital Blue SN580 500 GB | Western Digital Blue SN580 500 GB |
| Datastore NVMe disk | Samsung 980 PRO 2TB | SK hynix Platinum P41 1TB | SK hynix Platinum P41 1TB |
| NVMe tiering disk | Western Digital RED SN700 500GB |
| NIC | Intel X520-DA2 | Intel i226 | Intel i226 |
| GPU | Nvidia P4 | n/a | n/a |
| PSU | be quiet! SFX POWER 3 300W | Dell Power brick | Dell Power brick |
| Enclosure | Norco 2U short rackmount | Dell Optiplex 7080 micro | Dell Optiplex 7080 micro |

### Proxmox lab server
| Comp | Hardware | 
| --- | --- |
| CPU | Intel Core i3 9100 |
| Motherboard | Fujitsu D3643-H1 |
| Memory | 2x Crucial 32GB 3200MT kit |
| OS disk | Crucial MX100 256GB |
| Datastore disk | Western Digital Blue 1TB SSD |
| NIC | Intel X520-DA2 |
| PSU | PicoPSU 150-XT with Leicke ULL 120W Adapter|
| Enclosure | Norco 2U short rackmount |

### NAS
| Comp | Hardware | 
| --- | --- |
| CPU | Intel Core i3 9100 |
| Motherboard | Fujitsu D3643-H1 |
| Memory | Crucial Ballistix 8GB DDR4 2400MT |
| OS disk | Western Digital Blue 1TB |
| Data disk 1 | Samsung 980 1TB NVMe |
| Data disk 2 | Toshiba MG08 16TB |
| NIC | Intel X520-DA2 |
| PSU | PicoPSU 150-XT with Leicke ULL 120W Adapter|
| Enclosure | Norco 2U 8bay rackmount |

## Software
My vSphere lab is currently set up as a three host vSphere cluster with the following software packages/versions.

| Software | Version |
| --- | --- |
| vSphere | 8U3 |
| vCenter | 8U3p1 |
| NSX | 4.2.0.1 |
| AVI Loadbalancer | 22.1.7 |

My primary lab server is running Proxmox 8.4. I made the switch to proxmox a few months ago in an effort to reduce power usage to a minimum since this system is running 24/7.  
vSphere is __not__ a power efficient Hypervisor since it doesn't really have to. 

At the time of the switch, the Broadcom aquisition was in full transition and internal vSphere licenses were no longer available. Not willing to drop money on VMUG just yet, I decided to go with Proxmox.

## Networking
All lab servers are connected with a single 1 GbE connection for management. The vSphere whitebox has one 10GB SFP+ DAC connection for data while the Proxmox build and Dell SFF's use a 2.5GbE link.