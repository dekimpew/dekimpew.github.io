# Deploying VKS workload clusters with ArgoCD
With the addition of ArgoCD as a supervisor service, we can now easily deploy argoCD instances in vSphere namespaces and manage objects within these namespaces.  
One of the interesting things you can do is manage your workload cluster state with argoCD.  

In this example we will be deploying workload clusters in a namespace on a supervisor and show how argoCD can reconcile your cluster state.

## Prerequisites
- A running supervisor cluster
- A vSphere namespace
- A git repo to store your cluster yamls

## Deploying ArgoCD

### ArgoCD Operator
- Download the operator yaml and configuration from the repo [here](https://vsphere-tmm.github.io/Supervisor-Services/)
- Deploy the service through the "Add new service" wizard on the __Services__ tab
- Enable the ArgoCD operator on your supervisor. This is done through "Actions" -> "Manage Service"  in the __Services__ tab.
- Once the operator has been enabled on the supervisor, you should be able to see the "svc-argocd-operator-DOMAIN_X" namespace and pods.

### Deploy an ArgoCD instance in your vSphere namespace
With the ArgoCD operator installed, we can now deploy ArgoCD instances in our vSphere namespaces.  
You can follow the basic usage steps in this [repo](https://vsphere-tmm.github.io/Supervisor-Services/supervisor-services-labs/argocd-operator/usage.html). Refer to the ArgoCD Operator documentation for more detailed options of your Argo instance. 

__Attention__: If you roll your own manifest, make sure to use the ```kubernetes.io/os: CRX``` nodeSelector. Otherwise the pods will never schedule on your ESXi hosts.

- Deploy your ArgoCD instance in your chosen namespace.
- Once the pods are created, point your browser towards the LoadBalancer IP your ArgoCD service received.
- The admin password can be found in the ```MY_NAMESPACE-argocd-cluster``` secret.

### Creating the Argo application
You can either do this through the UI or CLI.  
Below is an example Application yaml:
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  annotations:
    argocd.argoproj.io/compare-options: ServerSideDiff=true
  name: argocd-clusters
  namespace: MY_NAMESPACE
spec:
  destination:
    namespace: MY_NAMESPACE
    server: https://kubernetes.default.svc
  ignoreDifferences:
  - group: cluster.x-k8s.io
    jqPathExpressions:
    - .spec.topology.variables[] | select(.name == "TKR_DATA")
    - .spec.topology.variables[] | select(.name == "user")
    - .spec.topology.variables[] | select(.name == "clusterEncryptionConfigYaml")
    - .spec.topology.variables[] | select(.name == "ntp")
    - .spec.topology.variables[] | select(.name == "extensionCert")
    kind: Cluster
  project: default
  source:
    path: .
    repoURL: https://github.com/MY_REPO.git
    targetRevision: HEAD
```

You can see the application already contains some additional fields to ignore diffs. More on that later.

### Pushing your cluster yaml to git and reconcile with ArgoCD.

