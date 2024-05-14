# Side Self Study

# 1.Refresh your knowledge of OSI model
The OSI (Open Systems Interconnection) model is a conceptual framework that standardizes the functions of a telecommunication or computing system into seven distinct layers. Each layer serves a specific purpose and communicates with the layers above and below it. Here's a brief overview of each layer:

1. **Physical Layer (Layer 1)**:
   - This layer deals with the physical transmission of data over the network media.
   - It defines the electrical, mechanical, and procedural standards for transmitting raw data between devices.
   - Examples of devices at this layer include network cables, hubs, repeaters, and network interface cards (NICs).

2. **Data Link Layer (Layer 2)**:
   - This layer provides error-free transmission of data frames between adjacent nodes on the network.
   - It handles issues such as framing, error detection and correction, flow control, and access to the physical media.
   - Examples of devices at this layer include switches and bridges.

3. **Network Layer (Layer 3)**:
   - This layer is responsible for the logical addressing and routing of data packets between different networks.
   - It determines the optimal path for data packets to reach their destination across interconnected networks.
   - Examples of devices at this layer include routers and Layer 3 switches.

4. **Transport Layer (Layer 4)**:
   - This layer ensures reliable end-to-end communication between source and destination hosts.
   - It handles issues such as segmentation, reassembly, flow control, error recovery, and congestion control.
   - Examples of protocols at this layer include TCP (Transmission Control Protocol) and UDP (User Datagram Protocol).

5. **Session Layer (Layer 5)**:
   - This layer establishes, manages, and terminates communication sessions between applications on different hosts.
   - It provides services such as session establishment, maintenance, and synchronization.
   - Examples of services at this layer include remote procedure calls (RPCs) and session management protocols.

6. **Presentation Layer (Layer 6)**:
   - This layer translates, encrypts, and compresses data to ensure that it is readable by the application layer.
   - It handles tasks such as data format conversion, data encryption/decryption, and data compression/decompression.
   - Examples of protocols at this layer include SSL/TLS for encryption and MIME for multimedia content.

7. **Application Layer (Layer 7)**:
   - This layer provides network services directly to end-users and applications.
   - It supports communication between application processes running on different hosts.
   - Examples of protocols at this layer include HTTP, FTP, SMTP, and DNS.

The OSI model serves as a conceptual framework for understanding how different networking protocols and technologies interact within a networked environment. While not all networking technologies strictly adhere to the OSI model, it remains a valuable tool for network design, troubleshooting, and education.


# 2. Read about Load Balancing, get yourself familiar with different types and techniques of traffic load balancing.
Load balancing is a crucial concept in maintaining efficient and reliable performance for applications and websites that experience high volumes of traffic. Here's a breakdown of load balancing and its different types:

## What is Load Balancing?

Load balancing is the process of distributing incoming network traffic across a group of servers, also known as a server farm or pool. This ensures that no single server gets overloaded, and users experience fast response times. It's like having multiple checkout lanes at a grocery store to prevent long queues.

## Types of Load Balancing

There are several ways to distribute traffic among servers, each with its own advantages and use cases. Here are some common types:

- **Round Robin**: This is a simple approach where requests are sent to servers in a sequential order. It's easy to implement but may not be ideal for servers with varying capacities.

- **Least Connections**: The load balancer directs traffic to the server with the fewest active connections. This ensures a more even distribution of workload.

- **Weighted Round Robin**: Assigns a weight to each server based on its processing power or capacity. Servers with higher weights receive more traffic.

- **Resource-Based**: The load balancer analyzes server health metrics like CPU usage and memory before distributing traffic. This ensures requests go to servers with available resources.

- **DNS-based Load Balancing**: Distributes traffic across geographically dispersed servers by directing users to the closest server based on their DNS lookup. This improves response times for geographically diverse user bases.

- **Content-Based Switching**: Routes traffic based on the content requested. For example, static content like images might be served from a different server pool than dynamic content requiring processing.

## Techniques for Load Balancing

Load balancing is often implemented using hardware or software solutions. Here are some common techniques:

- **Hardware Load Balancers**: Dedicated devices specifically designed for high-performance traffic management.

- **Software Load Balancers**: Software applications installed on a server that performs load balancing functions.

- **Cloud Load Balancing Services**: Many cloud platforms offer managed load balancing services that are easy to set up and scale.


3. Practice in editing simple web forms with HTML + CSS + JS

### References 
1. []()