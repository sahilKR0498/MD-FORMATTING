# Design Concept and Architecture Diagram

* [Design Concept](#design-concept)
* [Architecture Diagram](#architecture-diagram)

## Design Concept

* Choiced MetalLB L2 mode since it can be easily deploy and has less hardware requirement than BGP mode
* Run MetalLB controller on one of nodes
* Run MetalLB Speaker on all nodes where your application Pods are running on

## Architecture Diagram

![Layer2_Deployment](Images/MetalLB_L2_Deploy.PNG)

MetalLB has two major components:
* **Controller:** Controller is in charge of IP assignment
* **Speaker:** Speaker is a pod which advertises svcs with assigned IPs using a specifid advertising strategies. Speaker must be running on the same node of an application pod, so it is deployed as daemonset

Controller allocates an external IP to the svc(which must be defined as a type "LoadBalancer") of your application, then one of speaker pods announces the ip with ARP(IPv4)/NDP(IPv6). From the network’s perspective, it looks like that one of nodes where speaker Pods are running has multiple IP addresses to its network interface.

kube-proxy will make iptables rules for forwarding requests from the ip to your application pod, so you can access the pod from external.