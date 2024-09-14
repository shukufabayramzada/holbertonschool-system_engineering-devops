# Distributed Web Infrastructure Design for www.foobar.com

<a href="https://ibb.co/1GGyzjD"><img src="https://i.ibb.co/xqq9Fdp/task-1.png" alt="task-1" border="0"></a>

## Overview

This project outlines the design of a web infrastructure that serves a website at **www.foobar.com**. It consists of three servers, each serving a specific purpose:

- **HAProxy** as the load balancer
- **Nginx** as the web server
- **Application server** (PHP-FPM or another ) to process application logic
- **MySQL** as the database with a Primary-Replica (Master-Slave) cluster setup

## Infrastructure Components

### 1. **Load Balancer (HAProxy)**

The **load balancer** distributes incoming traffic across multiple servers, ensuring that no single server becomes overloaded. It helps with load distribution and redundancy in case of server failure.

- **Distribution Algorithm**:

  - We use the **Round-Robin** algorithm, where each incoming request is forwarded to the next available server in sequence. This helps to balance the traffic evenly.

- **Active-Active Setup**:
  - In this configuration, both web and application servers are fully operational, and the load balancer distributes traffic between them. This increases resource utilization and ensures higher availability compared to an Active-Passive setup.

### 2. **Web Server (Nginx)**

The **web server (Nginx)** handles HTTP/HTTPS requests from users. It serves static content such as HTML, CSS, and JavaScript and forwards dynamic requests to the application server for further processing.

- **Why Nginx?**: Nginx is lightweight, high-performing, and well-suited for handling a large number of concurrent connections.

### 3. **Application Server (PHP-FPM or Gunicorn)**

The **application server** is responsible for processing the business logic of the website. It interacts with the database to retrieve or store data and sends responses back to the web server.

- **Why separate the application server?**: This separation allows better resource allocation, scalability, and improved system efficiency.

### 4. **Database (MySQL Primary-Replica Setup)**

A **Primary-Replica (Master-Slave)** setup is implemented for the MySQL database to ensure higher availability and better performance.

- **Primary Node (Master)**: Handles all **write** operations.
- **Replica Node (Slave)**: Handles **read** operations and replicates data from the master node in real-time.

This setup improves performance by distributing read queries across multiple nodes and provides fault tolerance by promoting the replica to the master in case of failure.

### 5. **Three Physical/Virtual Servers**

- **Server 1**: Hosts the **HAProxy Load Balancer**
- **Server 2**: Hosts the **Nginx Web Server**
- **Server 3**: Hosts the **Application Server** and the **MySQL Database (Primary-Replica Setup)**

## Request Workflow

1. **User Access Request**: A user enters `www.foobar.com` in their browser.
2. **DNS Resolution**: The domain `www.foobar.com` resolves to the public IP address of the load balancer.
3. **Load Balancer**: The load balancer forwards the request to one of the available web servers using the round-robin algorithm.
4. **Web Server (Nginx)**: The web server handles static content or forwards dynamic requests to the application server.
5. **Application Server**: The application server processes the request, interacts with the MySQL database if necessary, and sends the response back to the web server.
6. **Database (MySQL)**:
   - **Writes** are sent to the primary node.
   - **Reads** can be distributed across the primary and replica nodes.
7. **Response**: The web server sends the processed response back to the user’s browser.

## Issues with this Infrastructure

### 1. **Single Point of Failure (SPOF)**

- The **load balancer** is a single point of failure. If it fails, the entire website becomes inaccessible.
  - **Solution**: Introduce a redundant load balancer for failover.
- The **primary database node** is another SPOF. If it fails, all write operations will be affected.
  - **Solution**: Promote the replica to the primary role if the master fails.

### 2. **Security Issues**

- **No Firewall**: Without a firewall, the system is vulnerable to attacks.
  - **Solution**: Implement firewalls at the server level to protect against unauthorized access.
- **No HTTPS**: All communication between the user and the server is insecure.
  - **Solution**: Implement **SSL/TLS** certificates to secure communications.

### 3. **No Monitoring**

- Without monitoring, system failures and performance issues may go unnoticed.
  - **Solution**: Use **monitoring tools** (e.g., Prometheus, Grafana) to track performance, uptime, and system health.

## Improvements

- **Redundant Load Balancer**: Add another load balancer to prevent system outages in case of failure.
- **SSL/TLS Certificates**: Enable HTTPS to secure user-server communication.
- **Monitoring and Alerts**: Implement monitoring tools for real-time alerts and system health monitoring.

## Conclusion

This design outlines a **distributed infrastructure** for hosting **www.foobar.com** that improves scalability, fault tolerance, and performance. While it resolves some issues from the single-server setup, further improvements are needed to fully address security and availability concerns.
