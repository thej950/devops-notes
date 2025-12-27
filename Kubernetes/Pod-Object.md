Docker-kubernetes 22nd Session
---------------------------------
Content:
-------------
1.some basic kubernetes commands
2.create nginx pod using with definition files
3.created postgres pod using definition files
4.created jenkins pod using definition files
--------------------------------------------
$ kubectl get nodes
	-->from above command to see list of nodes
$ kubectl run --image nginx webserver1
	-->from above command pod called webserver1 created within the pod nginx container will be created 
$ kubectl get nodes -o wide
	-->from above commands to see more information 
	--> o --> output
$ kubectl describe pods webserver1
	-->to see the detailed information about pods
$ kubectl delete pods webserver1
	-->to delete pods
-----------------------------
$ kubectl run --image mysql mydb --env MYSQL_ROOT_PASSWORD=thej
	-->to create mydb pod within the pod mysql container
$ kubectl exec -it mydb bash
	-->to enter into pods
$ kubectl delete pods mydb
	-->to delete pods
---------------------------------
-->in kubernetes we have kubernetes definition file (or) manifest file these file used to create yml file 

-----------
apiVersion:
kind:
metadata:
spec:
--------------
-->apiVersion: it containe code library of objects like pod obeject and service object and many more
-->kind: it represents of objects
->metadata: it contains data of the data and it provide additional data which is related to pods 
-->spec: it perform main activity of pods creation and related to creation
------------------------------------------------
  kind				apiVersion
  ----				-----------
1.pod				v1
2.service				v1
3.NameSpace			v1
4.ReplicationController		v1
5.ReplicaSet			apps/v1
6.Deployment			apps/v1
7.stateFulset			apps/v1
8.secret				v1
9.PersistentVolume			v1
10.PersistentVolumeClaim		v1
11.HorizontalPodAutoscaler		v1
--------------------------------------------------------------
-->from above based on kind related object the code library will dowload 
-->apiVersion represents code library
-------------------------------------------
metadata:
----------
-->metadata is data of data is called metadata like additional information about pod 
----------------------------------------
spec:
------
-->the main steps related to pods created using these steps which image going to be download 
----------------------------------------------
$mkdir file1
$cd file1
$ vim pod-definition1.yml
=====================================
---
apiVersion: v1
kind: pod
metadata:
  name: nginx-pod
  labels:
    type: proxy
    author: intelliqit
spec:
  containers:
    - name: mynginx
      image: nginx
...
=============================
-->from above file containe nginx image to run container to inside pod (pod name is nginx-pod) 

$ kubectl apply -f pod-definition.yml
-->above command to deploy pods from above file
$ kubectl get pods
	-->to isplay pods
$ kubectl get pods -o wide
	-->to display pods with more information
$ kubectl delete -f pod-definition1.yml
	-->to delete pod-definition contain pods not file

-------------------------
$ vim pod-definition2.yml
====================================
---
apiVersion: v1
kind: pod 
metadata:
  name: postgres-pod
  labels:
    type: db
    author: intelliqit
spec:
  containers:
    - name: mydb
      image: postgres
      env:
        - name: POSTGERS_PASSWORD
          value: intelliqit
        - name: POSTGRES_USER
          value: myuser
        - name: POSTGRES_DB
          value: mydb
...
========================================
$ kubectl apply -f pod-definition2.yml
$ kubectl get pods
---------------------------------------------
$ vim pod-definition3.yml
==============================================
---
apiVersion: v1
kind: pod
metadata:
  name: jenkins-pod
  labels:
    type: ci-cd
    author: intelliit
spec:
  containers:
    - name: myjenkins
      image: jenkins/jenkins
      ports:
        - containerPort: 8080
          hostPort: 8080
...
====================================
$ kubectl apply -f pod-definition3.yml
$ kubectl get nodes
$ kubectl get nodes -o wide
-->take publicIP(external ip) of slave machine where actually jenkins-pod is runing paste on browser with :8080 port number it should be access
---------------------------------------------
-->to delete all pods
kubectl delete --all pods
-->to delete all services
kubectl delete --all svc
-->to delete all deployments 
kubectl delete --all deployments

	