# mongo-cluster
This doc explains on how to configure mongoDB in a cluster with 3 nodes, 5 nodes or more.

### About ###
MongoDB uses a Master-Slave setup. Make sure to allocate atleast 2 Core CPU and 4GB of RAM to each node on production workloads with min of 3 nodes. No load balancing is as Mongo works as a master setup. MongoDb manages connections to the Master node and writes to it. Data is automatically replicated among other nodes.

For more info, refer: https://www.mongodb.com/docs/manual/replication/

### Installation ###

Refer below cloud-formation template for mongoDB installation.

https://github.com/cloud-martian/app-stack/tree/main/cloud-formation

### Configurations ###
Edit the /etc/mongod.conf file on each node

* Be sure the port number is the same on each instance (27017).  Port number is specified under “net”.  Add the IP’s of all the EC2’s boxes to bindIp.  IP’s are separated by a comma
* Add the replica set name.  Use the name of the environment with a number.  “prod“.  The number will be used to indicate the cluster.

Below configs are already part of above /etc/mongod.conf file. Just be sure to update below fields in the /etc/mongod.conf file. 

```
On Node 1
net:
  port: 27017
  bindIp: node1-IP
replication:
  replSetName: "prod"

On Node 2
net:
  port: 27017
  bindIp: node2-IP
replication:
  replSetName: "prod"

On Node 3
net:
  port: 27017
  bindIp: node3-IP
replication:
  replSetName: "prod"

```

### Setup ###
After adding above configs, create replicaset using below simple steps.

To create replica set:

Type 'mongo' on any instance to enter into mongo Shell: >mongo

Enter the following in the command line:

```
config = { _id: "prod", members: [
{ _id : 0, host : "node1-IP:27017"},
{ _id : 1, host : "node2-IP:27017"},
{ _id : 2, host : "node3-IP:27017"}, ]
};

Run this to instantiate the replica set:
rs.initiate(config);

Run this to verify:
rs.status();

exit
```

### Connection String to use ###
Use below connection string to connect backend to mongo and start writing data.

```
MongoDBConnectionString: "mongodb://user:password@node1-IP:27017,node1-IP:27017,node3-IP:27017"
```
