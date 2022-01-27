# Basic Usage

- [IP allocation](#ip-allocation)
  - [Expose with automatic IP allocation](#expose-with-automatic-ip-allocation)
  - [Expose with static IP allocation](#expose-with-static-ip-allocation)
  - [IP sharing](#ip-sharing)
- [Traffic Policy](#traffic-policy)

## IP allocation

### Expose with automatic IP allocation

If MetalLB has a pool which doesn't have `auto-assign: false` flag, it will assign an IP automatically if you create a svc with `spec.type: LoadBalancer`.

The following is the example to create a svc for nginx.

- By `oc expose`:
  ```
  $ oc expose deploy/nginx --protocol TCP --port 80 --target-port 8080 --type LoadBalancer
  ```
- By yaml:
  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: nginx
  spec:
    ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 8080
    selector:
      app: nginx
    type: LoadBalancer
  ```

After created the above, `192.168.202.31` was assigned automatically as follows.
```
$ oc describe svc -n test nginx
Name:                     nginx
Namespace:                test
Labels:                   <none>
Annotations:              <none>
Selector:                 app=nginx
Type:                     LoadBalancer
IP:                       172.30.107.43
LoadBalancer Ingress:     192.168.202.31
Port:                     <unset>  80/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  31467/TCP
Endpoints:                10.129.5.30:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason        Age   From                Message
  ----    ------        ----  ----                -------
  Normal  IPAllocated   7s    metallb-controller  Assigned IP "192.168.202.31"
  Normal  nodeAssigned  6s    metallb-speaker     announcing from node "worker2.ocp4m.cluster.sub.nec.test"
```
### Expose with static IP allocation

User can assign one of IPs of pools configured in "metallb" configmap by setting `spec.loadBalancerIP` in svc(See <=== below).
```
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  ports:
  - name: http
    port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: nginx
  type: LoadBalancer
  loadBalancerIP: 192.168.202.31 <===
```

#### IP sharing

By default, user cannot assign the same IP which has been already used by another svc.
User can allow it by seeting `metallb.universe.tf/address-pool: <sharing key>` annotation as follows.

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  annotations:
    metallb.universe.tf/address-pool: my-share-ip
...
```

The all of fllowing conditions must be satisfied for IP sharing.
- All svcs which share the same IP have the same sharing key
- All svcs which share the same IP uses different ports (e.g. tcp/80 for one and tcp/443 for another one)
- `spec.externalTrafficPolicy` is set to Cluster, or all svcs which share the same IP point to the exact same set of Pods

MetalLB still can assign another IP if user didn't set `spec.loadBalancerIP` in a svc.

## Traffic Policy

User can set Cluster or Local to `spec.externalTrafficPolicy` in a svc. Cluster is set by default.

- **Cluster:** kube-proxy makes iptables rules as to forward all requests received on any nodes to all pods which has been bound with a svc. In MetalLB, only one of node where speak pods are running can be **announing node**. So all requests from external will be handled only by the announing node, but they will be distributed to one of pods randomly with equal proportions. In addition to that, the source IP of requests will be hiden by an IP of the announing node
- **Local:** kube-proxy makes iptables rules as to forward requests received on a node to a pod which is running on the node. So, only the pod running on the announing node will receive all requests sent from external. The application can see the real source IP of the requests
