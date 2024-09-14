# Application Server vs Web Server: Infrastructure Design

## Overview

This project outlines the design of a web infrastructure that clearly separates the web server, application server, and database into distinct components, each on its own server. Additionally, a **HAProxy** load balancer is added to create a load-balancing cluster for improved scalability and fault tolerance.

## Infrastructure Components

### 1. **Server Configuration**

We will use **3 servers**, each dedicated to specific tasks:

- **Server 1**: Web server (Nginx)
- **Server 2**: Application server (e.g., PHP-FPM for PHP, Gunicorn for Python)
- **Server 3**: Database server (MySQL)

These servers are connected via a **HAProxy load-balancer cluster** to ensure efficient traffic distribution and redundancy.

### 2. **Load Balancer (HAProxy)**

The **HAProxy** load balancer will be configured in a **cluster** with another HAProxy instance to provide:

- **High Availability**: If one load balancer goes down, the other continues handling traffic.
- **Traffic Distribution**: Requests from users are distributed across the available web and application servers.

The **HAProxy cluster** is used to ensure that there is no single point of failure (SPOF) in the load balancing layer.

- **Why Load Balancer**:
  - Ensures even distribution of traffic to prevent overloading a single server.
  - Improves fault tolerance and redundancy.
  - Provides the capability to scale by adding more web or application servers.

### 3. **Web Server (Nginx)**

The **web server** is responsible for handling **static content** (e.g., HTML, CSS, JavaScript, images) and forwarding dynamic content requests to the application server. **Nginx** is chosen for its ability to efficiently serve static files and handle high levels of concurrent connections.

- **Why Web Server**:
  - Serves static content directly, reducing the load on the application server.
  - Acts as a reverse proxy to forward dynamic requests to the application server.

### 4. **Application Server (PHP-FPM, Gunicorn, etc.)**

The **application server** executes the dynamic logic of the website, interacting with the database to process requests. It handles programming logic, retrieves data from the database, and returns the response to the web server.

- **Why Application Server**:
  - Handles dynamic content requests (e.g., processing user input, API calls).
  - Offloads business logic and database interactions from the web server.

### 5. **Database Server (MySQL)**

The **database server** is responsible for storing all dynamic data for the website. This includes user information, content, and application settings.

- **Why Database Server**:
  - Centralized data storage for the application, accessed by the application server to retrieve or update information.
  - By isolating the database, the infrastructure becomes more modular and can scale the database layer independently.

---

## Request Workflow

1. **User Access Request**: A user enters `www.foobar.com` in their browser.
2. **HAProxy Load Balancer**: The request is first handled by the load balancer, which distributes it to one of the available web servers.
3. **Web Server (Nginx)**: The web server checks if the request is for static content (e.g., HTML, CSS). If it is, it serves the content directly; if not, it forwards the request to the application server.
4. **Application Server (PHP-FPM, Gunicorn)**: The application server processes the request, retrieves data from the database if necessary, and generates a response.
5. **Database Server (MySQL)**: If the request requires data retrieval or updates, the application server queries the database.
6. **Response**: The application server sends the response back to the web server, which then delivers it to the user.

---

## Specifics of This Infrastructure

### 1. **HAProxy Load Balancer Cluster**

- **Why add a load balancer cluster?**

  - Improves fault tolerance: If one load balancer fails, the other one takes over.
  - Balances traffic across servers: Ensures no single server is overloaded.

- **Cluster Setup**: The load balancer cluster is configured using **HAProxy** to distribute traffic in a **round-robin** manner. This ensures that each request is handled by an available server, improving response times and avoiding overload.

### 2. **Web Server (Nginx)**

- **Why separate the web server?**
  - Separating the web server reduces the load on the application server by directly serving static files.
  - It also improves security as the web server acts as a barrier between the internet and the application server.

### 3. **Application Server (PHP-FPM or Gunicorn)**

- **Why split the application server?**
  - Splitting the application server allows you to scale the application logic independently.
  - By isolating application logic from web server tasks, we can improve performance and reliability.

### 4. **Database Server (MySQL)**

- **Why isolate the database?**
  - Keeping the database on a separate server allows for better performance and security.
  - It also enables scaling of database resources as needed without affecting the web or application server.

---

## Issues with This Infrastructure

### 1. **Single Point of Failure (SPOF)**

Although we have a load-balancer cluster, each layer still represents a potential SPOF if one server goes down. The infrastructure can be further enhanced by adding redundant web, application, and database servers.

### 2. **Security Concerns**

Even though firewalls and SSL can be implemented, further security measures such as **intrusion detection** or **network segmentation** should be considered to reduce attack surfaces.

### 3. **Scalability**

This infrastructure can scale horizontally by adding more web and application servers behind the load balancer. However, the database server can become a bottleneck if traffic grows significantly.

---

## Conclusion

This infrastructure clearly separates the web server, application server, and database server, enhancing modularity and scalability. By adding a load-balancer cluster, we ensure that traffic is efficiently distributed and that there is redundancy in the system to handle failures. However, further improvements in terms of security, redundancy, and database scaling may be required to handle larger traffic loads and more sophisticated attack vectors.
