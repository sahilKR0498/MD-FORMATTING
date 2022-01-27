# System Requirement

- [Resource](#resource)
- [Software](#software)
- [Others](#others)

## Resource

The followings are the values of CPU/Memory request and limit written in the manifest file provided from MetalLB official. You must tune them according to the workload.

|Component          |CPU |Memory| 
|-------------------|----|------|
|MetalLB Controller |100m|100 Mi|   
|MetalLB Speaker    |100m|100 Mi|

## Software

Below are the software components which required for running MetalLB L2 Mode.
The "Version" column shows the versions we evaluated. Please keep in mind that there might be some differences when you uses future versions.

|Software               |Version            |URL                                                                     |
|-----------------------|-------------------|------------------------------------------------------------------------|
|Kubernetes             |v1.19.0(OCP v4.5)  |https://access.redhat.com/products/red-hat-openshift-container-platform/|
|MetalLB Controller     |v0.9.4             |https://hub.docker.com/r/metallb/controller                             |
|MetalLB Speaker        |v0.9.4             |https://hub.docker.com/r/metallb/speaker                                |

## Others

- IP spoofing feature must be disabled in IaaS layer before installing MetalLB  
  (e.g. Configure allowed-address-pair on ports of each node in OpenStack)
- CNI plugin which can work with MetalLB (See https://metallb.universe.tf/installation/network-addons/)
  - OpenShiftSDN isn't listed in the above page, but we verified that MetalLB can work with NetworkPolicy mode
- Non-cloud environemnt (See https://metallb.universe.tf/installation/clouds/)
  - Use a specific feature of Mega-cloud instead of MetalLB. For example, ELB can be used on AWS