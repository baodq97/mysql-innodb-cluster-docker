# InnoDB cluster Error
https://dev.mysql.com/doc/mysql-shell/8.0/en/troubleshooting-innodb-cluster.html

## Access Mysql Shell
docker exec -it [container-mysql-server-cluster] mysqlsh
## Check cluster status.
shell.connect('root@node1:3306', dbPass);
var cluster = dba.getCluster();
cluster.status();
output:
{
    "clusterName": "devCluster",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "node2:3306",
        "ssl": "REQUIRED",
        "status": "OK",
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.",
        "topology": {
            "node1:3306": {
                "address": "node1:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.0.30"
            },
            "node2:3306": {
                "address": "node2:3306",
                "memberRole": "PRIMARY",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.0.30"
            },
            "node3:3307": {
                "address": "node3:3307",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.0.30"
            }
        },
        "topologyMode": "Single-Primary"
    },
    "groupInformationSourceMember": "node2:3306"
}

## Exec to mysql query
1. docker exec -it [container-server-name] /bin/bash
2. bash#> mysql -u root -pmysql
## One or more instance of Innodb cluster error.
1. If primary node not error => Access mysqlsh => connect shell to primary node => dba.rebootClusterFromCompleteOutage();
2. If primary node error => Access mysqlsh => connect to secondary node alive => get cluster => 
  - cluster.forceQuorumUsingPartitionOf('node2:3306'); //node2:3306 is node not alive.
  - cluster.status(); // now node 2 is primary
  - cluster.rescan(); // auto remove other node error.
  - cluster.addInstance({user: "root", host: "node3",port:3307, password: dbPass}); // add instance error remove from past step.
  If cluster.addInsance error. => exec to mysql command => reset slave; => Try again cluster addInstance.