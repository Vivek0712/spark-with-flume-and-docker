# spark-with-flume-and-docker

This is a spark toy which deploying docker containers in multi-hosts environment, combining with flume to collect the streaming data from meetup.com and implement spark streaming with word count and store the result to mongodb container

<br/><br/>

* __Starting docker daemon with modifying /etc/default/docker:__

    <pre>DOCKER_OPTS="-H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --cluster-store consul://MASTER_IP:8500 --cluster-advertise eth1:2376"</pre>

* __Run bootstrap_docker.sh to start consul and swarm container in your master node__

* __Run bootstrap_slave.sh in all of your slave nodes__

* __Mount shared/ in every slave nodes by using sshfs:__

    <pre>sshfs -o allow_other USER@MASTER_IP:PATH/TO/SHARED shared</pre>

* __Create a overlay network in your master node, for example I created a overlay network named 'my-net':__

    <pre>docker network create --driver overlay MY-MULTI-HOST-NETWORK</pre>

* __Create a environment variable to directly control swarm in master node:__

    <pre>export DOCKER_HOST=tcp://MASTER_IP:4000</pre>

* __Run docker compose to set up multi-host containers in swarm, it will automatically deploy containers in nodes:__

    <pre>docker-compose scale namenode=1 datanode=WHATEVER_YOU_WANT mongodb=1 flume=1</pre>

* __Run post.py to start collecting data from meetup.com to flume:__

    <pre>python post.py FLUME_URL:8001 </pre>

* __Run flumecount.sh which will store the flume wordcount result into mongodb, but make sure you already create database in mongodb__
