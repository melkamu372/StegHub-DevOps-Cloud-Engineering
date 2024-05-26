# Read more about 
1. **different configuration aspects of Apache mod_proxy_balancer module**. 

2. **Understand what sticky session means and when it is used.**

## Apache mod_proxy_balancer Configuration `mod_proxy_balancer`

The `mod_proxy_balancer` module in Apache HTTP Server is used to configure load balancing between backend servers, which helps distribute incoming client requests to multiple servers to ensure availability and reliability.

### Load Balancer Configuration

- **BalancerMember:** Defines the backend servers (also called balancer members) in the load balancer pool.
```
  <Proxy balancer://mycluster>
      BalancerMember http://backend1.example.com
      BalancerMember http://backend2.example.com
  </Proxy>
  ProxyPass / balancer://mycluster/

```

**BalancerManager** : Provides a web-based interface to manage and monitor the load balancer

```
<Location /balancer-manager>
    SetHandler balancer-manager
    Require all granted
</Location>
```

### Sticky Sessions (Session Persistence)
Sticky sessions, also known as session persistence, ensure that a user's session is consistently handled by the same backend server. This is crucial for applications where session data is stored on the server, and accessing different servers for subsequent requests would disrupt the session state.

Sticky sessions can be configured using the stickysession parameter:
```
<Proxy balancer://mycluster>
    BalancerMember http://backend1.example.com
    BalancerMember http://backend2.example.com
    ProxySet stickysession=JSESSIONID
</Proxy>
ProxyPass / balancer://mycluster/ stickysession=JSESSIONID
```
In the above example, the `JSESSIONID` cookie is used to track the session, ensuring that all requests with the same session ID are routed to the same backend server.

## Load Balancing Methods

**byrequests**: Distributes requests evenly based on the number of requests.
```
ProxySet lbmethod=byrequests
```
**bytraffic**: Distributes requests based on the amount of traffic (bytes).
```
ProxySet lbmethod=bytraffic
```

**bybusyness**: Distributes requests based on the number of active requests
```
ProxySet lbmethod=bybusyness
```

## Failover and Recovery

`status=+H`: Marks a backend server as hot standby.

```
BalancerMember http://backend3.example.com status=+H
```
**retry**: Sets the time to wait before retrying a failed backend server.
```
ProxySet retry=60
```

## When to Use Sticky Sessions

Sticky sessions are particularly useful when:

- **Session Data**: The application stores session data on the server rather than in a shared storage or database.
- **Stateful Applications**: Applications that maintain state across multiple requests, such as shopping carts or user authentication systems.
- **Performance Considerations**: Avoiding the overhead of synchronizing session data across multiple servers.

> However, sticky sessions can reduce the effectiveness of load balancing and lead to uneven distribution of load. In such cases, it's often better to use a centralized session store or a shared caching solution like Redis or Memcached.
