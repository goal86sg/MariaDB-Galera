# MariaDB-Galera
Forming a multi node MariaDB Galera cluster

## Test the cluster node status
```
 docker exec -ti mariadb-cluster-docker-compose_node1_1 mysql -u root -p -e 'show status like "wsrep_cluster_size"'
 ```
