# Self Study For Devops Foundation
1. ## What  Software Development Life cycle (SDLC) 
The Software Development Life Cycle (SDLC) is a structured process that enables the production of high-quality, low-cost software, in the shortest possible production time. The goal of the SDLC is to produce superior software that meets and exceeds all customer expectations and demands.
The SDLC includes different phases, and each phase has a specific process and deliverables. Although SDLC meaning might vary for each development team, the most common phases include:

- **Requirements gathering and analysis**: Business analysts work with stakeholders to determine and document the software requirements.

- **System design**: Software architects translate the requirements into a software solution and create a high-level design.

- **Implimentation**: Developers build the software based on the system design.

- **Testing**: The software is tested for bugs and defects and to make sure that it meets the requirements. Any issues are fixed until the software is ready for deployment.

- **Deployment**: The software is released to the production environment where it is installed on the target systems and made available to users.

- **Maintenance and support**: This ongoing process include training and supporting users, enhancing the software, monitoring performance, and fixing any bugs or security issues.

### Common SDLC Models
In software development, there are various frameworks, or “models,” of the Software Development Lifecycle (SDLC), which arrange the development process in different ways. These models help organizations implement SDLC in an organized way. Here are some of the most commonly used software life cycle models.

1. Agile Model
This model arranges the SDLC phases into several development cycles, with the team delivering small, incremental software changes in each cycle. The Agile methodology is highly efficient, and rapid development cycles help teams identify issues early on, but overreliance on customer feedback could lead to excessive scope changes or project termination. It's best for software development projects that require flexibility and the ability to adapt to change over time.

2. Waterfall Model
This model arranges all the phases sequentially, with each new phase depending on the outcome of the previous one. It provides structure to project management, but there is little room for changes once a phase is complete, so it's best for small, well-defined projects.

3. Iterative Model
With this model, the team begins development with a small set of requirements and iteratively enhances versions until the software is ready for production. It's easy to manage risks, but repeated cycles could lead to scope change and underestimation of resources. This model is best for projects that require high flexibility in their requirements and have the resources to handle multiple iterations.

4. Spiral Model
This model combines the iterative model's repeated cycles with the waterfall model's linear flow to prioritize risk analysis. It's best for complex projects with frequent changes but can be expensive for smaller projects.

5. Big Bang Model
The Big Bang Model is a unique approach where developers jump right into coding without much planning. This means that requirements are implemented as they come, without any kind of clear roadmap. If changes are needed, it can require a complete revamp of the software. While this model isn't great for larger projects, it’s best for academic or practice projects, or smaller projects with only one or two developers. Essentially, it's a model that works well when requirements aren't well understood and there's no set release date in sight.

### SDLC vs DevOps
DevOps is a set of practices that combines software development (Dev) and IT operations (Ops) to enable faster and more frequent software delivery. It involves collaboration, automation, and monitoring throughout the software development lifecycle.

> Here are the distinctions between SDLC and DevOps:

- SDLC is a methodology for managing software development, while DevOps is a cultural shift that promotes collaboration between development and operations teams.
- SDLC focuses on delivering software that meets the user requirements, while DevOps focuses on delivering software that meets the business objectives.
- SDLC involves different phases, such as planning, design, coding, testing, and deployment, while DevOps involves continuous integration, continuous delivery, and continuous monitoring.

2. ## What Is LAMP Stack Mean
LAMP is a popular open-source technology stack used primarily in web development.

*The stack consists of four components necessary to establish a fully functional web development environment. The first letters of the components' names make up the LAMP acronym:*

- **Linux** is an operating system that runs all the components.
- **Apache HTTP Server** is a web server software that delivers both static and dynamic web pages to users.
- **MySQL** is a relational database management system used for creating web databases and storing and managing dynamic content.
- **PHP, Perl, and Python** are programming languages for developing web applications. Developers can choose one of the three languages for building backend solutions.

3. ## Read about 'Chmod' and 'Chown' command in linux
In Linux, managing file and directory permissions is crucial for maintaining system security and ensuring authorized access to resources. Two essential commands for this purpose are chmod and chown
3.1. **Chmod (change mode)**:

The chmod command used to modify the permissions associated with a file or directory. These permissions determine which users (owner, group, and others) can access the file and what actions they can perform (read, write, and execute).

**Understanding Permissions**:

User (u): The owner of the file or directory.
Group (g): The group to which the file or directory belongs.
Others (o): Everyone else on the system.
Read (r): Allows users to view the contents of a file or list the contents of a directory.
Write (w): Allows users to modify the contents of a file or create/delete files within a directory.
Execute (x): Allows users to execute a file if it's a program or script.
Specifying Permissions:

**There are three ways to specify permissions with chmod**:

Symbolic Notation:
- _r (read)_
- _w (write)_
- _x (execute)_
- _- (absence of permission)_
  
_Octal Notation:_

Permissions are represented by a 3-digit octal number (0-7) where each digit corresponds to the permissions for user (first digit), group (second digit), and others (third digit).
Each digit is calculated by adding the values of the corresponding permissions:
r (4)
w (2)
x (1)

_Ownership Indicator (optional)_:

u (user)
g (group)
o (others)
a (all)

**Examples**:

- chmod +rwx filename.txt: Grants read, write, and execute permissions to the owner, group, and others (octal equivalent: 777).
- chmod 644 filename.txt: Sets permissions to read (4) for owner and group, and read-only (4) for others (octal notation).
- chmod go-w filename.txt: Removes write permission (w) from the group (g) and others (o) for filename.txt (symbolic notation).

3.2. **Chown (change owner)**:

The chown command used to change the ownership of a file or directory. This means you can specify which user account on the system will be considered the owner of the resource.

_Syntax_:

chown [options] owner:group filename
owner: The username of the new owner.
group: The name of the new group (optional).
filename: The file or directory whose ownership you want to change.
**Examples**:

- chown johndoe:users filename.txt: Changes the owner of filename.txt to the user johndoe and the group to users.
- chown johndoe filename.txt: Changes the owner of filename.txt to johndoe, keeping the current group ownership.

4. ## Learn what TCP and UPD tearms mean and how they arre different. List down port most commenly used in web (http,https,ssh,telnet,sftp,telnet)
TCP (Transmission Control Protocol) and UDP (User Datagram Protocol) are both fundamental protocols used for communication over networks like the internet. They differ in their approach to data transmission, offering distinct advantages and disadvantages for various applications.

**TCP (Transmission Control Protocol):**

- TCP is a connection-oriented protocol that provides reliable, ordered, and error-checked delivery of data between devices over a network.
It ensures that data packets are delivered in the correct order and are retransmitted if they are lost or corrupted during transmission.
- TCP establishes a connection between two devices (sender and receiver) before data transfer and ensures that the data is successfully delivered.
- It is commonly used for applications that require reliable and sequential data transmission, such as web browsing, email, file transfer (e.g., FTP), and remote access (e.g., SSH).

**UDP (User Datagram Protocol)**:

- UDP is a connectionless protocol that provides a lightweight and fast transmission of data between devices over a network.
- Unlike TCP, UDP does not establish a connection before data transfer and does not guarantee delivery or order of data packets.
- It is often used for real-time applications, such as streaming media, online gaming, VoIP (Voice over IP), and DNS (Domain Name System), where a small delay is acceptable and dropped packets can be tolerated.

**Commonly Used Web Ports**:

| Service                                          | Port | Protocol |
|--------------------------------------------------|------|----------|
| HTTP (Hypertext Transfer Protocol)              | 80   | TCP      |
| HTTPS (Hypertext Transfer Protocol Secure)      | 443  | TCP      |
| SSH (Secure Shell)                              | 22   | TCP      |
| Telnet                                           | 23   | TCP      |
| SFTP (SSH File Transfer Protocol)               | 22   | TCP      |
| FTP (File Transfer Protocol)                    | 21   | TCP      |

5. ## Basic text editing in vi(vim) editor 
[Practice here](https://www.openvim.com/)
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5a228541-9c08-471e-aa16-c1f6566fa356)
