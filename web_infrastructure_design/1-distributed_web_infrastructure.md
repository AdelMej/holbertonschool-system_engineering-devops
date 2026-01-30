```mermaid
architecture-beta
    group internet[internet](Internet)
    group lb[server](Load Balancer)
    group server1[server](Server 1)
    group server2[server](Server 2)
    group db[database](database)

    service user(internet)[User Browser] in internet
    service haproxy(server)[HAProxy] in lb

    service nginx1(server)[Nginx] in server1
    service app1(server)[Application Server] in server1

    service nginx2(server)[Nginx] in server2
    service app2(server)[Application Server] in server2

    service mysql_primary(database)[MySQL Primary] in db
    service mysql_replica(database)[MySQL Replica] in db

    user:B --> T:haproxy
    haproxy:L --> L:nginx1
    haproxy:B --> T:nginx2
    nginx1:B --> T:app1
    nginx2:B --> T:app2
    app1:B --> T:mysql_primary
    app2:B --> T:mysql_primary
    app1:B --> T:mysql_replica
    app2:B --> T:mysql_replica
```

---

# Why each component isadded

- Load Balancer (HAProxy)
- Distributes incoming traffic across multiple servers
- Improves availability
- Allows scaling horizontally
- Prevents one server from being overloaded

# Web Servers (Nginx)

- Handle HTTP/HTTPS requests
- Serve static files
- Forward dynamic requests to the application server
- Act as a reverse proxy

# Application Servers

- Execute business logic
- Process user requests
- Communicate with the database
- Keep application code isolated from the web layer

# Application Files

- Contain the application source code
- Must be identical on both servers to ensure consistency

# Database (MySQL Primary + Replica)

- Stores persistent data
- Separates read and write operations
- Improves read performance
- Adds limited fault tolerance

# 3️⃣ Load Balancer configuration

- Distribution algorithm
- Round Robin

# How it works

- Requests are distributed sequentially
- Each server receives requests in turn
- Simple and effective when servers have similar capacity

# Active-Active vs Active-Passive

- Active-Active (used here):
  - Both servers handle traffic at the same time
  - Better resource utilization
  - Higher throughput

- Active-Passive:
  - One server handles traffic
  - Second server is on standby
  - Used mainly for failover, not load sharing

# 4️⃣ Database Primary-Replica (Master-Slave) cluster

How it works
Primary database handles all writes
Replica database copies data from the Primary
Replication is usually asynchronous
Application reads can be directed to the Replica

Difference between Primary and Replica

| Primary | Replica |
| ------------- | ---------- |
| Handle writes | Read-only |
| source of truth | Copy of data |
| Higher load | Lower load |

The application must never write to the Replica.

# 5️⃣ Issues with this infrastructure

- Single Points of Failure (SPOF)
- Load balancer (only one)
- Database Primary
- No automatic failover
- Security issues
- No firewall
- No HTTPS / TLS encryption
- Database potentially exposed
- No network segmentation
- No monitoring
- No visibility into failures
- No performance metrics
- No alerting system
- Failures are detected only by users
