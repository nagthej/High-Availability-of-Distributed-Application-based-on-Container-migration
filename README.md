# High-Availability-of-Distributed-Application-based-on-Container-migration


Docker uses a client-server based architecture. The Docker client communicates with the Docker daemon- which builds,
runs and distributes containers. The Docker client and daemon can run on same host, or we can connect Docker client to a
remote Docker daemon machines. The client and daemon exchanges information using REST API, which is on top of
UNIX sockets or on a Network interface.

Summary:

• Developed an Innovative model to migrate a web application running inside a container from one host to another.
• Using Docker 17.06.0-ce as a container platform, joined multiple hosts in a multi-manager configuration to form a cluster. 
• We run Discovery service called consul in the backend to monitor the health status of hosts. 
• Then we checkpoint and restore using CRIU by leaving the application running on the main node.  
• Finally, we add High availability to the application by achieving Failover.
Also we extended the application to Nvidia-DIGITS using Nvidia-docker wrapper tool.

=>Detailed explanation of the project is in Google drive, please request for permission to only view the documentation:
  https://drive.google.com/open?id=0B2UkVVe1wNckY19vTmhHc1VYZnc

=> Detailed procedure is explained in Report.pdf in code section of this repository (Author Qicang Qiu contributed to 
DIGITS application part- Object detection and classification and not to any of code files in this repository of the project).

=>Dockerizing a web application is implemented on Nvidia GeForce 840 M. We checkpoint the running web application 
using CRIU. 
=>The dump files will be in the form of images which contains information of current host file
descriptors, cgroups, mounts, and TCP connections. 
=>We then copy all these files to multiple hosts remotely. Then we restore the container from that specific checkpoint. 
=>The application immediately boots up and continuous its job from where it was left off. 
=>We further provide an abstract of how to achieve container migration for DIGITS (Deep learning GPU training
system) container. The use of GPU is to leverage the GPU ability with the help of Nvidia-Docker wrapper tool.



Container Migration procedure:

=>First a Dockerfile is written using the syntax given by docker.inc. From that file, we
build a base image using “docker build” command and run a container using “docker run” command on one host.
=>Now to Initiate swarm mode. We must make one host as manager. We make the GeForce enable host as manager by
“Docker swarm init” command. This issues a token number to join to the swarm as workers.
=>Now using this token, the other two hosts are joined as workers. This forms a cluster where the command can be
executed on any host.
=>When commands are executed on worker nodes, behind the scenes it just routes those commands to manager to get it
executed.
=>Now we deploy our services to scale up the containers. We run docker-compose.yml file. Finally, we run the app.py file
where we get “hello world” message in localhost. 
=>We start the discovery service consul in the backend. Consul along with etcd or zookeeper can be used.
All 3 are compatible with swarm mode.
=>Now all the 3 hosts will send heartbeats to the consul where main host gets elected as leader.
=>Now CRIU is used to create checkpoint and restore it. We create checkpoint and restore it to all 3 nodes in the cluster. 
=>To copy the dump files to remote host we used rsync Linux command since it is more powerful than scp to transfer files.
We can also use shared file systems between multiple hosts such as NFS.
=>If the manager host is down for some reason (including maintenance of server), the manager node cannot send
heartbeat to consul. 
=>The consul will know that host is down. Now the other 2 hosts compete for the key to get elected as a
leader. The IP with the healthiest node gets elected as leader and we can observe the transfer of leadership. This way if the
host is down, the leadership is transferred to backup host ensuring service is always up and running.

We also extended the application to Nvidia-DIGITS using Nvidia-docker wrapper tool.



