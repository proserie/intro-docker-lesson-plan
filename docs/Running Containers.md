  
**Running Containers**

We will go through the process of installing a fully functional ELK Stack (Hurray Elasticsearch). 

First we get up elasticsearch since this is the most important piece. We run the command below to do just that.
path

Linux/Unix

`docker run --name elasticsearch -p 9200:9200 -v "$(pwd)/src/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml" --hostname es  -d docker.elastic.co/elasticsearch/elasticsearch:6.2.3`

Windows

`docker run --name elasticsearch -p 9200:9200 -v "%cd%/src/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml" --hostname es  -d docker.elastic.co/elasticsearch/elasticsearch:6.2.3`

Note: At this point docker will go ahead and do what it needs to do to create the container, what you see is docker building the image. Each individual piece is called "Layers". More on layers can be found on this quick article. [Docker Layers](https://medium.com/@jessgreb01/digging-into-docker-layers-c22f948ed612)

The above command should take maybe a minute or 2 to complete. once done you will see something similar to the below when you run `docker ps`

```text
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                              NAMES
f6486f532f34        docker.elastic.co/elasticsearch/elasticsearch:6.2.3   "/usr/local/bin/dock…"   42 minutes ago      Up 42 minutes       0.0.0.0:9200->9200/tcp, 9300/tcp   elastic
```

We can do all of our checks to make sure everything is good. We can see the ports listen on 0.0.0.0:9200 on the host to 9200 on the container.

Next for our ELK Stack we will get up logstash. For this test logstash will take json input and output to elasticsearch. We run the below command to spin up logstash.


Linux/Unix 

`docker run --name logstash -p 10000:10000 -v "$(pwd)/src/logstash/pipelines/:/usr/share/logstash/pipeline/" docker.elastic.co/logstash/logstash:6.2.3`


Windows

`docker run --name logstash -p 10000:10000 -v "%cd%/src/logstash/pipelines/:/usr/share/logstash/pipeline/" docker.elastic.co/logstash/logstash:6.2.3`

Logstash should successfully run but will fail. Lets take a look at see why. If we cat the pipeline file we have the below input

```text
input {
  tcp {
    port => 10000
    codec => json
  }
}
filter {}
output {
  elasticsearch {
    hosts => "127.0.0.1:9200"
  }
}
```

The reason logstash failed is because the elasticsearch output 127.0.0.1 is for the local container not 127.0.0.1 on the host. To do container to container traffic we need to create a container network.

First we check what interfaces we have on our local machines and used that docker interface to create a new swarm network

`docker swarm init --advertise-addr 10.1.0.2`

`docker network create --subnet 10.1.0.0/24 --gateway 10.1.0.1 --attachable --opt encrypted -d overlay devnet`

Once our network is created let's destroy our elastic container and spin up a new one in the new network (yes we can just add it to the network but where's the fun in that)

`docker stop elasticsearch` then `docker rm elasticsearch`


Linux/Unix

`docker run --name elasticsearch -p 9200:9200 -v "$(pwd)/src/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml" --network devnet  -d docker.elastic.co/elasticsearch/elasticsearch:6.2.3`


Windows

`docker run --name elasticsearch -p 9200:9200 -v "%cd%/src/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml" --network devnet  -d docker.elastic.co/elasticsearch/elasticsearch:6.2.3`


Now let us spin up out logstash container and get a shell and ping our elastic container.

`docker run -it --rm  --network devnet docker.elastic.co/logstash/logstash:6.2.3 /bin/bash`

When you get the shell ping the elastic container `ping elasticsearch`

We know it works so lets exit that shell then go into the path logstash pipeline file and change `127.0.0.1` to `elasticsearch`, after that we start up a logstash container like so

Linux/Unix

`docker run --name logstash -p 10000:10000 -v "$(pwd)/src/logstash/pipelines/:/usr/share/logstash/pipeline/" --network devnet -d docker.elastic.co/logstash/logstash:6.2.3`

Windows

`docker run --name logstash -p 10000:10000 -v "%cd%/src/logstash/pipelines/:/usr/share/logstash/pipeline/" --network devnet -d docker.elastic.co/logstash/logstash:6.2.3`

Now that that is successful we will spin up kibana


Linux/Unix

`docker run -p 5601:5601 --network devnet -d  -v "$(pwd)/src/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml" docker.elastic.co/kibana/kibana:6.2.3`


Windows

`docker run -p 5601:5601 --network devnet -d  -v "%cd%/src/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml" docker.elastic.co/kibana/kibana:6.2.3`

Bonus command

`curl -XPOST -d @src/fakedata/fakedata.json 127.0.0.1:10000`
