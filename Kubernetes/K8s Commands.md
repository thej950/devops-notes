K8s Commands
-----------------
1. Get
	1. Kubetl get nodes
	2. kubectl get all
	3. kubectl get pods 
	4. kubectl get hpa
	5. kubectl get services 
	6. kubectl get namespace
	7. kubectl get pods -n custome-namespace
	8. kubectl get nodes -o wide 
	9. kubectl get pods -o wide 
	10. kubectl get pv
	11. kubectl get pvs
	12. kubectl get svc
	13. kubectl get svc -n custome-namespace 
	14. kubectl get ingress
	15. kubectl get ingress -n custome-namespace
	16. kubectl get deployment
	17. kubectl get deployment -n custome-namespace
	18. kubectl get pods -n kube-system -w 
	19. kubectl get nodes --watch 
	20. kubectl get pv,pvc
	21. kubectl get all --all-namespaces
	22. kubectl get all -n prometheous 
	23. kubectl get service -n grafana 

2. Delete
	1. kubectl delete -f autoscaler.yml
	2. kubectl delete pods lg
	3. kubectl delete all --all (To delete all pods and services in default namespace)
	4. kubectl delete all --all -n <namespace> (To Delete all resources in a specific namespace)
	5. kubectl delete all --all --all-namespaces (warning dont use this it will delete all namespaces pods also by default pods also deleted )
	6. kubectl delete all --all --cascade (To Delete all resources, including labels)
	7. kubectl delete -f namespace.yml (To delete namespace)
	8. kubectl delete pods mydb (to delete pods)
	9. kubectl delete -f pod-definition1.yml (to delete pod-definition contain pods not file)
	10. kubectl delete --all pods
	11. kubectl delete --all svc
	12. kubectl delete --all deployments
	13. kubectl delete deployment nginx-deployment (delete nginx pods in the k8s cluster)

3. RUN
	1. kubectl run --image imagename specify-container-name
	2. kubectl run --image mysql mydb --env MYSQL_ROOT_PASSWORD=thej
	3. kubectl run mynginx --image=nginx 

4. EXPOSE
	1. kubectl expose deployments mynginx --type=LoadBalancer --port=80 (port expose 80)

5. EXEC
	1. kubectl exec -it mydb bash (To enter into Pod)

6. APPLY
	1. kubectl apply -f any-defination-file.yml 
		- (it can be pod,namespec,service,replicaset,replication controller,statefullSet,stateless,hpa,pv,pvc defination files)

7. LABEL
	1. kubectl label nodes take_slave_id_  slave1=intelliit1 
		- Above command to apply label to machine in key=value format

	2. kubectl label nodes ip-192-168-78-213.ap-southeast-1.compute.internal key1-
		- Above command to remove label from machine 

8. TAINT
	1. kubectl taint nodes take_node_id slave1=intelliit1:NoSchedule
		- To add taint on a machine 

	2. kubectl taint nodes id_machine slave2=intellit2:NoShedule-
		- To remove Taint on a machine

	3. kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
		- To see tainted machine on the cluster

9. PATCH
	1. kubectl patch svc prometheous-grafana -p '{"spec":{"type":"LoadBalancer"}}'
		- Above command to change cluster ip to LoadBalancer

10. REPLACE
	1. kubectl replace -f replicaset.yml

11. SCALE
	1. kubectl scale --replicas=1 -f replicaset.yml
		- Changing replicas opposite to desired specified in the definition file 

12. SET
	1. kubectl set image deployment/nginx-deployment mynginx=nginx:1.23
		- from above command a new version which is nginx:1.22 to nginx:1.23 will be updated 

	2. kubectl set image deployment nginx-deployment nginx:2.0.0

13. ROLLOUT
	1. kubectl rollout status deploy nginx 
		- above command will show status of the image


	1. kubectl cluster-info 
		- above command will show information about cluster 

14. CREATE
	1. kubectl create -f /tmp/metallb.yaml
	2. kubectl create deploy nginx --image nginx 

15. Version
	1. kubectl version 

16. DESCRIBE

	1. kubectl describe pod mysql-stfst-0
		- Verify the container readiness: Run the following command to check the readiness status of the container within the MySQL pod:

	1. kubectl describe pods <nginx-deployment-576c7c49b5-q4vlb >  | grep 1.23
		- To verify or describe pods info 

17. LOGS

	1. kubectl logs mysql-stfst-0
		- Check the pod logs: Use the following command to view the logs of the MySQL pod:


