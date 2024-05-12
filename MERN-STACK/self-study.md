# Self study
# 1. Make a research 
# 1.1. what types of Database Management Systems (DBMS) exist and what each type is more suitable for. 
Database Management Systems (DBMS) come in various types, each suited for different purposes and scenarios. Here are some common popular types: `RDBMS`, `NoSQL DBMS`, `NewSQL DBMS`, `in-memory DBMS`, `columnar DBMS`, `multimodel DBMS` and `cloud DBMS`.

## Relational Database Management Systems (RDBMS):
RDBMS. Sometimes referred to as a SQL DBMS and adaptable to most use cases, RDBMS presents data as rows in tables with a fixed schema and relationships defined by values in key columns. RDBMS Tier-1 products can be quite expensive, but there are high quality, open source options such as PostgreSQL that can be cost-effective. Other examples of popular RDBMS products include Oracle, MySQL, Microsoft SQL Server and IBM Db2
- ACID (Atomicity, Consistency, Isolation, Durability) compliance ensures data integrity.
- Suitable for applications with structured data and complex queries and traditional transactional systems, such as banking applications, e-commerce platforms, and enterprise resource planning (ERP) systems.
- Examples: MySQL, PostgreSQL, Oracle, SQL Server.
## NoSQL Database Management Systems:
 Well-suited for loosely defined data structures that may evolve over time, NoSQL DBMS may require more application involvement for schema management. There are four types of NoSQL database systems: document databases, graph databases, key-value stores and wide-column stores. Each uses a different type of data model, resulting in significant differences between each NoSQL type.
- Designed for high scalability, performance, and flexibility.
- Not strictly ACID compliant, allowing for eventual consistency and high availability.
- Suitable for applications with large volumes of unstructured or semi-structured data, distributed across multiple servers, modern web applications, real-time analytics, content management systems, and IoT (Internet of Things) platforms.
- Examples: MongoDB (document-oriented), Cassandra (columnar), Redis (key-value), Neo4j (graph).

## NewSQL DBMS:
Modern relational systems that use SQL, NewSQL database systems offer the same scalable performance as NoSQL systems. But NewSQL systems also provide ACID (atomicity, consistency, isolation and durability) support for data consistency. A NewSQL DBMS is engineered as a relational, SQL database system with a distributed, fault-tolerant architecture. Other typical features of NewSQL system offerings include in-memory capability and clustered database services with the ability to be deployed in the cloud.
These are a newer class of databases that combine the scalability of NoSQL systems with the ACID (Atomicity, Consistency, Isolation, Durability) properties of traditional RDBMS.
- Examples include Google Spanner, CockroachDB, and NuoDB.
## In-memory DBMS:
  These databases primarily rely on main memory for computer data storage. They are optimized for high-performance applications where speed is crucial but can consume more resources. Therefore, an in-memory database is ideal for applications that require high performance and rapid access to data, such as data stores that support real-time HTAP (hybrid transactional and analytical process). Any type of DBMS (relational, NoSQL, etc.) can also support in-memory processing.
  - Examples include SAP HANA, VoltDB, and MemSQL.
## Columnar database management system
it stores data in tables focused on columns instead of rows, resulting in more efficient data access when only a subset of columns is required. It's well-suited for data warehouses that have a large number of similar data items.
- Popular columnar database products include Snowflake and Amazon Redshift.

## Multimodel database management system (DBMS)
it is a type of database that supports multiple data models within a single, integrated platform.  Users can choose the model most appropriate for their application requirements without having to switch to a different DBMS. For example, IBM Db2 is a relational DBMS, but it also offers a columnar option. Many of the most popular database systems similarly qualify as multimodel through add-ons, including Oracle, PostgreSQL and MongoDB. 
- Example Azure Cosmos DB and MarkLogic, were developed specifically as multimodel databases
# 1.2 Difference Between Relational DBMS and NoSQL:

### Relational DBMS:
- Structure: Data is organized into tables with rows and columns, linked by relationships.
- Schema: Requires a predefined schema with fixed structure and data types.
- Query Language: Uses SQL (Structured Query Language) for querying and manipulating data.
- Transactions: ACID compliance ensures data integrity with strong consistency.
- Scaling: Vertical scaling (adding more resources to a single server) is common but has limits.
- Examples: `MySQL`, `PostgreSQL`, `Oracle`

### NoSQL:
- Structure: Data can be organized in various ways, such as documents, key-value pairs, columns, or graphs.
- Schema: Typically schema-less or schema-flexible, allowing for dynamic changes to data structure.
- Query Language: Various NoSQL databases have their own query languages or APIs, often different from SQL.
- Transactions: Not all NoSQL databases provide ACID guarantees; some prioritize availability and partition tolerance (AP) over consistency.
- Scaling: Designed for horizontal scaling (adding more servers to a distributed system), offering better scalability for large datasets and high volumes of concurrent users.
- Examples: `MongoDB`, `Cassandra`, `Redis`, `Neo4j`

> Relational databases excel in structured data and transactional consistency, NoSQL databases offer flexibility, scalability, and better performance for modern applications dealing with large volumes of data and complex query patterns. The choice between them depends on the specific requirements and characteristics of the application.

# 2. Get yourself familiar with a concept of web application frameworks

A web application framework is a software framework designed to support the development of dynamic web applications, websites, and web services. It provides a structured approach to building web applications by offering a set of tools, libraries, and components that streamline common tasks and promote best practices designed to provide a foundation upon which developers can construct their applications.

## 2.1. Server-side (Backend) and Client-side (Frontend) Frameworks:

### Server-side (Backend) Frameworks:
- These frameworks are used to build the backend or server-side logic of web applications.
- They typically handle tasks such as routing, database interaction, authentication, authorization, and business logic.
- Server-side frameworks often run on web servers and generate dynamic HTML or other content that is sent to the client.
- Examples: Express.js (Node.js), Django (Python), Ruby on Rails (Ruby), Flask (Python), Laravel (PHP).

### Client-side (Frontend) Frameworks:
- These frameworks are used to build the frontend or client-side portion of web applications.
- They focus on user interface (UI) design, interactivity, and client-side logic execution within the web browser.
- Client-side frameworks often use JavaScript and CSS to create dynamic and responsive user experiences.
- Examples: React.js, Angular, Vue.js, Ember.js, Backbone.js.

## 2.2. Usage of Server-side and Client-side Frameworks:

### Server-side (Backend) Frameworks:
- Used for building the backend logic and functionality of web applications, such as handling requests, processing data, and generating dynamic content.
- Ideal for implementing server-side rendering (SSR) and serving HTML pages with dynamic data.
- Provide tools and libraries for interacting with databases, managing sessions, handling authentication and authorization, and implementing RESTful APIs.
- Enable developers to structure their codebase efficiently and follow best practices for scalable and maintainable server-side development.

### Client-side (Frontend) Frameworks:
- Used for creating interactive and dynamic user interfaces (UI) in web applications.
- Enable developers to build single-page applications (SPAs) and progressive web apps (PWAs) that run entirely in the web browser.
- Provide tools and components for managing application state, routing, form validation, and integrating with backend APIs.
- Support modern frontend development practices, such as component-based architecture, virtual DOM manipulation, and state management patterns.
- Improve developer productivity by offering reusable UI components, efficient data binding, and declarative programming paradigms.

# 3. Practice basic JavaScript syntax just for fun.
**JavaScript** is a high-level, dynamic, interpreted programming language that is primarily used for adding interactivity and dynamic behavior to web pages. Originally created by Brendan Eich at Netscape in 1995, JavaScript has since become one of the most popular and widely-used programming languages in the world.

## Key Features of JavaScript:

1. **Client-Side Scripting**: JavaScript is primarily used as a client-side scripting language in web browsers, enabling developers to manipulate the content and behavior of web pages dynamically.
2. **Versatility**: JavaScript is a versatile language that can be used for a wide range of tasks, including building web applications, server-side development (Node.js), desktop applications (Electron), mobile app development (React Native), and game development (Unity).
3. **Dynamic Typing**: JavaScript is dynamically typed, meaning that variables do not have predefined types and can hold values of any data type.
4. **Prototype-based Object-Oriented Programming**: JavaScript uses a prototype-based inheritance model where objects inherit properties and methods from other objects.
5. **Asynchronous Programming**: JavaScript supports asynchronous programming through callbacks, promises, and async/await syntax, allowing developers to write non-blocking code for handling tasks such as network requests and file I/O.
6. **ECMAScript Standardization**: JavaScript is standardized by the ECMAScript specification, which defines the core features and syntax of the language. Modern JavaScript implementations adhere to the latest ECMAScript standards (ES6, ES7, ES8, etc.).

## Use Cases of JavaScript:

- **Web Development**: JavaScript is the cornerstone of modern web development, used for creating interactive user interfaces, handling form submissions, validating input, implementing client-side validation, and more.
- **Web Applications**: JavaScript frameworks and libraries like React.js, Angular, and Vue.js enable developers to build robust single-page applications (SPAs) with rich user experiences.
- **Server-Side Development**: With the rise of Node.js, JavaScript can now be used for server-side development, allowing developers to build scalable and efficient web servers and APIs.
- **Mobile Development**: Frameworks like React Native and NativeScript enable developers to build cross-platform mobile applications using JavaScript.
- **Game Development**: JavaScript can be used for browser-based game development and building games with frameworks like Phaser.js and Three.js.

# Basic JavaScript Syntax

 The fundamental building blocks and functionalities of JavaScript syntax.

## Building Blocks

**Variables**: These are named storage units for data. You can declare them using keywords like `let`, `var`, or `const`, followed by the variable name and an assignment operator (`=`) to assign a value.

```
let message = "This is a message!";
const age = 25;
```

## Data Types

JavaScript can manage various data types, including:

- **Numbers**: Integers, decimals, etc.
- **Strings**: Text.
- **Booleans**: True/False.
- And more.

## Comments

Annotations within your code that provide explanations without affecting program execution. 

- Single-line comments: Use `//`.
- Multi-line comments: Use `/* */`.

## Statements

Complete instructions that tell the program what to perform. Each statement typically ends with a semicolon (`;`).

## Operators

### Arithmetic Operators

Perform mathematical calculations:

- Addition `+`.
- Subtraction `-`.
- Multiplication `*`.
- Division `/`.

### Assignment Operators

Assign values to variables:

- Basic assignment `=`.
- Shorthand versions like `+=` for addition assignment.

### Comparison Operators

Evaluate conditions:

- Equal to `==`.
- Not equal to `!=`.
- Less than `<`.
- Greater than `>`.

## Control Flow

### Conditional Statements

Allow you to execute code based on specific conditions:

- `if`, `else if`, and `else` statements.

### Loops

Repeat a block of code multiple times:

- `for` and `while` loops.

## Functions

Reusable blocks of code that perform specific tasks:

```javascript
function functionName(parameters) {
  // code block
}

```
# 4. Explore what RESTful API is? 
 A RESTful API (Representational State Transfer) is an architectural style for designing networked applications. It defines a set of constraints and principles for designing web services that are scalable, flexible, and easy to maintain. RESTful APIs use standard HTTP methods (GET, POST, PUT, DELETE) to perform CRUD (Create, Read, Update, Delete) operations on resources.

## Key Principles of RESTful APIs

1. **Client-Server Architecture**: Separation of concerns between client and server, allowing them to evolve independently.
2. **Statelessness**: Each request from the client to the server must contain all the necessary information to process the request. The server does not store any client state between requests.
3. **Uniform Interface**: Consistent and standardized interfaces between components, promoting simplicity and interoperability.
4. **Cacheability**: Responses must be explicitly marked as cacheable or non-cacheable to improve performance and scalability.
5. **Layered System**: Client interacts with the intermediary layers (proxies, gateways, etc.) without knowing the underlying details, enhancing scalability and security.
6. **Code on Demand (optional)**: Server can provide executable code to the client, extending functionality dynamically (e.g., JavaScript in web browsers).

## Benefits of RESTful APIs

- **Scalability**: Separation of concerns and statelessness make RESTful APIs highly scalable.
- **Flexibility**: Resources are represented in a uniform way, enabling clients to interact with them in a predictable manner.
- **Simplicity**: Standard HTTP methods and uniform interfaces simplify API design and implementation.
- **Interoperability**: RESTful APIs use standard protocols and formats (HTTP, JSON, XML), making them compatible with a wide range of platforms and programming languages.

## Use Cases in Web Development

In web development, RESTful APIs are used for various purposes, including:

### 1. Client-Server Communication

- **Data Retrieval**: Clients can retrieve data from servers using HTTP GET requests.
- **Data Manipulation**: Clients can create, update, or delete resources on servers using HTTP POST, PUT, DELETE requests.

### 2. Single-Page Applications (SPAs)

- **Backend Services**: SPAs communicate with backend servers via RESTful APIs to fetch data and update the UI dynamically.

### 3. Microservices Architecture

- **Service Communication**: Microservices communicate with each other via RESTful APIs, allowing them to function independently and scale horizontally.

### 4. Mobile App Development

- **Backend Integration**: Mobile apps interact with backend servers through RESTful APIs to fetch data and perform operations.

### 5. Third-Party Integrations

- **Integration with External Services**: RESTful APIs enable integration with third-party services and platforms, such as payment gateways, social media platforms, and more.

RESTful APIs play a crucial role in modern web development, enabling developers to build scalable, flexible, and interoperable applications.


# 5. Read what Cascading Style Sheets (CSS) is used for 
Cascading Style Sheets (CSS) is a style sheet language used for describing the presentation of a document written in markup languages like HTML and XML. CSS defines how HTML elements should be displayed on screen, in print, or in other media types.

## What CSS is Used For

CSS is used for various purposes in web development, including:

1. **Styling HTML Elements**: CSS allows developers to define the appearance of HTML elements, such as fonts, colors, margins, padding, and positioning.

2. **Layout Control**: CSS provides tools for creating responsive layouts, grid systems, and flexible designs, allowing developers to control the arrangement and positioning of elements on a web page.

3. **Responsive Web Design**: CSS enables the creation of responsive websites that adapt to different screen sizes and devices, ensuring a consistent user experience across desktops, tablets, and mobile devices.

4. **Animation and Effects**: CSS offers animation and transition properties that allow developers to create visually appealing effects, such as fade-ins, slide-outs, and hover effects, without relying on JavaScript.

5. **Customization and Theming**: CSS can be used to customize the appearance of web applications and websites, allowing developers to create unique branding and visual identities.

## Basic Syntax and Properties

### Syntax

CSS consists of a set of rules that define how HTML elements should be styled. Each rule consists of a selector and one or more declarations.

`css
selector {
  property: value;
}
`

**Example**

```
/* CSS comment */
body {
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
}

h1 {
  color: #333333;
  font-size: 24px;
}
```
_In this example:_

- The body selector styles the entire document body with a specific font family and background color.
- The h1 selector styles all `<h1>` elements with a specific text color and font size.
- Selector: Specifies which HTML elements the rule applies to. Selectors can be based on element names, classes, IDs, attributes, or hierarchical relationships.
- Property: Defines the style property to be applied to the selected elements.
- Value: Specifies the value of the style property.

## Common CSS Properties

CSS offers a wide range of properties for customizing the appearance and layout of web pages. Some of the most commonly used CSS properties include:

- **Font Properties**: `font-family`, `font-size`, `font-weight`, `font-style`.
- **Color and Background Properties**: `color`, `background-color`, `opacity`.
- **Layout Properties**: `width`, `height`, `margin`, `padding`, `display`, `position`.
- **Text Properties**: `text-align`, `text-decoration`, `line-height`.
- **Border and Box Properties**: `border`, `border-radius`, `box-shadow`.

These properties allow developers to control typography, colors, spacing, layout, and visual effects on web pages, making CSS an essential tool for web development.


# References
1. [Database management system (DBMS)](https://www.techtarget.com/searchdatamanagement/definition/database-management-system#:~:text=Popular%20database%20models%20and%20management,multimodel%20DBMS%20and%20cloud%20DBMS.)
2. [What are web frameworks and why you need them?](https://intelegain-technologies.medium.com/what-are-web-frameworks-and-why-you-need-them-c4e8806bd0fb)
3. [What is REST API](https://restfulapi.net/)
4. [Cascading Style Sheets
home page](https://www.w3.org/Style/CSS/Overview.en.html)
5. [Mdn Web doc JavaScript tutorial](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
6. [javaScript pluralsight](https://www.javascript.com/)
   
