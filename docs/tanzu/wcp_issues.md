# Supervisor issues
This page is meant as a high lvl KB for issues I've encountered with Supervisor.

## Cleanup
### Unable to remove NSX bits due to expired NSX password
__Symptoms__:  
The supervisor removal is stuck on removing the NSX bits.    
__Logs__:  
````
faultcode: ns0:FailedAuthentication
faultstring: Password of the user logging on is expired. :: Password of the user logging on is expired. :: User account expired: {Name: wcp-cluster-user-domain-REDACTED, Domain: vsphere.local}
faultxml: <?xml version='1.0' encoding='UTF-8'?><S:Envelope xmlns:S="http://schemas.xmlsoap.org/soap/envelope/"><S:Body><S:Fault xmlns:ns4="http://www.w3.org/2003/05/soap-envelope"><faultcode xmlns:ns0="http://docs.oasis-open.org/ws-sx/ws-trust/200512">ns0:FailedAuthentication</faultcode><faultstring>Password of the user logging on is expired. :: Password of the user logging on is expired. :: User account expired: {Name: wcp-cluster-user-domain-REDACTED, Domain: vsphere.local}</faultstring></S:Fault></S:Body></S:Envelope>

2024-10-01T02:09:28.793Z error wcp [kubelifecycle/cluster_network.go:233] [opID=6691222f-b2fc507c-d6ee-4e5d-a122-964730e97c76] Received error cleaning NCP-created resources for cluster domain-REDACTED on NSX Managers: REDACTED:443. Err: exit status 1
````
__Root cause__:  
When the password of the nsx user expires, the nsx cleanup script can no longer reach out to NSX to clean up the relevant NSX objects.

__Solution__:  
1. Edit the _/usr/lib/vmware-wcp/nsx_policy_cleanup.py_ python script on the vCenter.
2. Set the following values:
````
self.vc endpoint = None
self.vc_username = None
self.vc_password = None 
self.username = "admin"
self.password = "My_admin_password"
````  

3. Restart the WCP service