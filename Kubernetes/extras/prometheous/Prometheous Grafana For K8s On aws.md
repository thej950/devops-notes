Prometheous Grafana For K8s On aws
------------------------------------
1. Pre-Requisite
	
	-> awscli
	-> eksctl
	-> kubectl
	-> Helm Chart

step1: Installing awscli on ubuntu 
	
	$ sudo apt install awscli -y

	$ awscli --version

step2: Installing eksctl 
	
	$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
	
	$ sudo mv /tmp/eksctl /usr/local/bin

	$ eksctl --version 

step3: setup aws credentials 
	
	-> For to setup aws credentials into local machine create a access key and secretkey to accesskey authenticate awscloud to access 

		$ aws configure
		AWS Access Key ID [None]: <ENTER_YOU_ACCESS_KEY>
		AWS Secret Access Key [None]: <ENTER_ACCESS_KEY>
		Default region name [None]: <ENTER_THE_REGION>
		Default output format [None]: <json or text> 

step4: Install kubectl (kubernetes command line ) 
	
	-> Kubectl command used to manage the kubernetes cluster Remotely 

		$ curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"

		$ chmod +x ./kubectl 

		$ sudo mv ./kubectl /usr/local/bin
		
		$ kubectl version

step5: Istalling Helm 
	
	-> Using helm chart we able to install Prometeous and grafana and ode exporter on K8s 
	-> To setup Prometheous and Grafana on Kuberenetes cluster Recommended to Go with Helm repository so here do not miss any configuration setup 
	-> Helm Chart work out of the box ad it will take care of everything for us installing (prometheous-altmanager, prometheous-server, prometheous-operator)

		$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
		
		$ chmod 700 get_helm.sh
		
		$ ./get_helm.sh


step6: Setup K8s in aws using eksctl 
	
	To create k8s cluster 

		$ eksctl create cluster --name test-cluster-1 --version 1.22 --region eu-central-1 --nodegroup-name worker-nodes --node-type t2.large --nodes 2 --nodes-min 2 --nodes-max 3

			From above command 
			eksctl create cluster -> this will tell eksctl to create k8s cluster 
			--name test-cluster-1 -> this will assigne name to the cluster
			--version 1.22    	  -> this will set version To download k8s 
			--region eu-central-1 -> To define region to create k8s cluster 
			--nodegroup-name worker-nodes	  -> To define worker node 
			--node-type			  -> To define type of node to ceate 
			--nodes 2 			  -> this will tell how many nodes to create inside cluster 
			--node-min 2 		  -> It will define minimum number of nodes inside cluster
			--node-max 3 		  -> It will define maximum number of nodes inside cluster 

			Note: It will take minimum 15 minuites to create cluster On AWS 

step7: Install Kubernetes Metric Server 
	
	-> By perming Installation of the Kubernetes Metric Server Into the k8s cluster so that Prometheous can collect the performance metrics of K8s 
	-> Kubereetes metrics server collects the resource metrics from kubelets and exposes it to kuberenetes api server trough metric api for use by Horizontal Pod Autoscaler. 

	The Kuberenetes Metrics Offers
	------------------------------
	-> Fast Autoscaling Collecting Metrics 
	-> Scalable supports upto 5000 node clusters 
	-> Resources Efficiency and it requires very less CPU(1 milli core) and less memory(2 mb)

	Installation Of Kuberenetes Metrics 
	------------------------------------

		$ kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
		
	To verify Kuberenetes Metric server 
	------------------------------------

		$ kubectl get deployment metrics-server -n kube-system (or) kubectl get pods -n kube-system 

step8: Install Prometheous 
	
	-> Install prometheous using Helm Chart in cluster 

		$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts 

		$ helm repo update 

		$ helm repo list 

		$ kubectl create namespace prometheus

		$ helm install prometheus prometheus-community/prometheus --namespace prometheus --set alertmanager.persistentVolume.storageClass="gp2" --set server.persistentVolume.storageClass="gp2" 

		$ kubectl get all -n prometheous 

	To view Prometheous Dashboard 
	----------------------------------

		$ kubectl port-forward deployment/prometheus-server 9090:9090 -n prometheus

step9: Install Grafana 
	
	-> after complete installation of prometheous lets install grafana 

		$ helm repo add grafana https://grafana.github.io/helm-charts 

		$ helm repo update 

	-> Now create Datasource in prometheous so that grafana can access the kuberenetes metrics 

		$ vim prometheus-datasource.yaml
		------------------------------------
		datasources:
		  datasources.yaml:
		    apiVersion: 1
		    datasources:
		    - name: Prometheus
		      type: prometheus
		      url: http://prometheus-server.prometheus.svc.cluster.local
		      access: proxy
		      isDefault: true

		----------------------------------------

	-> Lets create Grafana namespace 

		$ kubectl create namespace grafana

	-> Install Grafana 

		$ helm install grafana grafana/grafana \
		    --namespace grafana \
		    --set persistence.storageClassName="gp2" \
		    --set persistence.enabled=true \
		    --set adminPassword='EKS!sAWSome' \
		    --values prometheus-datasource.yaml \
		    --set service.type=LoadBalancer 

	-> To verify Grafana Installation 

		$ kubectl get all -n grafana

	-> To access Grafana Dashboard Find Loadbalanacer IP 

		$ kubectl get service -n grafana 

step10: Now Import Grafana Dashboard From Grafana Labs 
	
	-> Upto Now we setup everything like installation of prometheous and grafana and To access Grafana From browser Now we Required to Import Dashboard for customisation 
	-> To Customisation dashboard there are multiple Dashboards are avaiable so Go into open source grafana dashboard search one good one dashboard for our Kuberenernets cluster 
	-> so here Im going to take One dashboard that container ( 6417 ) 

		=>GoTo->Dashboard ->Click import->type (6417)->select prometheous->click on import 

step11: Lets Deploy a Spring Boot microservices and monitor it on Grafana 
	
	$ vim k8s-spring-boot-deployment.yml 
	---------------------------------------------
		apiVersion: apps/v1
		kind: Deployment
		metadata:
		  name: jhooq-springboot
		spec:
		  replicas: 2
		  selector:
		    matchLabels:
		      app: jhooq-springboot
		  template:
		    metadata:
		      labels:
		        app: jhooq-springboot
		    spec:
		      containers:
		        - name: springboot
		          image: rahulwagh17/kubernetes:jhooq-k8s-springboot

		          resources:
		            requests:
		              memory: "128Mi"
		              cpu: "512m"
		            limits:
		              memory: "128Mi"
		              cpu: "512m"

		          ports:
		            - containerPort: 8080

		          readinessProbe:
		            httpGet:
		              path: /hello
		              port: 8080
		            initialDelaySeconds: 15
		            periodSeconds: 10

		          livenessProbe:
		            httpGet:
		              path: /hello
		              port: 8080
		            initialDelaySeconds: 15
		            periodSeconds: 10

		          startupProbe:
		            httpGet:
		              path: /hello
		              port: 8080
		            failureThreshold: 30
		            periodSeconds: 10

		          env:
		            - name: PORT
		              value: "8080"
		---
		apiVersion: v1
		kind: Service
		metadata:
		  name: jhooq-springboot
		spec:
		  type: NodePort
		  ports:
		    - port: 80
		      targetPort: 8080
		  selector:
		    app: jhooq-springboot
		Copilot is ready for this page
		Summary
		Copilot
		raw.githubusercontent.com
		Ask a follow-up question
		GPT-3.5
		GPT-4
	------------------------------------- 

		$ kubectl apply -f k8s-spring-boot-deployment.yml 

	-> To verify spring boot application 

		$ kubectl get deployment jhooq-springboot


============================================================================

Reference website: https://jhooq.com/prometheous-k8s-aws-setup/
