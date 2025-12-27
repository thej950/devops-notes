Helm Installation
-------------------
Refer Link: https://helm.sh/docs/intro/install/
  $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    -->this command will dowload helm binary into local machine 
  $ chmod 700 get_helm.sh
    -->to change permission to excute that file 
  $ ./get_helm.sh
    -->to start install helm using that file  
===============output=======================================================
vagrant@thej-machine:~$ ./get_helm.sh
Downloading https://get.helm.sh/helm-v3.13.1-linux-amd64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /usr/local/bin
helm installed into /usr/local/bin/helm
vagrant@thej-machine:~$
==============================================================================
To verify helm 
  $ helm version 
==============output====================================
vagrant@thej-machine:~$ helm version
WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /home/vagrant/.kube/config
WARNING: Kubernetes configuration file is world-readable. This is insecure. Location: /home/vagrant/.kube/config
version.BuildInfo{Version:"v3.13.1", GitCommit:"3547a4b5bf5edb5478ce352e18858d8a552a4110", GitTreeState:"clean", GoVersion:"go1.20.8"}
vagrant@thej-machine:~$
========================================================

===============================================
1. creating a helm chart 
  $ helm create helloworld
    -->from baove command a helloworld template will be created 
    -->helm is a keyword and create also a keyword helloworld this is a custome name it can be anything 
    -->By default that template comes with nginx image only 
==================output============================
vagrant@thej-machine:~$ tree helloworld/
helloworld/
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml

3 directories, 10 files
vagrant@thej-machine:~$
=======================================================

2. To deploy that template we have to update some values in /helloworld/values.yml change desired image and service type to expose 
    $ vim /helloworld/values.yml 
    ============================
    service:
      type: NodePort
      port: 80

    ==============================

3. To install pod from helm template 
    
    $ heml install myhelloworld helloworld
        -->helm is a command 
        -->myhelloworld is a custome release name  can be anything
        -->helloworld this is a template name where it from 

================output=======================================
vagrant@thej-machine:~$ helm install myhelloworld helloworld/
NAME: myhelloworld
LAST DEPLOYED: Mon Oct 16 07:51:02 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services myhelloworld)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
vagrant@thej-machine:~$
================================================================

  $ heml list -a 
    ---> to see out put of helm 
==================output=================================
vagrant@thej-machine:~$ helm list -a
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
myhelloworld    default         1               2023-10-16 07:51:02.057663605 +0000 UTC deployed        helloworld-0.1.0        1.16.0
vagrant@thej-machine:~$
=========================================================
To Release Helm 
  $ helm uninstall myhelloworld 
    -->above command will release pods which is setup by helloworld template with name myhelloworld 

Some Basic commands For Helm 
------------------------------
  $ helm create   -->this command will create a helm template 
  $ helm install  -->this command will realease helm template with customise name like ( $ helm install myhelloworld helloworld )
  $ helm upgrade  -->this command will update latest modifications in the chart template then we perform ($ helm upgrade myhelloworldrelease helloworl ) from this i actually updating latest helm modification in existing myhelloworld with new name myhelloworldrelease based on chart hellowrld 
  $ helm rollback -->this command will rollback to previuose state of the helloworld chart for that command is like ( $ helm rollback myhelloworldrelease 1 )
  $ helm --debug --dry-run 
    --> above command will capable to debug and dry run the helm chart (hellowrld) before deploying into cluster 
    --> syntax for that is like  ( $ helm --debug --dry-run myhelloworldrelease helloworld )
    --> above command first it will interact with Kubernetes API server  
  $ helm template 
    -->above command similar to debug abd dry run but it will not interact with kubernetes API server 
    -->it will verify from in the template only command is like ( $ helm template helloworld )
  $ helm lint 
    -->above command it will find and verify any errors in helm chart wich is here helloworld 
    -->command is $ helm lint helloworld 
  $ helm uninstall 
    -->it will remove release of helm chart command is $ helm uninstall myhelloworldrelease 
  $ docker tag  --> this command will change the image name 
                --> command is like $ docker tag old_image_name New_image_name
---------------------------------
1. helm  create command will create helm template 

    $ helm create helloworld 








