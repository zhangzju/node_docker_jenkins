# node_docker_jenkins
A repo for trying build nodejs project with docker.


## build  scripts

The first step: Excute the shell

```shell
rm -rf 	/var/lib/jenkins/jobs/node_docker_jenkins/node_docker_jenkins.tar.gz
tar -zcvf /tmp/node_docker_jenkins.tar.gz -C /var/lib/jenkins/jobs/node_docker_jenkins/workspace/docker_node . --exclude="*.git"
mv /tmp/node_docker_jenkins.tar.gz /var/lib/jenkins/jobs/node_docker_jenkins/workspace/
```

second step: Transfer the file to deploy server

```shell
docker rm -f node

mkdir /var/node
mkdir /var/node/docker_node
mkdir /var/log/pm2

rm -rf /var/node/docker_node/app.js
rm -rf /var/node/docker_node/package.json

tar -xvf /var/node_docker_jenkins.tar.gz -C /var/node/docker_node

docker run --rm -v /var/node/docker_node:/var/node/docker_node -w /var/node/docker_node/ doublespout/node_pm2 cnpm install

docker run -d --name "node" -p 8000:8000 -v /var/node/docker_node:/var/node/docker_node -v /var/log/pm2:/root/.pm2/logs/ --link redis-server:redis -w /var/node/docker_node.doublespout/node_pm2 pm2 start --no-daemon app.js

rm -rf /var/node_docker_jenkins.tar.gz
```