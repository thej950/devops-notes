Prometheous and Grafana
------------------------

Prometheous:
------------
-> Prometheous is a monitoring tool for scrapping the performance metrics of any given hardware resources(CPU, VM, Cloud Virtual Machine, Router etc.) Prometheous scraps the data with timestamp which can be stored on a server and accessed using the PromQL
-> Prometheous has superd support API which makes Prometheous integration with any resource Present in the data center. Prometheous architecture is really scalable and 3rd party libraries and makes it more powerful. 
-> Prometheous is a Monitoring tool it will monitor and alerting are very crucial to mesures the performance metrics of an application running in production environment

-> To check target machines are available to show 
	
	localhost:9090/target



Advantages of Prometeous:
------------------------
1. Prometheous is independent framework it will not impact our cloud or in-premise infrastrucher 
2. It can be customised suite our infrastrucher needs  
3. To use Prometheous use to install required node exporter only instead of install multiple node exporters extra overhead on hardware resources 
4. Prometheous exporter can be customised to suite for our infrastrucher need, 
5. If we do not find our required node exporter in the library then prometheous allo to build our custome node exporter 
6. Prometheous comes with PromQL and TSDB which makes it more powerfull for debugging ad troubleshooting 
7. Prometheous has a build in database and you do not have to install adatabase seperatly 
8.The pull mechanism of prometheous is very flexible. and it allow to schedule variable scrappers at different intervals 
9. Prometheous is a open source it has a wide variety of community support for developers  


Node Exporter:
--------------
-> Node exporter is responsible for fetching the statics from the various hardware and virtual resources in the format of which promethous can understand 
-> with the help of prometheous server those statics can be exposed on port 9100 
-> There are many third party Node exporters which can be used by SREs as well as Devops based on their application needs. But primarly we look for these metrics ( CPU usage, Memory Usage, Disk Usage, Network Usage ) 
-> This node exporter used to collect various hardware and kernel-level metrics of our machine 




Grafana:
---------
->  Grafana is a user interface for viewing the metrics scrapped by prometheous from various resources.
-> Grafana is a open source analytics and visualization tool. Grafana does not store any data, But instead, it relies on prometheous to send the data so that dashboard can be prepared. 
-> Also Grafana is used for sending notifications and mail alerts based on various threshholds. 
-> One of the coll feature in Grafana is Garafana Labs ( In this Grafana Labs we can able download the dashboards prepared by other developers so there is not required to re-invent the wheel)

Advantages of Grafana:
-----------------------
1. If we compare Grafana with traditional tools like Zabbix and LibreNMS here Grafana allow to create multiple datasources where as Zabbix and LibreNMS allow to create single datasource 
2. In Grafana dashboard we can cutomised based on our reuirement. also we can have integration with multiple datasources in the same dashboard which is far more biggest merit of grafana 
3. In Grafana opensource community we can share our dashboard setting to others and we can get others dash boards as well from community so here we not re-invent wheel everytime 
4. Grafana Dashboard lokks good and it is allow developers to put PNG image on Dashboard for more Appealing 
5. Grafana developers community is building a lot of features and variaous datasources support which makes grafana more adaptable for various infrastrucher monitoring needs. 





Prometheous Installation:
------------------------
-> There are multiple ways to install prometehous On of the Way is below installation type 

step1: Installing Prometheous 
	
	Official website To download: https://prometheus.io/download/

	To Download Using Binary from  LTS 

		https://github.com/prometheus/prometheus/releases/download/v2.45.1/prometheus-2.45.1.linux-amd64.tar.gz

		$ mkdir prometheous
		$ cd prometheous

		$ wget https://github.com/prometheus/prometheus/releases/download/v2.45.1/prometheus-2.45.1.linux-amd64.tar.gz

	Extract this prometheous 

		$ tar xvfz prometheus-x.xx.x.linux-amd64.tar.gz

		$ cd prometheus-x.xx.x.linux-amd64.tar.gz

		$ ./prometheous

			-> Now promethous has been running access prometheous using localhost:9090 from browser 

step2: Insalling Node Exporter 
	
	Dowload Node exporte using Binary way Official website:https://prometheus.io/download/#node_exporter
	-> Look for node_exporter 

	$ mkdir node-exporter 
	$ cd node-exporter

	$ wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz

	$ tar -xvzf node_exporter-1.7.0.linux-amd64.tar.gz

	$ cd node_exporter-1.7.0.linux-amd64.tar.gz

	$ ./node_exporter 

		-> from above command node exporter are running it can be access using localhost:9100 


step3: Adding node_exporter scrap_configs To prometheous as a YAML Configurations 
	
	-> Create a file inside prometheous location where we unzip prometheous 
	-> Below file used establish connection from prometheous to nodeexporter 

	$ vim exporte-config.yaml

	++++++++++++++++++++++++++++++++++++++++++++++++++++

			global:
			   scrape_interval: 15s

			scrape_configs:
			   - job_name: node
			     static_configs:
			        - targets: ['localhost:9100','100.0.0.3:9100'] 
	
	++++++++++++++++++++++++++++++++++++++++++++++++++++++

	-> node export collects the data of current machine export to prometheos server using above file 
	-> In the scrape_configs block -> inside static_configs -> here mentioned target Two machine those two machines information will be exported to prometheous 
	-> using above file we able to setup export current machine metrics to prometheous server 

	To start the prometheous server 

		$ ./prometheus --config.file=exporter.yml

			-> from above command knows which server need to scrap 

step4: Installing Grafana
	
	-> After setup prometheous and node exporters setup grafana for to view dashboards of metrics 
	-> Grafana actually display metrics to visualize them to understand easily 

	To install Grafana from Official website: https://grafana.com/docs/grafana/latest/setup-grafana/installation/

	On ubuntu
	-----------

		$ sudo apt-get install -y apt-transport-https software-properties-common wget

		$ sudo mkdir -p /etc/apt/keyrings/

		$ wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null

		$ echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

		$ echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

		$ sudo apt-get update

		$ sudo apt-get install grafana

		$ sudo apt-get install grafana-enterprise

		$ sudo systemctl daemon-reload
		
		$ sudo systemctl start grafana-server
		
		$ sudo systemctl status grafana-server

		$ sudo systemctl enable grafana-server.service
	
	-> Now we able to access Grafana using localhost:3000/login 
	-> Default username is (admin) password (admin)

step5: To setup Grafana Dashboard
	
	-> after all installing promethous and node exporter and grafana now we need to setup dashboard for to visualize metrics 

	-> grafana is a analytics and visulaization dashboard 
	-> grafana does not store data, but istead it relies on Datasource conection towards the prometheos server it is responsible for scraping and storing the data  

	Now create Prometheous Datasource Inside Grafana Dashboard 
	------------------------------------------------------------
	-> Goto Grafana Dashboard -> goto Home-> click on connections->click Data source->click on Add data source->select prometheous-> enter hostame or Ip address of the rometheous server or localhost (http://localhost:9090)-> click on save and test 

	Now Import Dashboard From Grafana Labs
	-----------------------------------------
	-> Goto Grafana Dashboard->search as Linux memory (here get one number (2747))->now using that number 
	-> goto home->click on create->select import->type that number (2747)->click on load->click on import
	->after that we able to see memory dashboard 



==================================

To run commands in Backround 
-----------------------------
	
	$ nohup ./node_exporter > node_exporter.log 2>&1 &


