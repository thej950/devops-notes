# validate version of image in cluster 
```
kubectl get pod mysql-0 -n webapp -o jsonpath='{.spec.containers[0].image}'; echo
```
- it must show version of image navathej408/mydb:v4 like this 


===

# to check pod logs 

```
kubectl logs mysql-0 -n webapp
```

# edit details  while running pod instead of updating in manifest and apply 
```
kubectl edit <object-type> <name-of-object> 
kubectl edit statefulset mysql -n webapp
```

# check events by pod

```
kubectl describe pod mysql-0 -n webapp
kubectl describe pod mysql-0 -n webapp | grep -iE "pull|Error"

```

# logs checking for pod 

kubectl logs <mysql-pod-name>

# to check storage info 
```
kubectl get storageclass
```


# remove all pods in all namespaces  
```
kubectl delete all --all -A 
# this will delete only pod,services,dp,STS,replicasets,daemonsets 
# it will not delete pvc,pv,secrets,configMaps,Ingress,CRDs,Namespaces,ServiceAccounts
# delete all → delete all "workload" and "service" resources
# --all → delete all objects of those types
# -A → across ALL namespaces
```

kubectl delete pods --all --all-namespaces
kubectl get pods --all-namespaces -o name | grep -v "kube-system" | xargs kubectl delete
```

# To connect pod 
```
kubectl exec -it mysql-0 -n webapp -- bash
```

