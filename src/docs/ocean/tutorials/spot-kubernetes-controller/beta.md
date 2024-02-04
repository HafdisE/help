<meta name=“robots” content=“noindex”>

# Ocean Controller (Beta) Installation 

The beta controller is designed to align with Spot's goals of enhancing scaling experience and maximizing efficiency.  

## Risks and Recommendations 

Spot advises installing the beta version in a development cluster rather than a production environment.  

## Install the Beta Controller 

### Clusters with Existing Controller(s) 

1. Run the following command to retrieve the namespace where the existing controller is installed: 

```
export NAMESPACE=$(kubectl get cm -A \ 
  --field-selector=metadata.name=spotinst-kubernetes-cluster-controller-config \ 
  -o jsonpath='{.items[0].metadata.namespace}')
```

2. Run the following commands to export the details of the existing controller:  

```
set -o pipefail 
export SPOTINST_TOKEN=`(kubectl get secret -n $NAMESPACE spotinst-kubernetes-cluster-controller -o jsonpath='{.data.token}' | base64 -d) || kubectl get cm -n $NAMESPACE spotinst-kubernetes-cluster-controller-config -o jsonpath='{.data.spotinst\.token}'` 2&>1 
export SPOTINST_ACCOUNT=`(kubectl get secret -n $NAMESPACE spotinst-kubernetes-cluster-controller -o jsonpath='{.data.account}' | base64 -d) || kubectl get cm -n $NAMESPACE spotinst-kubernetes-cluster-controller-config -o jsonpath='{.data.spotinst\.account}'` 2&>1 
export SPOTINST_CLUSTER_IDENTIFIER=`kubectl get cm -n $NAMESPACE spotinst-kubernetes-cluster-controller-config -o jsonpath='{.data.spotinst\.cluster-identifier}'` 
```

3. Verify that all three variables have been exported successfully: 

```
env | grep -i spotinst 
```

4. Run the following command to scale down the existing controller’s deployment: 

```
kubectl scale deployment --replicas=0 -n $NAMESPACE spotinst-kubernetes-cluster-controller 
```
**Note**: You can return to the previous state at any time by running the same command with `--replicas=1`. 

## Helm Installation 

1. Run the following command to add spot helm repository: 

```
helm repo add spot https://charts.spot.io 
```

2. Update the repositories to the following:  

```
helm repo update 
```

3. Run the following command to install the controller: 

```
helm install ocean-controller spot/ocean-kubernetes-controller -n kube-system \ 
  --set spotinst.account=$SPOTINST_ACCOUNT \ 
  --set spotinst.token=$SPOTINST_TOKEN \ 
  --set spotinst.clusterIdentifier=$SPOTINST_CLUSTER_IDENTIFIER
```

The installation is complete.  

 

 

 