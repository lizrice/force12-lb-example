# force12-lb-example
Load balanced web server that can be scaled by Force12

This project was hugely influenced by and copies chunks from [bellycard/docker-loadbalancer](http://github.com/bellycard/docker-loadbalancer). 

## Getting started

You'll need Docker Toolbox installed and on Mac or Windows you'll need to start a Docker VM: 

```
docker-machine create -d virtualbox force12
docker-machine start force12
docker-machine env force12
eval "$(docker-machine env force12)"
```

Note your docker host IP address (the address, but not the port, from $DOCKER_HOST).

## Bring up infrastructure

The docker-compose.yml file runs containers for nginx, consul and registrator. 

```
docker-compose up
```

## Run Microscaling-in-a-Box

Sign up / log in to [Microscaling-in-a-Box](http://app.force12.io) and configure the high-priority task to run your web server by setting 
the container name to force12/simplewebserver:latest

Run the command as directed on the microscaling Run page.  

You should be able to see a very simple web server at the IP address you noted earlier. The nginx load balancer is using its default round-robin, 
which means that if you've got more than one web server container running, they will take it in turns to serve the web page, so you can see
the container ID for each web server.  You can compare this to the number of web server containers you can see running on the Microscaling-in-a-Box web site.

[![Microscaling a web server in action](http://img.youtube.com/vi/AuXvYNRUpJ0/0.jpg)](http://www.youtube.com/watch?v=AuXvYNRUpJ0)

If microscaling is set up to allow 0 high-priority tasks, you might have no web servers running, in which case you'll see an nginx 502 error.  

## See services stored in Consul

Take a look at what's registered in Consul like this:
```
curl http://<docker host ip address>:8500/v1/catalog/services | python -m json.tool
```
View the containers registered for each service like this (use this as another way to see how many web server containers are in play):
```
curl http://<docker host ip address>:8500/v1/catalog/service/<name, e.g. app> | python -m json.tool
```

### Build your own version of the web server container

```
docker build -t f12web hello-world
```

You need to tell the Force12 client not to try to pull this image from the repository by adding the F12_PULL_IMAGES=false flag when you run it.

```
docker run -e "F12_USER_ID=5k4ek" -e "F12_PULL_IMAGES=false"  -v "/var/run/docker.sock:/var/run/docker.sock:rw" -it force12io/force12:latest
```



