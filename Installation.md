# Installation and Customization

- [Prerequisites](#prerequisites)
- [Installation Steps](#installation-steps)
- [Customization](#customization)

## Prerequisites

- Obtain specific IP address ranges for MetalLB's IP allocation
- Disable IP spoofing on your environment (e.g. Configure allowed-address-pair on ports of each node in OpenStack) 

## Installation Steps

1. Prepare manifests of MetalLB like following
    - **MetalLB deployment manifest:** [metallb.yaml](Manifests/metallb.yaml)
        - In OCP, remove PodSecurityPolicy API Objects from https://raw.githubusercontent.com/metallb/metallb/v0.9.4/manifests/metallb.yaml and use SCC instead of them
        - Modify image's URL if your environment is disconnected network environment
        - MetalLB uses "7946" port for internal communication. Set METALLB_ML_BIND_PORT if you want to change the port
        - Change nodeSelector if you want to run MetalLB pods on specific kind of nodes
    - **MetalLB SCC file:** [metallb_scc.yaml](Manifests/metal_scc.yaml)
        - Only privileges for network are required for MetalLB, so we removed other privileges from this yaml
    - **MetalLB L2 Mode ConfigMap:** [configMap_L2.yaml](Manifests/configMap_L2.yaml)
        - This is an example so user must update it for their environment according to [Customization](#customization)
1. Create a new Probject for MetalLb
   ```
   $ oc new-project metallb
   ```
1. Create a SCC for MetalLB
   ```
   $ oc create -f metallb-scc.yaml
   securitycontextconstraints.security.openshift.io/metallb-scc created
   ```
1. Bind the SCC to 'speaker' sa ('speaker' sa is created in a later step)
   ```
   $ oc adm policy add-scc-to-user metallb-scc -n metallb -z speaker
   clusterrole.rbac.authorization.k8.io/system:openshift:scc:metallb-scc added: "speaker"</pre>
   ```
1. Create "memberlist" secret using below command
   ```
   $ oc create secret generic -n metallb  memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
   secret/memberlist created</pre>
   ```
1. Create "metallb" configmap using below command
   ```
   $ oc create -f configMap_L2.yaml
   configmap/metallb created</pre>
   ```
1. Apply metallb.yaml. MetalLB pods will be deployed by that
   ```
   $ oc apply -f metallb.yaml
   serviceaccount/controller created
   serviceaccount/speaker created
   clusterrole.rbac.authorization.k8s.io/metallb:controller created
   clusterrole.rbac.authorization.k8s.io/metallb:speaker created
   role.rbac.authorization.k8s.io/config-watcher created
   role.rbac.authorization.k8s.io/pod-lister created
   clusterrolebinding.rbac.authorization.k8s.io/metallb:controller created
   clusterrolebinding.rbac.authorization.k8s.io/metallb:speaker created
   rolebinding.rbac.authorization.k8s.io/config-watcher created
   rolebinding.rbac.authorization.k8s.io/pod-lister created
   daemonset.apps/speaker created
   deployment.apps/controller created</pre>
   ```
1. Check if MetalLB pods are running
   ```
   $ oc get pods -n metallb
   NAME                          READY   STATUS    RESTARTS   AGE
   controller-5f9dd67ddb-29hnw   1/1     Running   0          2d21h
   speaker-794f4                 1/1     Running   0          3h49m
   speaker-vm9jc                 1/1     Running   0          3h49m
   speaker-wwvm6                 1/1     Running   0          3h32m
   ...
   ```

## Customization 

**Note:** You have to rollout metallb with `oc -n metallb rollout restart deployment controller` after updating configmap

You can modify MetalLB's configuration by updating "metallb" configmap on the same namespace.

The following is a basic configuration of MetalLB L2 mode.

```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb
  name: config
data:
  config: |
    address-pools:
    - name: default
      avoid-buggy-ips: true ...(1)
      protocol: layer2
      addresses:
      - 192.168.202.90-192.168.202.98 ...(2)
```

- (1): Don't assign IP addresses which end with .0 or .255 to svc (.0 and .255 might cause some issue with an old network equipment)
- (2): The range of IP addressed MetalLB controller will allocate to each service. Add /32 in the CIDR notation (e.g. 192.168.200.90/32) if you want to add just a single IP address in a pool

User can configure multiple IP address pools as follows.
```
...
    address-pools:
    - name: pool1
      protocol: layer2
      addresses:
      - <SubnetA's range>
    - name: pool2
      protocol: layer2
      addresses:
      - <SubnetB's range>
      auto-assign: false ... (3)
```

(3) is the flag that MetalLB disables auto IP addresss allocation.
User must set `spec.loadBalancerIP` in svc's yaml for allocating an IP of a pool whose "auto-assign" flag is false.

Set `auto-assign: false` to all pools except for a pool which you want to use by default.