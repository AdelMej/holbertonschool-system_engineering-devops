```mermaid
architecture-beta
    group user(internet)[User]
    group dns(internet)[DNS]
    group server(cloud)[Server]

    service browser(internet)[User Browser] in user
    service domain(internet)[Domain] in dns
    service nginx(server)[Nginx Web Server] in server
    service app(server)[Application Server] in server
    service db(database)[MySQL Database] in server

    browser:R --> L:domain
    domain:L --> R:browser

    domain:R --> L:nginx
    nginx:L --> R:domain

    nginx:R --> L:app
    app:L --> R:nginx

    app:B --> T:db
    db:T --> B:app
```
---

# What is a server

A server is a computer that provides services to other computers over a network.
In this architecture, the server hosts all components of the application.

# Role of the domain name
The domain name maps a human-readable name to an IP address so users can reach the server easily.

# DNS record type for www
www is an A record pointing to the IP address 8.8.8.8.

# Role of the web server
The web server receives HTTP/HTTPS requests, serves static content, and forwards dynamic requests to the application server.

# Role of the application server
The application server contains the business logic and handles interactions between the user and the database.

# Role of the database
The database stores persistent application data such as users and records.

# Communication protocol
The server communicates with the user using HTTP/HTTPS over TCP/IP.

# Weaknesses of this architecture
- Single Point of Failure
- If the server goes down, the entire application is unavailable.
- Downtime during maintenance
- Restarting services causes downtime because there is no redundancy.
- No scalability
- The system cannot handle increased traffic because it runs on a single server.
