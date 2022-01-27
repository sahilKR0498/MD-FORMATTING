# Uninstallation

## Uninstallation steps

1. Delete resources created by the manifests
   ```
   $ oc delete -f metallb.yaml
   $ oc delete -f configMap_L2.yaml
   $ oc delete -f metallb-scc.yaml
   $ oc delete -n metallb secret/memberlist
   ```
1. Delete the project
   ```
   $ oc delete project metallb
   ```
1. Recreate svc of your applications if you want to remove LoadBalancer from it