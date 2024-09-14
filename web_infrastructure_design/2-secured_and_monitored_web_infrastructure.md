# Secured and monitored Web Infrastructure for www.foobar.com (Secured and Monitored)

<a href="https://ibb.co/ydpFJby"><img src="https://i.ibb.co/TY1M5F8/task-2.png" alt="task-2" border="0"></a>

## Overview

This project outlines the design of a secure and monitored three-server web infrastructure that serves **www.foobar.com**. The infrastructure will:

- Use **3 firewalls** to secure servers
- Serve encrypted traffic using an **SSL certificate**
- Implement **3 monitoring clients** to collect data from servers

## Infrastructure Components

### 1. **Firewalls**

We will add **3 firewalls**, one for each server, to protect against unauthorized access:

- **Firewall 1**: Protects the load balancer
- **Firewall 2**: Protects the web server
- **Firewall 3**: Protects the application and database server

**Firewalls are used for**:

- Filtering incoming and outgoing traffic
- Defining rules that allow or block specific traffic based on IP addresses, ports, and protocols
- Protecting against external attacks (e.g., DDoS, unauthorized access)

### 2. **SSL Certificate**

An **SSL certificate** is used to serve traffic over **HTTPS**. This ensures that all data exchanged between the user and the web server is encrypted and secure.

- **Why HTTPS?**:

  - HTTPS protects the integrity and confidentiality of user data.
  - It prevents eavesdropping, man-in-the-middle attacks, and tampering.

- **Where is SSL applied?**:
  - The SSL certificate is installed on the **load balancer** to encrypt traffic before forwarding it to the web server.

### 3. **Monitoring Clients (Sumologic or another service)**

We will install **monitoring clients** on each server to track performance, uptime, and potential issues. These clients collect data (e.g., CPU usage, memory usage, requests per second) and send it to a centralized monitoring platform such as **Sumologic**.

- **Why Monitoring?**:
  - Monitoring ensures that server performance is tracked in real-time.
  - It provides alerts if there are problems (e.g., downtime, high CPU usage, network failures).
- **How data is collected**:
  - Monitoring clients (e.g., **Sumo Logic**, **Prometheus**) run as background services and collect metrics on server health and traffic.
  - The data is sent to a central server where it can be visualized and analyzed.

### 4. **Request Workflow (Secured)**

1. **User Access Request**: A user enters `www.foobar.com` in their browser.
2. **DNS Resolution**: The domain `www.foobar.com` resolves to the public IP address of the load balancer.
3. **SSL Encryption**: The connection is secured over **HTTPS** with SSL encryption.
4. **Load Balancer**: The load balancer forwards the request to one of the available web servers using the round-robin algorithm.
5. **Web Server (Nginx)**: The web server handles static content or forwards dynamic requests to the application server.
6. **Application Server**: The application server processes the request and interacts with the MySQL database if necessary.
7. **Database (MySQL Primary-Replica Setup)**: Writes go to the primary node, and reads can be handled by the replica node.
8. **Response**: The web server sends the encrypted response back to the user.

---

## Infrastructure Issues

### 1. **SSL Termination at the Load Balancer**

- **Problem**: If SSL termination happens at the load balancer level, the traffic between the load balancer and the web servers is not encrypted. This leaves the internal network vulnerable to eavesdropping.
- **Solution**: Use **end-to-end encryption**, where traffic between the load balancer and web servers is also encrypted.

### 2. **One MySQL Server Accepting Writes**

- **Problem**: Only having one MySQL server capable of handling **writes** introduces a bottleneck and a single point of failure (SPOF). If the primary MySQL server goes down, no writes can be made.

- **Solution**: Implement **MySQL clustering** or **multi-primary replication**, which allows multiple nodes to accept writes and provides higher availability.

### 3. **Servers with All Components**

- **Problem**: Having servers with both the web server, application server, and database combined might affect scalability and performance. The system cannot scale components independently, and a failure in one component (e.g., database crash) might affect the entire server.

- **Solution**: Use **dedicated servers** for each role (e.g., one for the web server, one for the application server, and one for the database). This allows independent scaling and better fault tolerance.

---

## Monitoring QPS (Queries Per Second)

To monitor your web server’s **QPS (Queries Per Second)**:

- Install a monitoring agent like **Prometheus** or **Sumologic** on the web server.
- Configure it to collect the **QPS** metric from the Nginx logs or through built-in metrics.
- Set up dashboards and alerts to track traffic spikes, performance, and server load in real time.

---

## Conclusion

This solution adds essential security elements (firewalls, HTTPS) and monitoring capabilities to the basic three-server infrastructure, addressing key security and reliability concerns. Although it improves security and observability, further enhancements can be made, such as introducing SSL encryption end-to-end and scaling database write capabilities to handle increased traffic loads.
