# Monitoring, Troubleshooting, and Scaling MongoDB

## Monitoring MongoDB Performance

### :mag: Key Metrics to Monitor

- **CPU Usage:** High CPU indicates inefficient queries or inadequate hardware.
- **Memory (RAM):** MongoDB uses RAM for its working set. Monitor resident and virtual memory.
- **Disk Usage & IOPS:** Crucial for write-heavy operations and indexing.
- **Network Usage:** Check for bottlenecks in replication and client interactions.

### :hammer_and_wrench: Monitoring Tools

#### 1. MongoDB Atlas (Cloud)

- Visual dashboards for real-time metrics.
- Built-in alerts (e.g., "CPU > 80% for 5 minutes").
- Easy to use and integrates with third-party tools like PagerDuty, Slack.

#### 2. MongoDB Ops Manager (On-premise)

- Advanced monitoring with historical data and automation.
- Best for enterprise deployments.

#### 3. mongostat (CLI Tool)

Shows a quick overview:

```shell
mongostat --host localhost:27017
```

Sample output:

```sql
insert query update delete getmore command % dirty % used flushes vsize res qrw arw net_in net_out conn
  10     5     2      0       1     15|0       2.5    98.4     1   2.5G  512M 0|0  0|0   1.0kB  2.1kB   20
```

### :white_check_mark: Practical Task

Use mongostat while running a bulk insert operation:

```bash
for i in {1..10000}; do mongo test --eval 'db.metrics.insert({value: Math.random()})'; done
```

Observe how insert operations and memory usage spike.

## Scaling MongoDB

### :bricks: Vertical vs. Horizontal Scaling

- **Vertical Scaling:** Add more CPU, RAM, or SSD. Easy, but hits hardware limits quickly.
- **Horizontal Scaling:** Add more nodes—MongoDB supports sharding for this.

### :gear: Sharded Cluster Setup (Basic)

Components:

- **Config Servers** (store metadata)
- **Shard Servers** (data distributed across)
- **Mongos Router** (query router)

### :white_check_mark: Step-by-step Example (Local Setup with Docker)

```yaml
# docker-compose.yml
version: '3'
services:
  configsvr:
    image: mongo
    command: mongod --configsvr --replSet configReplSet
    ports: [ "27019:27019" ]

  shard1:
    image: mongo
    command: mongod --shardsvr --replSet shard1ReplSet
    ports: [ "27018:27018" ]

  mongos:
    image: mongo
    depends_on: [configsvr, shard1]
    command: mongos --configdb configReplSet/localhost:27019
    ports: [ "27017:27017" ]
```

Run the cluster, initiate replica sets, then:

```javascript
sh.addShard("shard1ReplSet/localhost:27018");
sh.enableSharding("mydatabase");
sh.shardCollection("mydatabase.users", { userId: 1 });
```

### :bulb: Infrastructure Tips

- Use SSD-backed nodes for better performance.
- Deploy across Availability Zones for fault tolerance.
- Leverage Auto-scaling in Atlas or Kubernetes for dynamic growth.

## Troubleshooting MongoDB

### :rotating_light: Common Errors & Fixes

| Error                       | Cause                            | Solution                                        |
| --------------------------- | -------------------------------- | ----------------------------------------------- |
| `Too many open connections` | Connection leak                  | Use connection pooling                          |
| `Exceeded memory limit`     | Poor indexing, large working set | Add indexes, scale vertically                   |
| `Duplicate key error`       | Unique constraint violation      | Check for duplicate values before insert/update |

### :receipt: Using Logs and Diagnostic Tools

#### MongoDB Logs

- Located at /var/log/mongodb/mongod.log
- Useful for slow queries, connection issues, and crashes.

#### Diagnostic Commands

```javascript
db.currentOp()     // Show current operations
db.serverStatus()  // Overall health and stats
```

#### Profiler

Enable it to log slow queries:

```javascript
db.setProfilingLevel(1, { slowms: 100 });
```

Check logged queries:

```javascript
db.system.profile.find().sort({ ts: -1 }).limit(5).pretty();
```

#### :white_check_mark: Practical Debugging Example

```javascript
db.users.createIndex({ email: 1 }, { unique: true });
db.users.insert({ email: "test@example.com" });
db.users.insert({ email: "test@example.com" }); // Triggers duplicate key error
```
