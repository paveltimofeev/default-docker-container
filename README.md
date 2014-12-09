##Dockerfile

### Base Docker Image
* [dockerfile/java:oracle-java7](http://dockerfile.github.io/#/java)

### Usage
#### First boot / in case of update
install docker on Ubuntu
```
sudo apt-get update
sudo apt-get install docker.io
```
pull and build image
```
sudo docker pull paveltimofeev/default-docker-container
sudo docker build -t="paveltimofeev/default-docker-container" github.com/paveltimofeev/default-docker-container
```

#### On every boot
run image at port 8087 with ES_CLUSTER_NAME = 'MyClusta'
```
sudo docker run -d -p 8087:9200 -p 9300:9300 -e "ES_CLUSTER_NAME=MyClusta" -e "AWS_REGION=us-east-1" paveltimofeev/default-docker-container
```
		
#### How to check results
show container's ID and Status
```
sudo docker ps -l
```
show container logs by its ID
```
sudo docker logs <container_ID>
```

#### How to remove *all* installed containers: 
```
sudo docker rm $(docker ps -a | awk '{print $1}')
```	
	
#### Attach persistent/shared directories
  1. Create a mountable data directory `<data-dir>` on the host.
  2. Create ElasticSearch config file at `<data-dir>/elasticsearch.yml`.

    ```yml
    path:
     data: /data/data
     logs: /data/log
     plugins: /data/plugins
     work: /data/work
   discovery:
     zen:
       ping:
         multicast:
           enabled: false
         timeout: 360
       fd:
         ping_retries: 5
     type: ec2
   bootstrap:
     mlockall: true
    ```

  3. Start a container by mounting data directory and specifying the custom configuration file:

    ```sh
    docker run -d -p 8088:9200 -p 9300:9300 -v <data-dir>:/data paveltimofeev/default-docker-container /elasticsearch/bin/elasticsearch -Des.config=/data/elasticsearch.yml
    ```

After few seconds, open `http://<host>:8088` to see the result.
