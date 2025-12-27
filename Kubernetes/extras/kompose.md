kompose: docker kubernetes 25th session 13:00 min to 20:00 min
--------------
-->kompose is tool which it can create kubernetes definitio file by help of docker compose file
step1:create docker compose file for mysql ad wordpress 
$vim docker-compose.yml
=====================
version: '3.8'
services:
  mydb:
    image: mysql
    evironment:
        MYSQL_ROOT_PASSWORD: intelliqit
  wordpress:
    image: wordpress
    ports:
      - 80:8080
    deploy:
       replicas: 3
============================
-->from above file kompose help to convert kubernets definitoion file to set pod architecture in kubernetes mysql and wordpress enviroment
$kompose convert
-->from above command 3 kubernetes definition files were generated to setup kubernets pod architecture for mysql and wordpress
1.mywordpress-deployment.yml
2.mydb-deployment.yml
3.mywordpress-service.yml
---------------------------------------------------------
