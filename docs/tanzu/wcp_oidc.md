# End-to-end: Using Gitlab as an external OIDC for WCP
This guide is an end-to-end walkthrough for setting up Gitlab external OIDC access to WCP guest clusters.
## Why?
When you deploy workload clusters in your organization, you want to control who gets to do what on which cluster. RBAC is essential and WCP offers two options out of the box:

1. vSphere SSO - This leverages the vSphere SSO configuration.  
2. External OIDC - This allows you to connect and external OIDC provider directly to the supervisor and workload clusters.

In this guide, we are going to use Gitlab as an external OIDC provider to authenticate users.
Enabling an external OIDC provider also allows you to create a kubeconfig that can be shared among developers, since there are no credentials stored in the kubeconfig file. All authentication is done through pinniped.

## Administrator tasks

### Setting up Gitlab
1. In the Gitlab admin area, go to the application page and create a new application.
2. Grab the redirect URL from the supervisor (Configure -> Identity Providers in the vsphere WCP UI)
3. Select Trusted and Confidential. Under scopes, select openid, profile and email.
4. Temporarily copy the user id and the secret. The secret can only be read now!

### Configuring the supervisor
1. Back in the vSphere UI, create a new identity provider.
2. fill in the following provider configuration:
    2.1. Issuer URL: this is the Gitlab fqdn.
    2.2. Username claim: nickname
    2.3. Group claim: groups
3. Next, fill in the user id and secret.
4. In additional settings, you __must__ fill in openid in "Additional Scopes". Otherwise pinniped will automatically request the offline_access scope which does not exist in gitlab.
5. If your gitlab instance is signed by a private CA, add the CA certificate.

### Verifying the OIDC connection
A siimple way of checking whether your Supervisor has succesfully connected to the OIDC provider is to get the OIDCIdentityProvider object in the vmware-system-pinniped namespace on the Supervisor.

1. Log into the supervisor. Generally you'll do this through the vSphere SSO  
```kubectl vsphere login --server dkv-tanzu-mgmt.dklab.be  -u administrator@dklab.be```
2. Grab the active OIDCIdentityProvider and verify it's status is __Ready__  
```
kubectl get OIDCIdentityProvider -n vmware-system-pinniped
NAME       ISSUER                 STATUS   AGE
oidc-idp   https://git.dklab.be   Ready    20h

```
3. Describe the resulting object if you want more details    
```kubectl describe OIDCIdentityProvider oidc-idp -n vmware-system-pinniped```

### Setting up the Tanzu cli and generating the kubeconfig file
The next step is to generate a kubeconfig that leverages pinniped to connect to Gitlab.

1. Check the correct version to download from this [Compatibility matrix](https://docs.vmware.com/en/VMware-Tanzu-CLI/index.html#compatibility-with-vmware-tanzu-products-1)
2. Once you've installed the tanzu binary, install the basic TKG group:
```
tanzu plugin install --group vmware-tkg/default
```
3. With the basic tanzu cli set up, connect to the supervisor cluster as an administrator. This will open a browser window to gitlab.
```
tanzu context create SVC_CLUSTER_NAME --endpoint https://SUPERVISOR_IP
```
4. Once you are logged in, check the cluster state
```
tanzu cluster list -n VSPHERE_NAMESPACE
```
5. Once you've verified your cluster is visible in the namespace, you can grab the user kubeconfig file
```
tanzu cluster kubeconfig get CLUSTER_NAME -n VSPHERE_NAMESPACE --export-file KUBECONFIG_FILE_PATH
```

Now you have exported a kubeconfig file that can be shared among developers. This kubeconfig file does not contain any user credentials but relies on the tanzu cli and the pinniped-auth plugin to connect to Gitlab and provide user authentication.

### Setting user rights on the cluster and/or namespace
The Authentication piece has been completed, but users still need the correct authorization on the vSphere namespace and/or workload cluster to do their job.  
See the flowchart and explanation [here](rbac_on_wcp.md)

## Developer/user tasks
As a developer, there are less steps to execute to be able to connect to the workload cluster.

### Downloading and configuring the Tanzu cli
You can follow the same matrix as in the previous chapter to determine what Tanzu cli version you require: [Compatibility matrix](https://docs.vmware.com/en/VMware-Tanzu-CLI/index.html#compatibility-with-vmware-tanzu-products-1)

1. Install the correct Tanzu cli version.
2. Install the basic TKG group (this contains the required pinniped plugin)
```
tanzu plugin install --group vmware-tkg/default
```

### Using the generated kubeconfig
As a part of the Administrator workflow, you as a developer or user should have received a kubeconfig file.  
You can now use the kubeconfig file as usual with kubectl. On first use, a browser window will open requiring you to log into Gitlab. Once logged in you can now 
