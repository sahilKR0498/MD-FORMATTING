# MetalLB BGP Mode

* [Overview](#overview)
* [System Requirement](#system-requirement)
* [Architecture Diagram](#architecture-diagram)

## Overview

If you have a route which supports BGP, MetalLB can work with BGP mode.
It has the following benefits over L2 mode.
- Can do load balancing not on an announcing node but on router side
- Can enable authentication using TCPMD5.
- No longer need to deploy speaker pod on every node when using Cluster traffic policy
- Failover time can be minimized by using features of the side of your router

On the other hand, it depends on the behaviors of a router you prepared.
We shows the basic ways to setup MetalLB with BGP mode here, but it will need to do more tune according to the implementation of your router

## System Requirement

- A physical or virtual router which supports BGP is required
- CNI plugin which can work with MetalLB BGP mode (See https://metallb.universe.tf/installation/network-addons/)
  - OpenShiftSDN isn't listed in the above page, but we verified that MetalLB can work with NetworkPolicy mode
- Non-cloud environemnt (See https://metallb.universe.tf/installation/clouds/)

## Architecture Diagram

![BGP_Mode__Deployment](Images/MetalLB_BGP_Deploy.PNG)

It is very similar to L2 Mode, but in BGP mode all requests from external will be handled on the router and it distributes the requests to appropriate nodes.

## Installation

Installation of BGP mode is same as Layer2 mode installation. Refer to link: [Installation](Design_Document/Deployment_Configuration.md#42-installation)

## Configuration

How to install MetalLB is the same as L2 mode.
What user needs to do for enabling BGP mode is to configure it on the configmap as follows.

```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb
  name: config
data:
  config: |
    peers:
    - peer-address: 10.0.0.1      ... (1)
      peer-asn: 64501             ... (2)
      my-asn: 64500               ... (3)
    address-pools:
    - name: default
      protocol: bgp
      addresses:
      - 192.168.10.0/24
```

- (1): The IP address of the router
- (2): AS number of the network of the router
- (3): AS number which you want to assign to MetalLB

For more information, please see [the official document of MetalLB](https://metallb.universe.tf/configuration/#bgp-configuration).
