Users
Linux  - ec2-user
CentOS - centos(use for Docker)
Ubuntu - ubuntu(use Mongo)	

Sudo riak start


Docker

Docker hub link: https://hub.docker.com/

Create a repository
Use ./docker.sh
Login
Build image
Push


Running Docker on AWS CentOS 7 (x86_64) - with Updates HVM

ssh -i cmpe281-us-west-2-oregon.pem centos@52.53.167.124
ssh -i cmpe281-nyu-aws-oregon.pem centos@52.53.167.124

Install Docker

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce
sudo systemctl start docker
sudo systemctl is-active docker
sudo systemctl start docker.service
```

```
sudo docker run -d --name user -td -p 5008:5000 rohank2002/cmpe274:user
sudo docker run -d --name product -td -p 5004:5001 rohank2002/cmpe274:productv3
sudo docker run -d --name pay  -td -p 5007:5004 rohank2002/cmpe274:pay
sudo docker run -d --name cartv2  -td -p 5001:6001 rohank2002/cmpe274:cartv2
sudo docker run -d --name review  -td -p 5005:3000 rohank2002/cmpe274:review
sudo docker run -d --name order  -td -p 5003:5003 rohank2002/cmpe274:order
sudo docker run -d --name recommender  -td -p 6001:5000 rohank2002/cmpe274:recommender
sudo docker run -d --name appriori -td -p 7001:9000 rohank2002/cmpe274:appriori



ssh -i "cmpe274.pem" ec2-user@ec2-52-10-252-71.us-west-2.compute.amazonaws.com
ssh -i "cmpe274.pem" ec2-user@ec2-34-210-43-231.us-west-2.compute.amazonaws.com
ssh -i "cmpe274.pem" ec2-user@ec2-34-215-237-121.us-west-2.compute.amazonaws.com
```


### Convenience script to install docker
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
``` 

Test Docker install
```sudo docker run hello-world```

Start docker work: 
-u arihant95  -p Arihant*1467 
```sudo docker login```

Kill Process
```sudo docker rm -f <id>```

Create and inspect network: 
```
sudo docker network create --driver bridge gumball
sudo docker network inspect gumball
sudo docker network ls
sudo docker network create -d bridge gumball-riak-kv-1
```

Run Mongo: 
```
sudo docker run --name mongodb --network gumball -d mongo
sudo docker ps -a
sudo docker exec -it mongodb bash
```

```
mongo
use cmpe281
```

-- Gumball MongoDB Collection (Create Document) --

db.gumball.insert(
	    { 
	      Id: 1,
	      CountGumballs: NumberInt(202),
	      ModelNumber: 'M102988',
	      SerialNumber: '1234998871109' 
	    }
	) ;

  -- Gumball MongoDB Collection - Find Gumball Document --

  db.gumball.find( { Id: 1 } ) ;




Run Rabbitmq:
`sudo docker run --name rabbitmq --network gumball -p 8080:15672 -p 4369:4369 -p 5672:5672 -d rabbitmq:3-management`

rabbitmqadmin declare queue name=gumball durable=false
rabbitmqadmin get queue=gumball


Login: guest / guest
Create new queue @ port 8080

Run goapi:
```
sudo docker pull arihant95/gumball:goapi-v3.0 
sudo docker run --name goapi --network gumball -td arihant95/gumball:goapi-v3.0 (tag)
sudo docker ps
```

Check connection:
```
sudo docker exec -it goapi bash 
ping mongodb
ping rabbitmq 
```

### Riak
```
docker-compose scale coordinator=1 member=3
```


### Run Cassandra:  
```
sudo docker run -d --name kong-database --network gumball cassandra:2.2
sudo docker ps
```

## Docker cassandra and kong
```
sudo docker pull cassandra:2.2
sudo docker pull kong:0.9.9
sudo docker run --name cassandra -d cassandra:2.2
```

### Run Kong:
sudo docker run -d --name kong \
--network gumball \
-e "KONG_DATABASE=cassandra" \
-e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
-e "KONG_PG_HOST=kong-database" \
-p 8000:8000 \
-p 8443:8443 \
-p 8001:8001 \
-p 7946:7946 \
-p 7946:7946/udp \
kong:0.9.9  


sudo docker run -d --name kong \
--network gumball-riak-kv-1 \
-e "KONG_DATABASE=cassandra" \
-e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
-e "KONG_PG_HOST=kong-database" \
-p 8888:8000 \
-p 8443:8443 \
-p 8001:8001 \
-p 7946:7946 \
-p 7946:7946/udp \
kong:0.9.9  



docker run -d --network gumball-riak-kv-1 --name mongo \
    -e MONGO_INITDB_ROOT_USERNAME=admin \
    -e MONGO_INITDB_ROOT_PASSWORD=admin \
    mongo

sudo docker run --name mongodb --network gumball -d mongo

docker run -d --network gumball-riak-kv-1 --name mongo \
    mongo


Add API, Plugins and test using Postman Gumball Kong (Change host to AWS public IP)

Run nodejs app in frontend:
	cd /etc
	sudo vi hosts
	change dockerhost to AWS IP address
	change key in endpoints for app.js if key auth is used 

var machine = "http://dockerhost:8000/goapi/gumball?apikey=c28afe5857ff4590a3026059533405cd";
var endpoint = “http://dockerhost:8000/goapi/order?apikey=c28afe5857ff4590a3026059533405cd";

	node app.js
Add file logger
Insert Quarter, Turn crank and process order
sudo docker exec -it kong bash
cd /tmp
cat kong.log

Add Basic Authentication (Login)

https://docs.konghq.com/hub/kong-inc/basic-auth/


Add plugin | Refer Hello->Kong Server | Plugin Add Login
Add customer | Refer Hello -> Kong Server | API Add Username
Add user and password | Refer Hello-> Kong Server | API add Password

Go to https://www.base64encode.org/ and give username:password
OR echo -n user:password | base64

Tests auth with following code | Refer Hello->Kong Server | Get Inventory (Basic Auth)
curl -X GET \
  http://52.53.167.124:8000/goapi/gumball \
  -H 'Authorization: Basic cHRnOnRlc3Q=' \
  -H 'Postman-Token: 95e96418-ac19-4971-947e-64371be803c3' \
  -H 'cache-control: no-cache'

Cloud9 + Docker(Go API gumball )

Login to cloud9
Create a new environment
Check git version
	git —version
Config git account
            git config --global user.name *****
            git config --global user.email *****
Clone git
	git clone <>
Check node version
	node —version
Install all modules
	npm install

Create AWS centOS instance
ssh -i cmpe281-us-west-1.pem centos@52.53.167.124

### Install Docker
```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce
sudo systemctl start docker
sudo systemctl is-active docker
```

Test Docker install
sudo docker run hello-world

Start docker work: sudo docker login (arihant95 / *****)

Create and inspect network: 
	sudo docker network create --driver bridge gumball1
	sudo docker network create --driver bridge gumball2

Run Mongo in gumball2: sudo docker run --name mongodb --network gumball2 -d mongo

Run Rabbitmq in gumball2: sudo docker run --name rabbitmq --network gumball2 -p 8080:15672 -p 4369:4369 -p 5672:5672 -d rabbitmq:3-management

Run goapi in gumball2: sudo docker pull preethitg/gumball:goapi-v3.0 
sudo docker run --name goapi --network gumball2 -td preethitg/gumball:goapi-v3.0

Add goapi in gumball1 network: sudo docker network connect gumball1 goapi

Run Cassandra in gumball1: sudo docker run -d --name kong-database --network gumball1 cassandra:2.2

Run Kong in gumball1: 
sudo docker run -d --name kong \
--network gumball1 \
-e "KONG_DATABASE=cassandra" \
-e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
-e "KONG_PG_HOST=kong-database" \
-p 8000:8000 \
-p 8443:8443 \
-p 8001:8001 \
-p 7946:7946 \
-p 7946:7946/udp \
kong:0.9.9  

Change app.js to point to <aws instance IP address>:8000


Cloud9 + Mongo (nodejs GumballV4)



Cloud9 + Heroku + Docker with Mongo Replica set
	Create docker-compose.yml file
	docker-compose up
	new tab
	docker ps
	sudo docker exec -it mongodb_primary bash
	mongo
	

Steps to configure replica set in three nodes:
step 1:
From node one:
db = (new Mongo('localhost:27017')).getDB('test')

step 2:
#Config for connecting three nodes together (execute this from one of the node)
config = {
  "_id": "comments",
  "members": [
    {
      "_id": 0,
      "host": "node1:27017"
    },
    {
      "_id": 1,
      "host": "node2:27017"
    },
    {
      "_id": 2,
      "host": "node3:27017"
    }
  ]
}
step 3:
#after writing the config file, we need to initiate to start replicating data of "comments" replica set

rs.initiate(config)

Step4: create db
	use cmpe281
-- Gumball MongoDB Collection (Create Document) --

db.gumball.insert(
	    { 
	      Id: 1,
	      CountGumballs: NumberInt(202),
	      ModelNumber: 'M102988',
	      SerialNumber: '1234998871109' 
	    }
	) ;

  -- Gumball MongoDB Collection - Find Gumball Document --

  db.gumball.find( { Id: 1 } ) ;



In other secondary containers do:
db = (new Mongo(‘localhost:27017’)).getDB('test')
	use cmpe281
	rs.slaveOk()
	db.gumball.find( { Id: 1 } ) ;
scp -i .<your-key-pair.pem> gumball-v1.war ec2-user@<your-host-ip>:/tmp
Riak


# Setup Riak Cluster

    http://basho.com/posts/technical/riak-on-aws-deployment-options/

    http://docs.basho.com/riak/kv/2.2.3/developing/usage/
    http://docs.basho.com/riak/kv/2.2.3/setup/installing/amazon-web-services/
    http://docs.basho.com/riak/kv/2.2.3/using/running-a-cluster/#configure-the-first-node
    http://docs.basho.com/riak/kv/2.2.3/using/cluster-operations/adding-removing-nodes/
    http://docs.basho.com/riak/kv/2.2.3/developing/usage/conflict-resolution/

    https://aws.amazon.com/marketplace/pp/B00YFZ60X2?ref=cns_srchrow

## Launch Riak Marketplace AMI (3 Nodes)

    1. AMI:             Riak KV 2.2 Series
    2. Instance Type:   t2.micro
    3. VPC:             cmpe281
    4. Network:         private subnet
    5. Auto Public IP:  no
    6. Security Group:  riak-cluster 
    7. SG Open Ports:   (see below)
    8. Key Pair:        cmpe281-us-west-1
    
    Riak Cluster Security Group (Open Ports):

	OPEN ALL PORTS!!
    
        22 (SSH)
        8087 (Riak Protocol Buffers Interface)
        8098 (Riak HTTP Interface)
    
    You will need to add additional rules within this security group 
    to allow your Riak instances to communicate. For each port range 
    below, create a new Custom TCP rule with the source set to the 
    current security group ID (found on the Details tab).
    
        Port range: 4369
        Port range: 6000-7999
        Port range: 8099
        Port range: 9080

## Launch "Jump Box" AWS Linux AMI

    1. AMI:             Amazon Linux AMI 2018.03.0 (HVM)
    2. Instance Type:   t2.micro
    3. VPC:             cmpe281
    4. Network:         public subnet
    5. Auto Public IP:  yes
    6. Security Group:  cmpe281-dmz 
    7. SG Open Ports:   22, 80, 443
    8. Key Pair:        cmpe281-us-west-1
    

## SSH into Riak Instance (via Jump Box)

    ssh -i <key>.pem ec2-user@<public ip>  (access jump box)
    ssh -i <key>.pem ec2-user@<private ip> (access riak node)
    
    
## Setup Riak Cluster Nodes (3 Nodes)

    You will need need to launch at least 3 instances 
    to form a Riak cluster. When the instances have been 
    provisioned and the security group is configured, 
    you can connect to them using SSH or PuTTY as the ec2-user.

    For all other nodes, use the internal IP address of the first node:
	sudo riak start

        sudo riak-admin cluster join riak@<ip.of.first.node>

    After all of the nodes are joined, execute the following:

        sudo riak-admin cluster plan
        sudo riak-admin cluster status
        
    If this looks good:

        sudo riak-admin cluster commit

    To check the status of clustering use:

        sudo riak-admin member_status

    You now have a Riak cluster running on AWS.

### For Go Path
```
export GOPATH=/Users/arihantsaiparuchuru/Downloads/Gumball-Practice/go-gumball-riak2/gumball
export PATH=$PATH:$GOPATH/bin
c getgo getgo.go
```



## Sample Riak Usage Example

    curl -i http://10.0.1.116:8098/buckets?buckets=true    

    curl -v -XPUT -d '{"foo":"bar"}' \
        http://10.0.1.116:8098/buckets/bucket/keys/key1?returnbody=true

    curl -i http://10.0.1.116:8098/buckets/bucket/keys/key1


### Docker-compose in CentOS
```
sudo yum install epel-release
sudo yum install -y python-pip
sudo pip install docker-compose
sudo yum upgrade python
```

### Dcoker start
```
sudo systemctl start docker
sudo systemctl is-active docker
```

### MySQL in mac
```
sudo /usr/local/mysql/support-files/mysql.server start
sudo /usr/local/mysql/support-files/mysql.server stop
sudo /usr/local/mysql/support-files/mysql.server restart
```
### Run mysql on docker
```
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=admin -d mysql:5.5
```

### connect to docker mysql
```
mysql -h 127.0.0.1 -P 3306 -u root
```

### Docker build and push image
- Build
```
docker build -t image_name .
docker build -t gumball-go
```
- Tag
```
docker tag <image_id> arihant95/<image_name>:TAG
docker tag <image_id> arihant95/gumball-go:TAG
```
- Push
```
docker push arihant95/gumball-go:TAG
```

### Start docker
```
sudo systemctl start docker.service
sudo systemctl is-active docker
```
### Pull docker
```
sudo docker pull arihant95/<name>:TAG
```

### Start docker
`sudo docker run --name gumball -p 3000:3000 -d arihant95/gumball-go:1.1`<br>

### Create and inspect network: 
```
sudo docker network create --driver bridge gumball
sudo docker network inspect gumball
sudo docker network ls
```

### Start cassandra in a network
```
sudo docker run -d --name NAME --network NETWORK_NAME cassandra:2.2

sudo docker run -d --name kong-database --network gumball cassandra:2.2
```

### Start KONG 
```
sudo docker run -d --name kong \
--network gumball \
-e "KONG_DATABASE=cassandra" \
-e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
-e "KONG_PG_HOST=kong-database" \
-p 8000:8000 \
-p 8443:8443 \
-p 8001:8001 \
-p 7946:7946 \
-p 7946:7946/udp \
kong:0.9.9
```

## Mysql install
```
sudo yum install -y mysql55-server
c
/usr/libexec/mysql55/mysql_secure_installation
sudo service mysqld start
```
## Mysql mid term
`mysql -u root -p root` <br>
`mysql -u cmpe281 -p cmpe281`


```
CREATE DATABASE CMPE281;    
CREATE USER 'cmpe281' IDENTIFIED BY 'cmpe281';
GRANT ALL PRIVILEGES ON *.* TO 'CMPE281'@'%' IDENTIFIED BY 'cmpe281' WITH GRANT OPTION;
SELECT * from information_schema.user_privileges where grantee like "'cmpe281'%";
```

##NodeJS
`nvm install v8.9.4`


### Add API to KONG
curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: f82d943d-9485-4fd5-9a16-893145eec906' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "goapi",
    "request_path": "/goapi",
    "strip_request_path": "true",
    "preserve_host": "true",
    "upstream_url": "http://go-gumball:3000"
}'

### Delete API in Kong
curl -X DELETE \
  http://localhost:8001/apis/goapi \
  -H 'Postman-Token: 83d99da5-c877-408a-9c9e-9018e258518f' \
  -H 'cache-control: no-cache'

//4dcd7265-1e94-443a-89d3-ab04e81fcda7

### Create Network
sudo docker network create --driver bridge front
sudo docker network create --driver bridge app
sudo docker network create --driver bridge db
sudo docker run --name mongo --network db -d mongo


### Connect network
docker create -it --network app --name go-gumball gumball-go-riak-1:latest

### Conect APP and DB
docker network connect db go-gumball
### Conect APP and Front
docker network connect front go-gumball
docker start go-gumball

### Cassandra and Kong
sudo docker run -d --name kong-database --network front cassandra:2.2
sudo docker run -d --name kong \
--network front \
-e "KONG_DATABASE=cassandra" \
-e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
-e "KONG_PG_HOST=kong-database" \
-p 8888:8000 \
-p 8443:8443 \
-p 8001:8001 \
-p 7946:7946 \
-p 7946:7946/udp \
kong:0.9.9  

sudo docker run --name node-gumball --network front -p 8080:8080 node-gumball:latest
https://success.docker.com/article/multiple-docker-networks




ssh -i "cmpe281-us-west-2-oregon.pem" ec2-user@ec2-34-213-40-97.us-west-2.compute.amazonaws.com
ssh -i "cmpe281-us-west-2-oregon.pem" ec2-user@10.0.2.85
10-0-2-85
10-0-2-80
10-0-2-214
sudo riak-admin cluster join riak@10.0.2.85
curl -i http://10.0.2.80:8098/buckets/orders/keys
curl -i http://10.0.2.214:8098/buckets/orders/keys

curl -i http://10.0.2.80:8098/types/gumball/buckets?buckets=true


mysql -u cmpe281 -p -h 10.0.2.109 test
mysql -u cmpe281 -p -h 10.0.2.109 test
cmpe281

### stop mongodb mac
`brew services stop mongodb` <br>

curl -X POST \
  http://localhost:3000/orders \
  -H 'content-type: application/json'

sudo docker run -d --name kong-database cassandra:2.2

sudo docker run -d --name kong \
--link kong-database:kong-database \
--link gumball-go:gumball-go \
-e "KONG_DATABASE=cassandra" \
-e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
-e "KONG_PG_HOST=kong-database" \
-p 8000:8000 \
-p 8443:8443 \
-p 7946:7946 \
-p 7946:7946/udp \
kong:0.9.9

curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: ac4b1c1e-9bdc-4c85-b77c-0f1864e63f7f' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "goapi",
    "request_path": "/goapi",
    "strip_request_path": "true",
    "preserve_host": "true",
    "upstream_url": "http://gumball-go:3000/"
}'

{"upstream_url":"http:\/\/gumball-go:3000\/","strip_request_path":true,"request_path":"\/goapi","id":"d11493b3-aba0-4bff-9746-b9209d2adae9","created_at":1555166002000,"preserve_host":true,"name":"goapi"}

```
ssh -i "cmpe281-nyu-aws-oregon.pem" ec2-user@10.0.1.169
ssh -i "cmpe281-nyu-aws-oregon.pem" ec2-user@10.0.1.104
ssh -i "cmpe281-nyu-aws-oregon.pem" ec2-user@10.0.1.7
ssh -i "cmpe281-nyu-aws-oregon.pem" ec2-user@10.0.2.215
ssh -i "cmpe281-nyu-aws-oregon.pem" ec2-user@10.0.2.172
```

```
curl -X POST \
  http://localhost:8000/goapi/order \
  -H 'apikey: d11493b3-aba0-4bff-9746-b9209d2adae9' \
  -H 'content-type: application/json'
```

sudo riak-admin bucket-type create eventbrite
sudo riak-admin bucket-type activate eventbrite

```
curl -v -XPUT -d '{"events":[{"1234":2},{"12456":12}]}' \
    http://10.0.1.169:8098/buckets/eventbrite/keys/asp?returnbody=true

curl -v -XPUT -d '{"events":[{"1235":8},{"12456":12}]}' \
    http://10.0.1.104:8098/buckets/eventbrite/keys/pbhandarkar?returnbody=true

curl -v -XPUT -d '{"events":[{"eventid":number_of_views}]}' \
    http://10.0.1.169:8098/buckets/eventbrite/keys/username?returnbody=true
```

```
curl -X GET http://10.0.1.169:8098/ping
curl -X GET http://10.0.1.104:8098/ping
curl -X GET http://10.0.1.7:8098/ping
curl -X GET http://10.0.2.215:8098/ping
curl -X GET http://10.0.2.172:8098/ping
```
```
curl -X GET http://10.0.1.169:8098/buckets/eventbrite/keys/asp
curl -X GET http://10.0.1.104:8098/buckets/eventbrite/keys/asp
curl -X GET http://10.0.1.7:8098/buckets/eventbrite/keys/asp
curl -X GET http://10.0.2.215:8098/buckets/eventbrite/keys/asp
curl -X GET http://10.0.2.172:8098/buckets/eventbrite/keys/asp
```
```
curl -X GET http://10.0.1.169:8098/buckets/eventbrite/keys/pbhandarkar
curl -X GET http://10.0.1.104:8098/buckets/eventbrite/keys/pbhandarkar
curl -X GET http://10.0.1.7:8098/buckets/eventbrite/keys/pbhandarkar
curl -X GET http://10.0.2.215:8098/buckets/eventbrite/keys/pbhandarkar
curl -X GET http://10.0.2.172:8098/buckets/eventbrite/keys/pbhandarkar
```

#### Riak Network Load Balancer Get Key-Value
```
curl -X GET http://riak-cluster-network-lb-d90a8ac266b9ee92.elb.us-west-2.amazonaws.com:80/buckets/eventbrite/keys/arihant

```

#### Network Load balancer write
```
curl -v -XPUT -d '{"posted_events":[],"booked_events":[]}' \
    http://riak-cluster-network-lb-d90a8ac266b9ee92.elb.us-west-2.amazonaws.com:80/buckets/eventbrite/keys/arihant?returnbody=true
```
```
curl -v -XPUT \
  'http://riak-cluster-network-lb-d90a8ac266b9ee92.elb.us-west-2.amazonaws.com:80/buckets/eventbrite/keys/asp?returnbody=true' \
  -H 'Content-Type: application/json' \
  -d '{
    "posted_events" : [
    	{
			"event_id" : "12345",
			"number_of_views" : 20,
			"number_of_bookings" : 30,
			"time_of_posting" : 123894943444
		},
        {
    		"event_id" : "12385",
			"number_of_views" : 20,
			"number_of_bookings" : 30,
			"time_of_posting" : 123894943444
		}
	],

	"booked_events" : [
		{
			"event_id" : "12344",
			"time_of_booking" : 123894923334
		},
        {
    		"event_id" : "12346",
			"time_of_booking" : 123894923334
		},
        {
    		"event_id" : "18344",
			"time_of_booking" : 123894923334
		}
	]
}'
```

/Users/arihantsaiparuchuru/Downloads/cmpe281/lambda-functions/getgo/
### For Go Path
```
export GOPATH=/Users/arihantsaiparuchuru/Downloads/cmpe281/lambda-functions/incrementViewUserEvent
export PATH=$PATH:$GOPATH/bin
GOOS=linux GOARCH=amd64 go build -o getgo getgo.go
```

curl -X POST \
  https://lho0n8hxa3.execute-api.us-west-2.amazonaws.com/getUserEventDetails \
  -d '{
    "bucket": "eventbrite",
    "userid": "asp"
}'

git config user.name "Arihant1467"
git config user.email "arihantsai.paruchuru@sjsu.edu"

bucket=eventbrite&username=saip

#### List all keys
```
curl -X GET http://riak-cluster-network-lb-d90a8ac266b9ee92.elb.us-west-2.amazonaws.com:80/buckets/eventbrite/keys?keys=true

curl -X GET http://10.0.2.172:8098/buckets/eventbrite/keys?keys=true
```
#### Delete Keys
curl -X DELETE http://riak-cluster-network-lb-d90a8ac266b9ee92.elb.us-west-2.amazonaws.com:80/buckets/eventbrite/keys/arihant


curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 31c81f94-450d-4a4d-aea1-026ff92b73fd' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "goapi",
    "request_path": "/goapi",
    "strip_request_path": "true",
    "preserve_host": "true",
    "upstream_url": "http://gumball:3000/"
}'

API KEY=c

curl -X GET \
http://localhost:8888/goapi/ping \
-H 'Content-Type: application/json' \
-H 'apikey: 579e40e5-ec35-4ee9-8892-3554e32b1864'

curl -X GET \
http://localhost:8888/goapi/gumball \
-H 'Content-Type: application/json' \
-H 'apikey: 579e40e5-ec35-4ee9-8892-3554e32b1864'

curl -X POST \
http://localhost:8888/goapi/order \
-H 'Content-Type: application/json' \
-H 'apikey: 579e40e5-ec35-4ee9-8892-3554e32b1864'

curl -X GET \
http://localhost:8888/goapi/order/6d32808f-2516-4170-abce-0d083373fd74 \
-H 'Content-Type: application/json' \
-H 'apikey: 579e40e5-ec35-4ee9-8892-3554e32b1864'

curl -X POST \
http://localhost:8888/goapi/order/6d32808f-2516-4170-abce-0d083373fd74 \
-H 'Content-Type: application/json' \
-H 'apikey: 579e40e5-ec35-4ee9-8892-3554e32b1864'


## Mongodb user creation 
```
db.createUser({
      user: "admin",
      pwd: "admin",
      roles: [
                { role: "userAdminAnyDatabase", db: "admin" },
                { role: "readWriteAnyDatabase", db: "admin" },
                { role: "dbAdminAnyDatabase",   db: "admin" }
             ]
  });

mongo -u admin -p admin
```

```
db.createUser({
      user: "cmpe274",
      pwd: "cmpe274",
      roles: [
                { role: "readWrite", db: "just_watch" },
             ]
});
mongo just_watch -u cmpe274 -p cmpe274
mongo 54.214.184.72/just_watch -u cmpe274 -p cmpe274
mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch

## Export Collection 
````
mongoexport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection cart --jsonArray --out cart.json
mongoexport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection order --jsonArray --out order.json
mongoexport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection payment --jsonArray --out payment.json
mongoexport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection products --jsonArray --out products.json
mongoexport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection reviews--jsonArray --out reviews.json
mongoexport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection sample --jsonArray --out sample.json
mongoexport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection users --jsonArray --out user.json


mongoexport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection products --type=csv --fields=_id,movie_id,director,year,released,genre,language,imdbRating,ProductName,ProductDesc,Image --out products.csv


mongoimport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection filters --fields _id,movie_id,director,year,released,genre,language,imdbRating,ProductName,ProductDesc,Image --type csv --file pythondata.csv

mongoimport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection filters --type json --file pythonformatted.json --jsonArray

mongoimport --uri mongodb://cmpe274:cmpe274@54.214.184.72:27017/just_watch --collection filters --type json --file productsformatted.json --jsonArray
```


db.sample.insert({"first":"Arihant","last":"sai"})
```

var eventURL= process.env.eventURL//'http://3.209.144.62:3000/'
var userURL=  process.env.userURL//'http://50.112.231.74:3000/'
var bookURL= process.env.bookURL//'http://10.0.0.234:3000/'
var dashboardURL=process.env.dashboardURL//"https://lho0n8hxa3.execute-api.us-west-2.amazonaws.com/getUserEventDetails"
var bucket = process.env.bucket//"eventbrite"

export{
    bookURL,
    userURL,
    eventURL,
    dashboardURL,
    bucket
}

curl -X POST \
 https://7g1vnr3vy6.execute-api.us-west-2.amazonaws.com/bookUserEvent \
 -H 'Content-Type: application/json' \
 -H 'cache-control: no-cache' \
 -d '{
 "bucket": "eventbrite",
 "user_uuid": "bc1d53c0-6aa8-416d-98ec-61686d2a85205",
 "orgId": "a418b7f2-1aec-4e70-a0c7-984fc12ff587",
 "eventId" : "806ef8b7-8261-459e-903e-0abed74ee",
 "eventName": "Summer bash",
 "date": "05/30/2019",
 "timeOfBooking": "25-04-2019",
 "location": "Santa Cruz,CA"
}'


curl -X POST \
  https://k3gku1lix8.execute-api.us-west-2.amazonaws.com/createUserEvent \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
  "bucket": "eventbrite",
  "user_uuid": "bc1d53c0-6aa8-416d-98ec-61686d2a8520",
  "eventId": "806ef8b7-8261-459e-903e",
  "eventName": "Summer bash",
  "location": "Fremont,CA",
  "date": "05-30-2019"
}'

```
sudo iptables -A INPUT -s 10.0.1.169 -j DROP
sudo iptables -A INPUT -s 10.0.1.104 -j DROP
sudo iptables -A INPUT -s 10.0.1.7 -j DROP
sudo iptables -A INPUT -s 10.0.2.215 -j DROP
sudo iptables -A INPUT -s 10.0.2.172 -j DROP
```

```
sudo iptables -D INPUT -s 10.0.1.169 -j DROP
sudo iptables -D INPUT -s 10.0.1.104 -j DROP
sudo iptables -D INPUT -s 10.0.1.7 -j DROP
sudo iptables -D INPUT -s 10.0.2.215 -j DROP
sudo iptables -D INPUT -s 10.0.2.172 -j DROP
```

### KONG and cassandra
```
sudo docker pull cassandra:2.2
sudo docker pull kong:0.9.9

sudo docker run -d --name kong-database \
                -p 9042:9042 \
                cassandra:2.2

sudo docker run -d --name kong \
    --link kong-database:kong-database \
    -e "KONG_DATABASE=cassandra" \
    -e "KONG_PG_HOST=kong-database" \
    -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
    -p 8000:8000 \
    -p 8443:8443 \
    -p 8001:8001 \
    -p 8444:8444 \
    kong:0.9.9
```


curl -X GET \
  http://localhost:8001/apis

curl -X GET \
  http://localhost:8001/apis/goapi/plugins

curl -X POST \
  http://localhost:8001/apis \
  -d '{
    "name": "userapi",
    "request_path": "/userapi",
    "strip_request_path": "true",
    "preserve_host": "true",
    "upstream_url": "http://40.122.67.229:5000/",
}'

curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "userapi",
    "request_path": "/userapi",
    "strip_request_path": "true",
    "preserve_host": "true",
    "upstream_url": "http://40.122.67.229:5000/"
}'

curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "userapi",
    "request_path": "/userapi",
    "strip_request_path": "true",
    "preserve_host": "true",
    "upstream_url": "http://40.122.67.229:5000/",
    "hosts" : ["cmpe281-mavericks-eventbrite.herokuapp.com"]
}'

curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "userapi",
    "request_path": "/userapi",
    "preserve_host": "true",
    "upstream_url": "http://40.122.67.229:5000/",
    "hosts" : ["cmpe281-mavericks-eventbrite.herokuapp.com"]
}'

curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "userapi",
    "request_path": "/userapi",
    "strip_request_path": "true",
    "preserve_host": "true",
    "upstream_url": "http://40.122.67.229:5000/"
}'

curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 53527c8b-ee80-47ee-8928-4c84b25aea4c' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "goapi",
    "request_path": "/goapi",
    "strip_request_path": "true",
    "preserve_host": "true",
    "upstream_url": "http://docker-gumball-internal-nlb-b1cfcb0146290f44.elb.us-west-2.amazonaws.com:8080/"
}'


{"upstream_url":"http:\/\/40.122.67.229:5000\/","strip_request_path":true,"request_path":"\/userapi","id":"a8e25a83-f6f8-421c-afb2-eff08c56de70","created_at":1556918046000,"preserve_host":true,"name":"userapi"}


curl -X GET \
  http://localhost:8001/apis/userapi/plugins \
  -H 'cache-control: no-cache'

  curl -X POST \
  http://localhost:8001/apis/userapi/plugins \
  -H 'cache-control: no-cache' \
  -H 'content-type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW' \
  -F name=key-auth

curl -X POST \
http://localhost:8001/apis/userapi/plugins \
    --data "name=key-auth"

curl -X POST http://localhost:8001/apis/userapi/plugins \
    --data "name=key-auth"

curl -X GET \
  http://localhost:8001/apis/userapi \

curl -X GET \
  http://localhost:8001/apis/userapi

curl http://localhost:8000/apis/userapi/users \
    -H "Host: http://40.122.67.229:5000/"
    -H 'apikey: 624ab11d-859b-4789-a6a0-a972543d2c45'

curl -i -X POST \
  --url http://localhost:8001/apis/ \
  -d 'name=sample' \
  -d 'upstream_url=http://40.122.67.229:5000/' \
  -d 'request_host=localhost' \
  -d 'request_path=/sample'

  {"upstream_url":"http:\/\/40.122.67.229:5000\/","strip_request_path":false,"request_path":"\/sample","id":"aa75da0a-7527-49a9-ae1a-388bf8dd85fe","created_at":1556919187000,"preserve_host":false,"name":"sample","request_host":"localhost"}

  curl -i -X GET \
  --url http://localhost:8000/sample/users/ \
  --header 'Host: localhost'

  curl -i -X GET \
  --url http://localhost:8000/sample/users

  curl -X DELETE \
  http://localhost:8001/apis/sample \
  -H 'Postman-Token: cf2ba3c5-ea0c-4153-8307-614bb21198f9' \
  -H 'cache-control: no-cache'

  curl -X POST \
  http://localhost:8001/apis \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 3fe96ce4-92a7-44c8-bd5c-b97d043a1281' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "userapi",
    "request_path": "/userapi",
    "strip_request_path": "true",
    "upstream_url": "http://40.122.67.229:5000/"
}'

{"upstream_url":"http:\/\/40.122.67.229:5000\/","strip_request_path":true,"request_path":"\/userapi","id":"e13d5fe0-ebcc-476d-9854-84c07e42c589","created_at":1556920807000,"preserve_host":false,"name":"userapi"}
