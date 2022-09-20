# Deployment Mysql Innodb Cluster using docker compose.
Using for development and testing.
https://dev.mysql.com/doc/mysql-shell/8.0/en/mysql-innodb-cluster.html

## Requirement
- Minimum need 3 mysql server can tolerate up to ONE failure.
## Testing all in one VM using Docker Compose.
1. cd testing
2. docker compose up -d
3. go to Config Cluster using mysqlsh

## Deploy to Vm.
1. copy folder "deploy-per-node" to each "VM"
2. change  extra_hosts in mysql-server-cluster and mysql-router
3. hostname in mysql-server-cluster
4. change --server_id and --report-host in docker-compose.yal > mysql-server-cluster > command. node1 server_id=1, report-host=node1
5. docker compose up -d
if deploy same vm need change port. --port and --mysqlx-port. and binding ports.

## Config Cluster using mysqlsh (Mysql Shell).
ssh to node1.
docker exec -it [container-mysql-server-cluster] mysqlsh
var dbPass = "mysql";
var clusterName = "devCluster";

shell.connect('root@node1:3306', dbPass);
dba.configureInstance();

shell.connect('root@node2:3306', dbPass);
dba.configureInstance();

shell.connect('root@node3:3306', dbPass);
dba.configureInstance();

shell.connect('root@node1:3306', dbPass);
var cluster = dba.createCluster(clusterName);

cluster.addInstance({user: "root", host: "node2", port:3306, password: dbPass});
cluster.addInstance({user: "root", host: "node3", port:3306, password: dbPass});

## After config success connect to mysql route.
  Mysql router will be route write acction to primary.
