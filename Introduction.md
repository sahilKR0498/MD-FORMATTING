# Introduction

- [Overview](#overview)  
  -  [Layer2 Mode](#layer2-mode)    
  - [BGP Mode](#bgp-mode)  
- [Out of Scope](#out-of-scope)  
- [Intended Audience](#intended-audience)  
- [Abbreviations](#abbreviations)  
- [Conventions](#conventions)  

## Overview

MetalLB is a load-balancer implementation for Kubernetes-based platform.

Although Kubernetes provides ingress for accessing application running on the cluster from external, it doesn't suited to handle raw TCP/UDP packets. (Most of ingress controller handles only HTTP/HTTPS/TLS protocol)
AWS or other public cloud services provide a network load-balancer function for that, but kubernetes itself doesn't provide such a function in on-premise environment.

MetalLB can be a solution for that.
End-user can expose their application running on the cluster to external with non-HTTP/HTTPS/TLS protocol via MetalLB.

MetalLB has two modes, Layer2 Mode and BGP Mode.
This guide focuses on Layer2 Mode.

## Out of Scope

Below details are out of the purview of the design document:
- MetalLB BGP mode detailed analysis
- Details of third party applications showed in usecase examples of MetalLB (e.g. MySQL, Nginx etc)
 
##  Intended Audience

This document is meant to be available for audience who has basic knowledge of kubernetes management.

## Abbreviations

|Abbreviations|Description                |
|-------|---------------------------------|
|L2 Mode|Layer2 Mode                      |
|K8s    |Kubernetes                       |
|OCP    |OpenShift Container Platform     |
|sa     |K8s's Service Account API Object |
|SCC    |Security Context Constraints     |
|svc    |K8s's Service API Object         |


## Conventions

All the work in this document is done based on MetalLB v0.9.4.