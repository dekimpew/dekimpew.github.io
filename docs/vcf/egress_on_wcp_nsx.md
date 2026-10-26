# Using Antrea Egress on Supervisor with NSX
Reading material: 
- NSX child segments: https://techdocs.broadcom.com/us/en/vmware-cis/nsx/vmware-nsx/4-2/administration-guide/segments/creating-a-child-segment.html
- Antrea Egress: https://antrea.io/docs/v2.4.3/docs/egress/

## Steps to enable egress
1. Enable EgressSeparateSubnet and nsx integration in antreaconfigs.cni.tanzu.vmware.com
```
#AntreaConfig.yaml
apiVersion: cni.tanzu.vmware.com/v1alpha1
kind: AntreaConfig
metadata:
 name: dkv-tanzu-dc-cl3-antrea-package #prefix required
 namespace: dkv-tanzu-dc
spec:  
  antreaNSX:
    enable: true #false by default
  antrea:
    config:
      featureGates:
        EgressSeparateSubnet: true
```

2. Create the child segment via NSX API
```
antreansxctl child-segment-create --nsx-managers='10.0.0.54' --user=admin --password='VMware123!VMware123!' --cidr='10.100.0.0/24' --gateway='10.100.0.1' --parent='/infra/segments/vnet_fd18772a-fae6-4ec2-a5af-47d36740e388_0' --vlan=200 egress
```

3. Create the ExternalIPPool object on the cluster
```
apiVersion: crd.antrea.io/v1beta1
kind: ExternalIPPool
metadata:
  name: prod-external-ip-pool
spec:
  ipRanges:
  - start: 10.100.0.2
    end: 10.100.0.200
  subnetInfo:
    gateway: 10.100.0.1
    prefixLength: 24
    vlan: 200
  nodeSelector: {}
```