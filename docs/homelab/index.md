# My homelab setup
_Nothing beats something to call your own, independent of clouds, vpn's or corporate logins and policies_

## The hardware

### vSphere lab server
| Comp | Hardware | 
| --- | --- |
| CPU | Intel Core i5 12500 |
| Motherboard | ASUS PRO B660M-C-D4-CSM |
| Memory | 2x Crucial Pro 64GB 3200MT kit |
| OS disk | Crucial MX100 256GB |
| Datastore NVMe disk | Samsung 980 PRO 2TB |
| NVMe tiering disk | Western Digital RED SN700 500GB |
| NIC | Intel X520-DA2 |
| PSU | PicoPSU 150-XT with Leicke ULL 120W Adapter|
| Enclosure | Norco 2U short rackmount |

### Proxmox lab server
| Comp | Hardware | 
| --- | --- |
| CPU | Intel Core i3 9100 |
| Motherboard | Fujitsu D3643-H |
| Memory | 2x Crucial 32GB 3200MT kit |
| OS disk | Crucial MX100 256GB |
| Datastore disk | Western Digital Blue 1TB SSD |
| NIC | Intel X520-DA2 |
| PSU | PicoPSU 150-XT with Leicke ULL 120W Adapter|
| Enclosure | Norco 2U short rackmount |

## The software
My vSphere lab is currently set up as a single host vSphere cluster with the following software packages/versions.

| Software | Version |
| --- | --- |
| vSphere | 8U3 |
| vCenter | 8U3p1 |
| NSX | 4.2.0.1 |
| AVI Loadbalancer | 22.1.6 |

My primary lab server is running Proxmox. I made the switch to proxmox a few months ago in an effort to reduce power usage to a minimum. vSphere is __not__ a power efficient Hypervisor since it doesn't really have to. 

At the time of the switch, the Broadcom aquisition was in full transition and internal vSphere licenses were no longer available. Not willing to drop money on VMUG just yet, I decided to go with Proxmox.

## Networking
Both lab servers are connected with a single 1 GbE connection for management and one 10GB SFP+ DAC connection for data.