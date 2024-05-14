# Side Self Study

# 1.Refresh your knowledge of OSI model(Open Systems Interconnection)
The OSI (Open Systems Interconnection) model is a conceptual framework that standardizes the functions of a telecommunication or computing system into seven distinct layers. Each layer serves a specific purpose and communicates with the layers above and below it. Here's a brief overview of each layer:
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7f450da6-d84d-4270-8c6a-488adf673d9c)


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

## How Data Flows Through the OSI Model
Data flows from layer 7 down to layer 1 from the sender, and then flows from layer 1 to layer 7 on the recipient device. The simplest example of communication flow through the OSI Model is an email application.

When a sender clicks “Send” on an email application, the message is sent to the presentation layer using a defined protocol (SMTP for outgoing email). The presentation layer compresses the data and sends the message to the session layer, which opens a session for communication between the sender’s device and the outgoing server.

The message is sent to the transport layer where data is segmented, and then the network layer breaks the segments into packets. Then, the packets are sent from the network layer to the data link layer, where packets are further broken down into frames. The frames are sent to the physical layer where data is converted to bitstreams of ones and zeros and transferred across a medium such as wireless connections or cables.

When the message reaches the recipient, the process is reversed. Data is sent from the physical layer to the application layer, where data is converted from the bitstream ones and zeros to the message available in the recipient’s email client. When a message is sent back to the sender, the process is repeated, and communication flows down to layer 1 from layer 7 and back up the OSI Model when it reaches the recipient’s device.

## The Importance of the OSI Model
The OSI Model's relevance in networking lies in its ability to serve as a universal reference framework. Here's why it's crucial:

- **Layered Approach**:
The model breaks down network communication into discrete layers, making it easier to design, develop, troubleshoot, and maintain network protocols and systems.

- **Standardization**:
It offers a standardized way to discuss and understand network communication. This standardization simplifies communication between different vendors and technologies.

- **Interoperability**:
By defining clear boundaries and responsibilities for each layer, the OSI Model promotes interoperability. Devices and software developed independently can communicate effectively if they adhere to the model's specifications.

- **Troubleshooting**:
When network issues arise, the model helps in pinpointing the layer at which the problem exists. This aids network administrators and engineers in diagnosing and resolving issues efficiently.

## OSI Model vs TCP/IP Model
The OSI Model and the TCP/IP Model are two fundamental frameworks used to conceptualize and understand network architecture and communication. While both models serve as guides for designing and troubleshooting networks, they differ significantly in their approach.

The OSI Model, with its seven-layer structure, offers a comprehensive and highly structured view of networking concepts, promoting interoperability and clear separation of concerns. In contrast, the TCP/IP Model, with its four-layer structure, mirrors the practical implementation of the Internet, simplifying the model for real-world network communication. This simplification, however, can come at the cost of some of the comprehensive structure found in the OSI Model, making each model suitable for different networking contexts.

# 2. Read about Load Balancing, get yourself familiar with different types and techniques of traffic load balancing.
Load balancing is a crucial concept in maintaining efficient and reliable performance for applications and websites that experience high volumes of traffic. Here's a breakdown of load balancing and its different types:

## What is Load Balancing?

Load balancing is the process of distributing incoming network traffic across a group of servers, also known as a server farm or pool. This ensures that no single server gets overloaded, and users experience fast response times. It's like having multiple checkout lanes at a grocery store to prevent long queues.

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/646de198-4f75-4c0f-9bf2-12e48c6914da)

## Types of Load Balancing Algorithms

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

## The Usages of Load Balance

1. **Improving website performance**:
   - Load balancing distributes incoming requests among multiple servers, alleviating overload on individual servers and thereby reducing response times for the request caller.

2. **Ensuring high availability and reliability**:
   - Load balancing distributes requests among multiple servers, preventing a single point of failure. It redirects traffic to available servers if one server fails or experiences performance issues.

3. **Scalability**:
   - Load balancing facilitates easy scaling of infrastructure as traffic increases. Additional servers can be added to the load balance pool to meet increased demand without significant changes to the infrastructure.

4. **Redundancy**:
   - Load balancing can maintain redundant copies of data and services, reducing the risk of data loss, inconsistency, or service outages due to hardware failures or other issues.

5. **Network Optimization**:
   - By routing network traffic across multiple servers through different paths, load balancing optimizes and reduces congestion in the network, improving overall network performance.

6. **Geographic Distribution**:
   - Load balancing can direct traffic to the nearest or best-performing server for end users. This is particularly useful for global services accessible from anywhere in the world.

7. **Application Performance**:
   - Load balancing redirects requests to specific applications, ensuring each request receives the necessary resources to perform optimally.

8. **Security**:
   - Load balancing can protect against Distributed Denial of Service (DDoS) attacks by making it harder to overwhelm a single target.

9. **Cost Saving**:
   - Load balancers enable effective utilization and efficient workload distribution, resulting in hardware and infrastructure optimization and cost savings.

10. **Load Testing**:
    - Load balancing can be configured to simulate high incoming traffic to evaluate application performance and identify application bottlenecks.


# 3. Practice in editing simple web forms with HTML + CSS + JS

## HTML (HyperText Markup Language)

- HTML is the standard markup language used to create the structure and content of web pages.
- It consists of a series of elements (tags) that define different parts of a web page, such as headings, paragraphs, links, images, and forms.
- HTML elements are organized into a hierarchical structure, with each element representing a specific piece of content or functionality.
- Web browsers interpret HTML code to render web pages for users to view and interact with.

## CSS (Cascading Style Sheets)

- CSS is a style sheet language used to define the presentation and layout of HTML documents.
- It allows web developers to control the appearance of HTML elements, including their colors, fonts, spacing, alignment, and more.
- CSS works by associating style rules with HTML elements, specifying how those elements should be displayed in the browser.
- By separating content (HTML) from presentation (CSS), we can create more flexible and maintainable web designs.

## JavaScript

- JavaScript is a high-level programming language commonly used to add interactivity and dynamic behavior to web pages.
- It allows developers to create interactive features such as animations, form validation, content updates, and more.
- JavaScript code can be embedded directly into HTML documents or included as separate files linked from HTML.
- It runs on the client-side (in the user's web browser), enabling real-time interactions without the need for server communication.
- JavaScript is a versatile language with a wide range of applications, including web development, server-side programming, game development, and more.
## Sample contact form practice out puts

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/00c723c1-55ce-4d29-901d-66cd57e4c9a9)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/05e80457-52ac-4c81-8592-d153edbe33cd)
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e1554803-0207-42e4-8869-7a3149efdfd1) ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3639a3ba-3547-4229-bf60-c10f545b5810)

[Practice source code link](https://html-css-js.com/?html=%3C!DOCTYPE%20html%3E%0A%3Chtml%20lang=%22en%22%3E%0A%3Chead%3E%0A%20%20%3Cmeta%20charset=%22UTF-8%22%3E%0A%20%20%3Cmeta%20name=%22viewport%22%20content=%22wi$*$dth=device-wi$*$dth,%20initial-scale=1.0%22%3E%0A%20%20%3Ctitle%3EContact%20Form%3C/title%3E%0A%20%20%3Clink%20rel=%22stylesheet%22%20href=%22styles.css%22%3E%0A%3C/head%3E%0A%3Cbody%3E%0A%0A%20%20%3Cdiv%20class=%22container%22%3E%0A%20%20%20%20%3Ch2%3EContact%20Us%3C/h2%3E%0A%20%20%20%20%3Cform%20i$*$d=%22contactForm%22%3E%0A%20%20%20%20%20%20%3Cdiv%20class=%22form-group%22%3E%0A%20%20%20%20%20%20%20%20%3Clabel%20for=%22name%22%3EName:%3C/label%3E%0A%20%20%20%20%20%20%20%20%3Cinput%20type=%22text%22%20i$*$d=%22name%22%20name=%22name%22%20required%3E%0A%20%20%20%20%20%20%20%20%3Cspan%20class=%22error%22%20i$*$d=%22nameError%22%3E%3C/span%3E%0A%20%20%20%20%20%20%3C/div%3E%0A%20%20%20%20%20%20%3Cdiv%20class=%22form-group%22%3E%0A%20%20%20%20%20%20%20%20%3Clabel%20for=%22email%22%3EEmail:%3C/label%3E%0A%20%20%20%20%20%20%20%20%3Cinput%20type=%22email%22%20i$*$d=%22email%22%20name=%22email%22%20required%3E%0A%20%20%20%20%20%20%20%20%3Cspan%20class=%22error%22%20i$*$d=%22emailError%22%3E%3C/span%3E%0A%20%20%20%20%20%20%3C/div%3E%0A%20%20%20%20%20%20%3Cdiv%20class=%22form-group%22%3E%0A%20%20%20%20%20%20%20%20%3Clabel%20for=%22message%22%3EMessage:%3C/label%3E%0A%20%20%20%20%20%20%20%20%3Ctextarea%20i$*$d=%22message%22%20name=%22message%22%20rows=%224%22%20required%3E%3C/textarea%3E%0A%20%20%20%20%20%20%20%20%3Cspan%20class=%22error%22%20i$*$d=%22messageError%22%3E%3C/span%3E%0A%20%20%20%20%20%20%3C/div%3E%0A%20%20%20%20%20%20%3Cbutton%20type=%22submit%22%3ESubmit%3C/button%3E%0A%20%20%20%20%3C/form%3E%0A%20%20%20%20%3Cdiv%20i$*$d=%22status%22%3E%3C/div%3E%0A%20%20%3C/div%3E%0A%0A%20%20%3Cscript%20src=%22script.js%22%3E%3C/script%3E%0A%3C/body%3E%0A%3C/html%3E%0A&css=body%20%7B%0A%20%20font-family:%20Arial,%20sans-serif;%0A%20%20background-color:%20#f4f4f4;%0A%7D%0A%0A.container%20%7B%0A%20%20max-wi$*$dth:%20400px;%0A%20%20margin:%200%20auto;%0A%20%20padding:%2020px;%0A%20%20background-color:%20#fff;%0A%20%20border-radius:%205px;%0A%20%20box-shadow:%200%200%2010px%20rgba(0,%200,%200,%200.1);%0A%7D%0A%0Ah2%20%7B%0A%20%20margin-bottom:%2020px;%0A%7D%0A%0A.form-group%20%7B%0A%20%20margin-bottom:%2020px;%0A%7D%0A%0Alabel%20%7B%0A%20%20display:%20block;%0A%20%20margin-bottom:%205px;%0A%7D%0A%0Ainput%5Btype=%22text%22%5D,%0Ainput%5Btype=%22email%22%5D,%0Atextarea%20%7B%0A%20%20wi$*$dth:%20100%25;%0A%20%20padding:%2010px;%0A%20%20border:%201px%20soli$*$d%20#ccc;%0A%20%20border-radius:%205px;%0A%7D%0A%0Abutton%20%7B%0A%20%20background-color:%20#007bff;%0A%20%20color:%20#fff;%0A%20%20border:%20none;%0A%20%20padding:%2010px%2020px;%0A%20%20cursor:%20pointer;%0A%20%20border-radius:%205px;%0A%7D%0A%0Abutton:hover%20%7B%0A%20%20background-color:%20#0056b3;%0A%7D%0A%0A.error%20%7B%0A%20%20color:%20red;%0A%20%20font-size:%2012px;%0A%7D%0A&js=document.getElementById(%22contactForm%22).addEventListener(%22submit%22,%20function(event)%20%7B%0A%20%20event.preventDefault();%0A%0A%20%20var%20name%20=%20document.getElementById(%22name%22).value;%0A%20%20var%20email%20=%20document.getElementById(%22email%22).value;%0A%20%20var%20message%20=%20document.getElementById(%22message%22).value;%0A%0A%20%20//%20Vali$*$date%20inputs%0A%20%20if%20(name.trim()%20===%20%22%22)%20%7B%0A%20%20%20%20document.getElementById(%22nameError%22).innerText%20=%20%22Name%20is%20required%22;%0A%20%20%20%20return;%0A%20%20%7D%20else%20%7B%0A%20%20%20%20document.getElementById(%22nameError%22).innerText%20=%20%22%22;%0A%20%20%7D%0A%0A%20%20if%20(email.trim()%20===%20%22%22)%20%7B%0A%20%20%20%20document.getElementById(%22emailError%22).innerText%20=%20%22Email%20is%20required%22;%0A%20%20%20%20return;%0A%20%20%7D%20else%20%7B%0A%20%20%20%20document.getElementById(%22emailError%22).innerText%20=%20%22%22;%0A%20%20%7D%0A%0A%20%20if%20(message.trim()%20===%20%22%22)%20%7B%0A%20%20%20%20document.getElementById(%22messageError%22).innerText%20=%20%22Message%20is%20required%22;%0A%20%20%20%20return;%0A%20%20%7D%20else%20%7B%0A%20%20%20%20document.getElementById(%22messageError%22).innerText%20=%20%22%22;%0A%20%20%7D%0A%0A%20%20//%20Simulate%20form%20submission%20(replace%20this%20with%20your%20actual%20form%20submission%20code)%0A%20%20setTimeout(function()%20%7B%0A%20%20%20%20document.getElementById(%22status%22).innerText%20=%20%22Message%20sent%20successfully!%22;%0A%20%20%20%20document.getElementById(%22contactForm%22).reset();%0A%20%20%7D,%201000);%0A%7D);%0A)


>**HTML**, **CSS**, and **JavaScript** are the cornerstones of web development, working together to create the structure, style, and interactivity of web pages.

**If you want to learen and practice this cornerstones of web development languages [clicke here](https://html-css-js.com/)**


## References 
1. [TechTarget What is OSI model](https://www.techtarget.com/searchnetworking/definition/OSI)
2. [Tpoint Tech OSI model](https://www.javatpoint.com/osi-model)
3. [Proofpoint What Is the OSI Model?](https://www.proofpoint.com/us/threat-reference/osi-model)
4. [NGINX What Is Load Balancing?](https://www.nginx.com/resources/glossary/load-balancing/)
5. [Medium Introduction to Load Balancing](https://medium.com/@gilbertomrf01/load-balance-4a68e7533b1a)
