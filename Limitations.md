# MetalLB Limitations

Below are the limitations of MetalLB v0.9.4.

- [Limitations regardless of L2 or BGP mode](#limitations-regardless-of-l2-or-bgp-mode)
- [Limitations of L2 mode](#limitations-of-l2-mode)

## Limitations regardless of L2 or BGP mode

|**SI. No.**| **Summary** |
|-----------|-------------|
| 1.        | When a node where controller, speaker or application Pod are running on got down, the recovery time would depend on the setting of health check of a node of k8s side. By default, k8s takes 40 seconds(`--node-monitor-grace-period`) for detecting a node down and takes 5 mins(`--pod-eviction-timeout`) for eviting a pod from a down node |
| 2.        | Need to restart a controller pod if you updated the configmap of MetalLB |
| 3.        | Cannot expose both of TCP and UDP with one svc |
| 4.        | MetalLB Controller cannot work with multiple replicas. User still can access their application via MetalLB if the controller is down, but a new IP won't be assigned to a new svc until the controller gets recovered |
| 5.        | MetalLB cannot work with a Endpoints API Object which you created manually (By default, this object is created automatically when creating a svc) |

## Limitations of L2 mode

|**SI. No.**| **Summary** |
|-----------|-------------|
| 1.        | Only one of nodes who have Speaker pods can be an announcing node for a svc. So the node must handle all requests from external then it can cause a performance problem when many requests came |
| 2.        | Failback always happens when the previous announcing node got recovered. So, sessions between external and application pods via MetalLB might be disconnected when the node got recovered |
| 3.        | Speaker Pod must run on the same node of an application pod which you want to access via MetalLB, because MetalLB selects the announcing node from nodes which are recorded in Endpoints API Object's `subsets.addresses` |
