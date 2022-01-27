# Upgrade

This is the steps of upgrading from MetalLBv0.9.4 to v0.9.5.

## Prerequisites

There is no special prerequisites for this version's upgrade, but please stop accessing applications via MetalLB during the upgrade. Otherwise your clients may get some error.
Please see [the release notes of MetalLB official](https://metallb.universe.tf/release-notes/) before upgrading.

## Upgrade Steps

1. Delete MetalLB controller and speaker pods using below command:
   ```
   $ oc delete -f metallb.yaml
   serviceaccount "controller" deleted
   serviceaccount "speaker" deleted
   clusterrole.rbac.authorization.k8s.io "metallb:controller" deleted
   clusterrole.rbac.authorization.k8s.io "metallb:speaker" deleted
   role.rbac.authorization.k8s.io "config-watcher" deleted
   role.rbac.authorization.k8s.io "pod-lister" deleted
   clusterrolebinding.rbac.authorization.k8s.io "metallb:controller" deleted
   clusterrolebinding.rbac.authorization.k8s.io "metallb:speaker" deleted
   rolebinding.rbac.authorization.k8s.io "config-watcher" deleted
   rolebinding.rbac.authorization.k8s.io "pod-lister" deleted
   daemonset.apps "speaker" deleted
   deployment.apps "controller" deleted</pre>
   ```
1. Change the image's versions of controller and speaker in metallb.yaml from v0.9.4 to v0.9.5
   ```
   $ vi metallb.yaml
   ...
           image: metallb/speaker:v0.9.5
   ...
           image: metallb/controller:v0.9.5
   ```
1. Reapply the manifest file
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
   deployment.apps/controller created
   ```
1. Check if MetalLB pods are running
   ```
   $ oc get pods | egrep "speaker|controller"
   controller-64554c6f68-kldr9           1/1     Running   0          112s
   speaker-2qfts                         1/1     Running   0          112s
   speaker-7kkdd                         1/1     Running   0          112s
   speaker-jq5rr                         1/1     Running   0          113s
   ...
   ```
1. Check if MetalLB's version was changed to v0.9.5
   ```
   $ oc logs controller-64554c6f68-kldr9 | head -1
   {"branch":"HEAD","caller":"main.go:142","commit":"v0.9.5","msg":"MetalLB controller starting version 0.9.5 (commit v0.9.5, branch HEAD)","ts":"2021-01-09T12:31:50.807913053Z","version":"0.9.5"}
    
   $ oc logs speaker-2qfts | head -1
   {"branch":"HEAD","caller":"main.go:80","commit":"v0.9.5","msg":"MetalLB speaker starting version 0.9.5 (commit v0.9.5, branch HEAD)","ts":"2021-01-09T12:31:50.617012746Z","version":"0.9.5"}
   ```