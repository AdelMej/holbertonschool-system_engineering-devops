```mermaid
architecture-beta
    group internet(internet)[Internet]
    group lb(server)[Load Balancer Server]
    group server1(server)[Server 1]
    group server2(server)[Server 2]
    group db(server)[Database Server]

    service user(internet)[User Browser] in internet

    service fw_lb(server)[Firewall] in lb
    service haproxy(server)[HAProxy and SSL] in lb
    service monitor_lb(server)[Monitoring Agent] in lb

    service fw_s1(server)[Firewall] in server1
    service nginx1(server)[Nginx] in server1
    service app1(server)[Application Server] in server1
    service monitor_s1(server)[Monitoring Agent] in server1

    service fw_s2(server)[Firewall] in server2
    service nginx2(server)[Nginx] in server2
    service app2(server)[Application Server] in server2
    service monitor_s2(server)[Monitoring Agent] in server2

    service mysql(server)[MySQL Primary] in db
    service monitor_db(server)[Monitoring Agent] in db

    user:B --> T:fw_lb
    fw_lb:B --> T:haproxy
    fw_lb:R --> T:monitor_lb
    haproxy:R --> L:monitor_lb
    haproxy:L --> R:fw_s1
    fw_s1:L --> R:nginx1
    haproxy:B --> T:fw_s2
    nginx1:L --> B:app1
    nginx1:B --> T:monitor_s1
    app1:R --> L:monitor_s1
    fw_s1:B --> R:monitor_s1
    fw_s2:B --> T:nginx2
    nginx2:B --> T:app2
    fw_s2:L --> T:monitor_s2
    nginx2:L --> R:monitor_s2
    app2:L --> B:monitor_s2
    app1:B --> L:mysql
    app2:B --> T:mysql
    mysql:B --> T: monitor_db
```

---

# Why each additional element exists

## Firewalls (3 total)

- Firewalls are added to control and restrict network traffic.
- Each firewall:
  - allows only required ports (e.g. 443, 3306 internally)
  - blocks unauthorized access
  - limits attack surface

### Example

- Load balancer firewall: only allow HTTPS (443)
- App servers: only allow traffic from LB
- Database: only allow traffic from app servers

# SSL certificate (HTTPS)

## HTTPS is used to

- encrypt traffic between users and the server
- prevent data interception (MITM attacks)
- protect credentials, cookies, tokens
- ensure authenticity of <www.foobar.com>

### Without HTTPS

- passwords can be sniffed
- sessions can be hijacked
- traffic can be modified

# monitoring agents (3)

Monitoring agents collect:

- CPU usage
- memory usage
- disk I/O
- network traffic
- application logs
- error rates

They send data to a centralized monitoring service (e.g. Sumologic, Datadog, Prometheus).

## What monitoring is used for

Monitoring is used to:

- detect outages
- identify performance issues
- observe traffic spikes
- alert engineers before users complain
- analyze historical trends

# How monitoring collects data

Monitoring agents:

- run on each server
- collect metrics locally
- tail logs
- push data periodically to a monitoring backend
- This is agent-based monitoring.

# How to monitor Web Server QPS

To monitor QPS (Queries Per Second) on Nginx:

- enable Nginx status module (stub_status)
- or parse access logs
- or expose metrics to Prometheus
- visualize QPS in dashboards

This helps detect:

- traffic spikes
- DDoS
- scaling needs
- 3️⃣ Issues with this infrastructure

# SSL termination at the Load Balancer

Problem:

- traffic between LB and backend is unencrypted
- internal attackers could sniff traffic
- violates end-to-end encryption

Better solution:

- re-encrypt traffic from LB to backend
- or use mutual TLS

# Only one MySQL Primary

Problem:

- single point of failure
- if MySQL crashes → writes stop
- no automatic failover

This affects:

- availability
- write scalability
- reliability

# Servers contain all components

Each server has:

- web server
- application server
- database

Problems:

- hard to scale components independently
- security risk (DB exposed on app server)
- inefficient resource usage
- harder maintenance

Best practice:

- separate concerns (web, app, DB)
