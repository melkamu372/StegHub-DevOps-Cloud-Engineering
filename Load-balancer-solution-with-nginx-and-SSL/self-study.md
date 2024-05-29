# Read more about HTTP load balancing methods and features supported by Nginx 
Nginx is a powerful and flexible HTTP server and reverse proxy that supports various load balancing methods and features. Here’s an overview of the HTTP load balancing methods and key features supported by Nginx:
## Load Balancing Methods

1. **Round Robin**
 Distributes client requests sequentially to the backend servers in a cyclic manner.
Use case: Default and simplest load balancing method, suitable for evenly distributed loads across servers.
```
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

```
2. **Least Connections** Sends requests to the server with the fewest active connections.
Use case: Useful when the servers have varying capacities or when the load varies significantly.
```
upstream backend {
    least_conn;
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

```
3. **IP Hash** Routes requests from the same client IP to the same backend server.
Use case: Ensures session persistence by sending a client to the same server based on the client's IP address.
upstream backend {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}
4. **Generic Hash** Allows the use of a custom hash key for load balancing, such as a specific header, cookie, or other request attribute.
Use case: Provides flexible session persistence based on custom parameters.
```
upstream backend {
    hash $request_uri consistent;
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

```
## Load Balancing Features