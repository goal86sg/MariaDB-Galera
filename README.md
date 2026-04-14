# MariaDB-Galera
Forming a multi-node MariaDB Galera cluster using Docker Compose.

## Requirements
- Docker Engine 20.10+
- Docker Compose v2+
- At least 4GB RAM available for 3 nodes

## Quick Start

1. **Clone and start the cluster:**
   ```bash
   docker-compose up -d
   ```

2. **Wait for cluster to initialize (~30 seconds)**

3. **Verify cluster status:**
   ```bash
   # Check cluster size
   docker exec -ti mariadb-galera_node1_1 mysql -u root -p${MARIADB_ROOT_PASSWORD:-test} -e "SHOW STATUS LIKE 'wsrep_cluster_size';"
   
   # Check node status
   docker exec -ti mariadb-galera_node1_1 mysql -u root -p${MARIADB_ROOT_PASSWORD:-test} -e "SHOW STATUS LIKE 'wsrep_local_state_comment';"
   ```

## Configuration

### Environment Variables
| Variable | Default | Description |
|----------|---------|-------------|
| `MARIADB_ROOT_PASSWORD` | test | Root password |
| `MYSQL_PASSWORD` | test | Application user password |
| `MYSQL_DATABASE` | maria | Initial database to create |

### Ports
- **Node 1:** 13306
- **Node 2:** 23306
- **Node 3:** 33306

## Testing

### Connect to Node 1:
```bash
docker exec -ti mariadb-galera_node1_1 mysql -u root -p${MARIADB_ROOT_PASSWORD:-test}
```

### Create test data on Node 1:
```sql
USE maria;
CREATE TABLE test (id INT AUTO_INCREMENT PRIMARY KEY, data VARCHAR(255));
INSERT INTO test (data) VALUES ('Hello from Node 1!');
SELECT * FROM test;
```

### Verify replication on Node 2:
```bash
docker exec -ti mariadb-galera_node2_1 mysql -u root -p${MARIADB_ROOT_PASSWORD:-test} -e "SELECT * FROM maria.test;"
```

## Troubleshooting

### Cluster won't start
```bash
# Check logs
docker-compose logs node1

# Restart cluster
docker-compose down
docker-compose up -d
```

### Node won't join
```bash
# Check node1 is running
docker ps | grep node1

# Force new cluster on node1
docker-compose restart node1
```

### Health check fails
```bash
# Check container health
docker inspect mariadb-galera_node1_1 | grep -A 20 Health
```

## Version History
- **10.11** - Current stable (LTS with Galera 4)
- **10.1** - Deprecated (original version)
