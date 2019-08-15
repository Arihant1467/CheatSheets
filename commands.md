#### Start Jenkins
```
java -jar jenkins.war --httpPort=8080
Browse to http://localhost:8080
```

#### Build Docker file
```
docker build -t docker_account_id/image_name:TAG .
```
#### Build Docker alpine
```
docker build -t droplets_web -f Dockerfile.alpine .
```

### Docker cli
```
docker login
docker images
docker ps
docker ps -a
docker pull docker_account_id:TAG
```

#### NGINX Proxy-server
```
docker run -d --name nginx-proxy -p 80:80 -v /var/run/docker.sock:/tmp/docker.sock jwilder/nginx-proxy
docker run -d --name nodeapp-1 --expose 3500 -e VIRTUAL_HOST=nodeapp-one.localhost arihant95/node-sample:1.0
docker run -d --name nodeapp-2 --expose 3500 -e VIRTUAL_HOST=nodeapp-two.localhost arihant95/node-sample:2.0
```

#### Node sample app url
```
http://nodeapp-one.localhost/
http://nodeapp-two.localhost/
```
#### Curl into the virtual host
```
curl -H "Host: nodeapp-one.localhost" localhost
curl -H "Host: nodeapp-two.localhost" localhost
```


#### NGINX Proxy-server
```
docker run -d --name nginx-proxy -p 5000:80 -v /var/run/docker.sock:/tmp/docker.sock jwilder/nginx-proxy:latest
docker run -d --name nodeapp-1 --expose 3500 -e VIRTUAL_HOST=nodeapp-one.localhost arihant95/node-sample:1.0
docker run -d --name nodeapp-2 --expose 3500 -e VIRTUAL_HOST=nodeapp-two.localhost arihant95/node-sample:2.0
```

#### Node sample app url
```
http://nodeapp-one.localhost:5000/
http://nodeapp-two.localhost:5000/
```

#### Curl into the virtual host
```
curl -H "Host: nodeapp-one.localhost" localhost:5000
curl -H "Host: nodeapp-two.localhost" localhost:5000
```

#### Stop docker process
```
docker stop nginx-proxy
docker stop nodeapp-1
docker stop nodeapp-2
```

#### Remove docker process
```
docker rm nginx-proxy
docker rm nodeapp-1
docker rm nodeapp-2
```

#### Remove docker Images
```
docker rmi jwilder/nginx-proxy
docker rmi arihant95/node-sample:1.0
docker rmi arihant95/node-sample:2.0
```

#### Run docker inside a docker network
```
docker run -d --name droplets_web --network=aparuchu_droplets-doc_default -p 80:80 droplets_web:latest
```
#### Run NGINX with custom configuration
```
docker run -d --name droplets_web -v /Users/aparuchu/Perforce/aparuchu_djt_docker_test/config/nginx.conf:/etc/nginx/nginx.conf -p 80:80 -p 443:443 --network=aparuchu_droplets-doc_default droplets_web:latest

docker run -d --name droplets_web -v /Users/aparuchu/Perforce/aparuchu_djt_docker_test/config/nginx.conf.prod:/etc/nginx/nginx.conf -p 80:80 -p 443:443 --network=aparuchu_droplets-doc_default droplets_web:latest
```

#### Test REST API Call using wget
```
wget  -qSO - http://localhost:80/api/version
```

#### Golang setup
```
export GOPATH=$HOME/path to your go project
export PATH=$PATH:$GOPATH/bin
```

#### Go Lang project structure
```
Main_Golang_project
|
|- bin/
|       hello # command executable
|- src/
|       hello/
|               hello.go
|       stringutil/
|               reverse.go
```


#### Building Golang Binaries
```
cd ./src/:project_folder
go build
./:project_folder
```

#### Installing Go libraries (locally)
```
go get 
```


#### Droplets versioning on devbld/droplets
```
docker run -d --name nginx-proxy -p 5000:80 --network=droplets_doc_versioning -v /var/run/docker.sock:/tmp/docker.sock jwilder/nginx-proxy:latest

docker run -d --name droplets-backend --network=droplets_doc_versioning arihant95/droplets-backend:1.0

docker run -d --name r32-droplets --network=droplets_doc_versioning -e VIRTUAL_HOST=r32.droplets.informatica.com quay.io/infacloud/droplets-web:proxy-working

docker run -d --name r33-droplets --network=droplets_doc_versioning -e VIRTUAL_HOST=r33.droplets.informatica.com quay.io/infacloud/droplets-web:proxy-working

docker run -d --name r34-droplets --network=droplets_doc_versioning -e VIRTUAL_HOST=r34.droplets.informatica.com quay.io/infacloud/droplets-web:proxy-working
```


#### On your browser
```
http://r32.droplets.informatica.com:5000/
http://r33.droplets.informatica.com:5000/
http://r34.droplets.informatica.com:5000/
```

#### Curl into the above created docker containers
```
curl -H "Host: r32.droplets.informatica.com" localhost:5000
curl -H "Host: r33.droplets.informatica.com" localhost:5000
curl -H "Host: r34.droplets.informatica.com" localhost:5000
```

#### NGINX template.conf
Location
/etc/nginx/conf.d/default.conf

```
server {
	server_name _; # This is just an invalid value which will never trigger on a real hostname.
	listen 80;
	access_log /var/log/nginx/access.log vhost;
	return 503;
}
# r32.droplets.informatica.com
upstream r32.droplets.informatica.com {
				## Can be connected with "droplets_doc_versioning" network
			# r32-droplets
			server 192.168.48.4:80;
}
server {
	server_name r32.droplets.informatica.com;
	listen 80 ;
	access_log /var/log/nginx/access.log vhost;
	location / {
		proxy_pass http://r32.droplets.informatica.com;
	}
}
# r33.droplets.informatica.com
upstream r33.droplets.informatica.com {
				## Can be connected with "droplets_doc_versioning" network
			# r33-droplets
			server 192.168.48.5:80;
}
server {
	server_name r33.droplets.informatica.com;
	listen 80 ;
	access_log /var/log/nginx/access.log vhost;
	location / {
		proxy_pass http://r33.droplets.informatica.com;
	}
}
```

#### Ping the instances
```
curl http://r32.droplets.informatica.com:5000
curl http://r33.droplets.informatica.com:5000
curl http://r34.droplets.informatica.com:5000
```

#### Settings of /etc/hosts settings
10.23.148.147 r32.droplets.informatica.com r33.droplets.informatica.com r34.droplets.informatica.com

#### To stop and remove droplets versioned containers
```
docker stop r32-droplets && docker rm r32-droplets
docker stop r32-droplets r33-droplets r34-droplets && docker rm r32-droplets r33-droplets r34-droplets
```

#### To exec on nginx-proxy container
```
docker exec -it nginx-proxy /bin/sh
```

#### Start nginx-proxy docker container and volume nginx.conf file
```
docker run -d --name nginx-redirect -p 80:80 --network=aparuchu_droplets-doc_default -v /Users/aparuchu/Perforce/aparuchu_djt_docker_test/config/sample.nginx.conf:/etc/nginx/nginx.conf nginx
```

#### Editing hosts file
```
sudo vim /etc/hosts
127.0.0.1 r32.example.com r33.example.com example.com
```

#### To stop nginx-redirect container
```
docker stop nginx-redirect && docker rm nginx-redirect
```

#### To inspect on network settings of container
```
docker inspect --format '{{ .NetworkSettings.IPAddress }}' nginx-redirect
docker inspect --format '{{ .NetworkSettings.IPAddress }}' r32-droplets
```

#### Running nginx-reverse-proxy and docker GEN as an individual container
```
docker run -d --name nginx-gen --volumes-from nginx -v /var/run/docker.sock:/tmp/docker.sock -v /Users/aparuchu/Perforce/aparuchu_djt_docker_test/config/sample.nginx.tmpl:/etc/docker-gen/templates/nginx.tmpl -t jwilder/docker-gen -notify-sighup nginx -watch -only-exposed /etc/docker-gen/templates/nginx.tmpl /etc/nginx/nginx.conf


docker run -d --name nginx-gen --volumes-from nginx -v /var/run/docker.sock:/tmp/docker.sock -v /Users/aparuchu/Perforce/aparuchu_djt_docker_test/config/sample.nginx.tmpl:/etc/docker-gen/templates/nginx.tmpl -t jwilder/docker-gen -notify-sighup nginx -watch -only-exposed /etc/docker-gen/templates/nginx.tmpl /etc/nginx/conf.d/default.conf


docker run -d --name nginx-gen --volumes-from nginx -v /var/run/docker.sock:/tmp/docker.sock:ro -v /Users/aparuchu/Perforce/aparuchu_djt_docker_test/config/nginx.tmpl:/etc/docker-gen/templates/nginx.tmpl -t jwilder/docker-gen --notify-sighup nginx -watch /etc/docker-gen/templates/nginx.tmpl /etc/nginx/conf.d/default.conf
```

#### Creating a virtual Docker bridge network (not in ingress mode)

```
docker network create -d bridge aparuchu_droplets-doc_default
docker network create -d bridge droplets_doc_versioning
```
#### List all virtual networks on dockerhost
```
docker network ls
```

#### Running droplets-proxy inside a virtual docker network
```
docker run -d --name droplets-proxy --network=aparuchu_droplets-doc_default -p 80:80 -v /var/run/docker.sock:/tmp/docker.sock:ro droplets-proxy:latest

docker run -d --name droplets-proxy --network=droplets_doc_versioning -p 80:80 -v /var/run/docker.sock:/tmp/docker.sock:ro droplets-proxy:latest
```

#### Nginx conf file
```
cat /etc/nginx/nginx.conf
cat /etc/nginx/conf.d/default.conf 
```

#### Launching versioned droplets-web
```
docker run -d --name droplets-r32 --network=droplets_doc_versioning -e VIRTUAL_HOST=r32.droplets.informatica.com quay.io/infacloud/droplets-web:r32

docker run -d --name droplets-r33 --network=droplets_doc_versioning -e VIRTUAL_HOST=r33.droplets.informatica.com quay.io/infacloud/droplets-web:r33

docker run -d --name droplets-latest --network=droplets_doc_versioning -e VIRTUAL_HOST=droplets.informatica.com quay.io/infacloud/droplets-web:latest
```

#### Launching droplets-web Backend (One backend for N Versions)
```
docker run -d --name droplets-backend --network=droplets_doc_versioning quay.io/infacloud/droplets-backend:1.0
```

#### Droplets versioned set of conatiners
```
docker run -d --name droplets-nginx --network=droplets_doc_versioning -p 5000:80 -p 5001:443 -v /var/run/docker.sock:/tmp/docker.sock quay.io/infacloud/droplets-nginx-proxy:1.0

docker run -d --name r33-droplets --network=droplets_doc_versioning -e VIRTUAL_HOST=r33.droplets.informatica.com quay.io/infacloud/droplets-web:r33

docker run -d --name latest-droplets --network=droplets_doc_versioning -e VIRTUAL_HOST=droplets.informatica.com quay.io/infacloud/droplets-web:latest
```

#### TAG a docker images
```
docker tag droplets-nginx-proxy:latest quay.io/infacloud/droplets-nginx-proxy:latest
```


 ### Running Mini IO (Open source S3 storage)
```
docker pull minio/minio:latest
docker run -d --name minio -p 9000:9000 -e "MINIO_ACCESS_KEY=QQZLXZWXVGNSLSFNCJCT" -e "MINIO_SECRET_KEY=BioFe9tPg5UqUDHuUkjLmXEwMErZtfXsUoVZ7lPD" -v /Users/aparuchu/Downloads/playground-s3-buckets:/data minio/minio:latest server /data
```

