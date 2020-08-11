docker image tag alpine localhost:5000/myfirstimage

docker push localhost:5000/myfirstimage

docker rmi localhost:5000/myfirstimage

docker pull localhost:5000/myfirstimage

Configuration:

https://docs.docker.com/registry/configuration/

Mirror and pull-through:

https://github.com/docker/docker.github.io/blob/master/registry/recipes/mirror.md