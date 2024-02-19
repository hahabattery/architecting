---
layout: default
title: Overview
nav_order: 3
parent: Architecture
has_children: true
permalink: /docs/architecture
---

Design Pattern이나 리팩토링같은 상세한 범위보다 더 포괄적인 어플리케이션들 사이의 구조에 대해서 정리한다.

---
# 리소스들
[7 Best Places to Learn System Design (with Pictures)](https://javinpaul.gumroad.com/p/7-best-places-to-learn-system-design-with-pictures)

---
# StranglerFigApplication
* https://martinfowler.com/bliki/StranglerFigApplication.html


---
# API Composition
* https://microservices.io/patterns/data/api-composition.html


---
# CQRS
* https://microservices.io/patterns/data/cqrs.html


---
# 12 Factor App
* https://12factor.net/

---
# The Frugal Architect
* https://thefrugalarchitect.com/

---
# Proxy Pattern이란?
프록시 패턴이란 소프트웨어 디자인 패턴 중 하나로 오리지널 객체(Real Object) 대신 프록시 객체(Proxy Object)를 사용해 로직의 흐름을 제어하는 디자인 패턴이다.


---
# API Gateway / Backend for Front-End

microservices.io/patterns/apigateway.html


---
## Modern Development Practices
![Modern Development Practices](docs/ModernDevelopmentPracticesOverview.png)
- Unit Testing and Mocking : We are in the age of continuous integration and delivery, and the basic thing that enables those is having a good set of unit test in place. (Don’t confuse unit testing with screen testing done manually to check if the screen flow is right. What I mean by unit testing is JUnit test’s checking the business logic/screen flow in a java method (or) set of methods). Understand JUnit. Here is a good start : https://courses.in28minutes.com/p/junit-tutorial-for-beginners. Also understand the concept of Mocking. When should we mock? And when we should not? Complicated question indeed.  Understand one mocking framework : Mockito is the most popular one. Easymock is a good mocking framework as well.
- Automated Integration Tests. Automated Integration Tests is the second important bullet in enabling continuous delivery. Understand Fitnesse, Cucumber and Protractor.
- TDD (actually I wanted to put it first). Understand what TDD is. If you have never used TDD, then be ready for a rude shock.  Its not easy to change a routine you developed during decades (or years) of programming. Once you are used to TDD you never go back. I promise. This list of videos is a good start to understanding TDD. https://www.youtube.com/watch?v=xubiP8WoT4E&list=PLBD6D61C0A9F671F6. Have fun.
- BDD. In my experience, I found BDD a great tool to enable communication between the ready team (Business Analysts, Product Owner) and the done team (Developers, Testers, Operations). When User Stories are nothing but a set of scenarios specified is GWT (Given When Then) format, it is easy for the done team to chew at the user story scenario by scenario.  With tools like Cucumber & Fitnesse, tooling is not far behind too. Do check BDD out.
- Refactoring. Is there an Architect who does not encounter bad code? Understand refactoring. Understand the role of automation tests in refactoring.
- Continuous Integration. Every project today has continuous integration. But, the real question is “What is under Continuous Integration?”. Compilation, unit tests and code quality gate(s) is the bare minimum. If you have integration and chain tests, wonderful. But make sure the build does not take long. Immediate feedback is important. If needed, create a separate build scheduled less frequently for slower tests (integration and chain tests). Jenkins is the most popular Continuous Integration tool today.
![](docs/ModernDevelopmentPractices.png)



## Design

### Agile and Design

#### Evolutionary Design

### What are the 4 Principles of Simple Design?

### Design Focus Areas
![Focus](docs/Design-Focus.png)
- 4 Principles of Simple Design. https://www.youtube.com/watch?v=OwS8ydVTx1c&list=PL066F8F24976D837C
 - Runs all tests
 - Minimize Duplication
 - Maximize Clarity
 - Keep it Small
 - Code - Method, Class, jar etc
 - Component
 - Cycle Time (Short cycles)
 - Team Size

- Object Oriented Programming. Have good object, which have well-defined responsibilities.  Following are the important concepts you need to have a good overview of. These are covered in various parts in the video https://www.youtube.com/watch?v=0xcgzUdTO5M. Also, look up the specific videos for each topic.
 - Coupling :
 - Cohesion : https://www.youtube.com/watch?v=BkcQWoF5124&list=PLBBog2r6uMCTJ5JLyOySaOTrYdpWq48vK&index=9
 - Encapsulation
 - Polymorphism : https://www.youtube.com/watch?v=t8PTatUXtpI&list=PL91AF2D4024AA59AF&index=5
 - SOLID Principles : https://github.com/in28minutes/java-best-practices/blob/master/pdf/SOLIDPrinciples.pdf
- UML is next even though, formal use of UML is on the way down with Agile. However, I think UML is a great tool in the arsenal for a white board discussion on design. A picture is worth thousand words. I recommend having a good overview of the UML basics. Focus on these four before you move on to others.
 - Class diagrams
 - Sequence diagrams
 - Component diagrams
 - Deployment diagrams
- Design Patterns. Following video covers all the major design patterns. https://www.youtube.com/watch?v=0jjNjXcYmAU. My personal view : Design Patterns are good to know. Have a good idea on what each one of them does. But, that where it ends. I’m not a big fan of understanding the intricate details of each Design Pattern. You can look it up if you have a good overall idea about Design Patterns.


### Design Review
![Review](docs/DesignReview.png)

## Architecture
Details about the important parts of the systems and the constraints (boundaries, communication, standards, guidelines)

## How to be a good architect?
- PDF - How to be a good architect : https://github.com/in28minutes/java-best-practices/blob/master/pdf/How%20to%20be%20a%20good%20Software%20Architect.pdf

### Architect Responsibilities
- Having good governance in place. Good review processes in place for Architecture, Design and Code.
- Creating a clean architecture based on sound principles. Architecture covering all Non Functional Requirements.
- Ensuring teams are as productive as they can be. Right tools.
- Ensuring teams are following the best engineering practices.
- Ensuring clear communication about architecture with business and technical teams.
![Architect Responsibilities](docs/ArchitectResponsibilities.png)

### Architect Qualities
Most important qualities I look for in an Architect are
- Impeccable Credibility : Somebody the team looks up to and aspires to be.
- Super diagnostic skills : The ability to do a deep dive on a technology issue. When developers are struggling with a problem (having tried different things),  Can he/she provide a fresh pair of eyes to look at the same problem?
- Forward Thinker and Proactive : Never satisfied with where we are. Identifies opportunities to add value fast.
- Great Communication :  Communication in the widest sense. Communicating the technical aspects to the stakeholders, project management, software developers, testers, etc.
![Architect Qualities](docs/ArchitectQualities.png)

### Architecture Review
![Architecture Review](docs/ArchitectureReview.png)

### General

#### Why is it important to use Continuous Integration from Day 0 of the project?

#### What is a vertical slice? Why should you need it?

#### Why should you create a reference component?

#### Agile and Architecture. Do they go together?
- First of all I’m a great believer that agile and architecture go hand in hand. I do not believe agile means no architecture. I think agile brings in the need to separate architecture and design. For me architecture is about things which are difficult to change : technology choices, framework choices, communication between systems etc. It would be great if a big chunk of architectural decisions are made before the done team starts. There would always be things which are uncertain. Inputs to these can come from spikes that are done as part of the Done Scrum Team.But these should be planned ahead.
- Architecture choices should be well thought out. Its good to spend some time to think (Sprint Zero) before you make a architectural choice.
- I think most important part of Agile Architecture is Automation Testing. Change is continuous only when the team is sure nothing is broken. And automation test suites play a great role in providing immediate feedback.
- Important principles for me are test early, fail fast and automate.

### Layers
- PDF https://github.com/in28minutes/java-best-practices/blob/master/pdf/LayeringInJavaApplications.pdf

#### Business Layer
Listed below are some of the important considerations
- Should I have a Service layer acting as a facade to the Business Layer?
- How do I implement Transaction Management? JTA or Spring based Transactions or Container Managed Transactions? What would mark the boundary of transactions. Would it be service facade method call?
- Can (Should) I separate any of the Business Logic into seperate component or service?
- Do I use a Domain Object Model?
- Do I need caching? If so, at what level?
- Does service layer need to handle all exceptions? Or shall we leave it to the web layer?
- Are there any Operations specific logging or auditing that is needed?Can we implement it as a cross cutting concern using AOP?
- Do we need to validate the data that is coming into the Business Layer? Or is the validation done by the web layer sufficient?

#### Data Layer
- Do we want to use a JPA based object mapping framework (Hibernate) or query based mapping framework (iBatis) or simple Spring DO?
- How do we communicate with external systems? Web services or JMS? If web services, then how do we handle object xml mapping? JAXB or XMLBeans?
- How do you handle connections to Database? These days, its an easy answer : leave it to the application server configuration of Data Source.
- What are the kinos of exceptions that you want to throw to Business Layer? Should they be checked exceptions or unchecked exceptions?
- Ensure that Performance and Scalability is taken care of in all the decisions you make.

#### Web Layer
- First question is do we want to use a modern front end javascript framework like AngularJS? If the answer is yes, most of this discussion does not apply. If the answer is no, then proceed?
- Should we use a MVC framework like Spring MVC,Struts or should we go for a Java based framework like Wicket or Vaadin?
- What should be the view technology?  JSP, JSF or Template Based (Velocity, Freemarker)?
- Do you want AJAX functionality?
- How do you map view objects to business objects and vice-versa? Do you want to have View Assemblers and Business Assemblers?
- What kind of data is allowed to be put in user session? Do we need additional control mechanisms to ensure session size is small as possible?
- How do we Authenticate and Authorize users? Do we need to integrated external frameworks like Spring Security?
- Do we need to expose external web services?

### Web Services
![SOAP Web Services](docs/SOAPWebServices.png)
![Example Web Service](http://3.bp.blogspot.com/-RSSyK3JhGhw/VVTOQyaX2jI/AAAAAAAAAL8/BZL6jYEZXL4/s640/WebService_BrowserGoogle.png)
- Service Provider : Google.com is the service provider. Handles the request and sends a response back.
- Service Consumer : Browser is the service consumer. Creates Request. Invokes Service. Processes the Response.
- Data Exchange Format : In this example, Data Exchange is done over HTTP protocol. Request is HTTP request and Response is HTTP Response. Data exchange format can be something else as well. SOAP (in case of SOAP web services) and JSON (most RESTful services).

#### Advantages
- Re-use : Web services avoid the need to implement business logic repeatedly. If we expose a web service, other applications can re-use the functionality
- Modularity : For example, tax calculation can be implemented as a service and all the applications that need this feature can invoke the tax calculation web service. Leads to very modular application architecture.
- Language Neutral : Web services enable communication between systems using different programming languages and different architectures. For example, following systems can talk with each other : Java, .Net, Mainframes etc.
- Web Services are the fundamental blocks of implementing Service Oriented Architecture in an organization.

#### SOAP Web Services
![SOAP Web Services](http://4.bp.blogspot.com/-DyySh3d6XUs/VVTOTSlv3RI/AAAAAAAAAMQ/MGYhbgtYuo4/s640/WebService_SoapMessge.png)
- In SOAP web services, data exchange (request and responses) happens using SOAP format. SOAP is based on XML.
- SOAP format defines a SOAP-Envelope which envelopes the entire document. SOAP-Header (optional) contains any information needed to identify the request. Also, part of the Header is authentication, authorization information (signatures, encrypted information etc). SOAP-Body contains the real xml content of request or response.
- All the SOAP web services use this format for exchanging requests and responses. In case of error response, server responds back with SOAP-Fault.

##### WSDL
WSDL defines the format for a SOAP Message exchange between the Server (Service Provider) and the Client (Service Consumer).

A WSDL defines the following:
- What are the different services (operations) exposed by the server?
- How can a service (operation) be called? What url to use? (also called End Point).
- What should the structure of request xml?
- What should be the structure of response xml?

##### Marshalling and Unmarshalling
![Marshalling and Unmarshalling](http://1.bp.blogspot.com/-HsuVLQuzNIs/VVVh4yvb2-I/AAAAAAAAAMk/xy4B1YcirbU/s400/WebService_Marshalling.png)
SOAP web services use SOAP based XML format for communication. Java applications work with beans i.e. java objects. For an application to expose or consume SOAP web services, we need two things
- Convert Java object to SOAP xml. This is called Marshalling.
- Convert SOAP xml to Java object. This is called Unmarshalling.
JAXB and XMLBeans are frameworks which enable use to do marshalling and unmarshalling easily.

##### Security
- At transport level, SSL is used to exchange certificates (HTTPS). This ensures that the server (service producer) and client (service consumer) are mutually authenticated. It is possible to use one way SSL authentication as well.
- At the application level, security is implemented by transferring encrypted information (digital signatures, for example) in the message header (SOAP Header). This helps the server to authenticate the client and be confident that the message has not been tampered with.

#### REST Web Services
- PDF TO UPDATE https://www.mindmup.com/#m:g10B8KENIDghuHAYmFzM0daOU80SDA
- There are a set of architectural constraints (we will discuss them shortly) called Rest Style Constraints. Any service which satisfies these constraints is called RESTful Web Service.
- There are a lot of misconceptions about REST Web Services : They are over HTTP , based on JSON etc. Yes : More than 90% of RESTful Web Services are JSON over HTTP. But these are not necessary constraints. We can have RESTful Web Services which are not using JSON and which are not over HTTP.

##### Constraints
The five important constraints for RESTful Web Service are
- Client - Server : There should be a service producer and a service consumer.
- The interface (URL) is uniform and exposing resources. Interface uses nouns (not actions)
- The service is stateless. Even if the service is called 10 times, the result must be the same.
- The service result should be Cacheable. HTTP cache, for example.
- Service should assume a Layered architecture. Client should not assume direct connection to server - it might be getting info from a middle layer - cache.

##### Richardson Maturity Model
Richardson Maturity Model defines the maturity level of a Restful Web Service. Following are the different levels and their characteristics.
- Level 0 : Expose SOAP web services in REST style. Expose action based services (http://server/getPosts, http://server/deletePosts, http://server/doThis, http://server/doThat etc) using REST.
- Level 1 : Expose Resources with proper URI’s (using nouns). Ex: http://server/accounts, http://server/accounts/10. However, HTTP Methods are not used.
- Level 2 : Resources use proper URI's + HTTP Methods. For example, to update an account, you do a PUT to . The create an account, you do a POST to . Uri’s look like posts/1/comments/5 and accounts/1/friends/1.
- Level 3 : HATEOAS (Hypermedia as the engine of application state). You will tell not only about the information being requested but also about the next possible actions that the service consumer can do. When requesting information about a facebook user, a REST service can return user details along with information about how to get his recent posts, how to get his recent comments and how to retrieve his friend’s list.

##### RESTful API Best Practices
- Needs more work
- While designing any API, the most important thing is to think about the api consumer i.e. the client who is going to use the service. What are his needs? Does the service uri make sense to him? Does the request, response format make sense to him?
- URI’s should be hierarchical and as self descriptive as possible. Prefer plurals.
- Always use HTTP Methods. Best practices with respect to each HTTP method is described below:
 - GET : Should not update anything. Should be idempotent (same result in multiple calls). Possible Return Codes 200 (OK) + 404 (NOT FOUND) +400 (BAD REQUEST)
 - POST : Should create new resource. Ideally return JSON with link to newly created resource. Same return codes as get possible. In addition : Return code 201 (CREATED) is possible.
 - PUT : Update a known resource. ex: update client details. Possible Return Codes : 200(OK)
 - DELETE : Used to delete a resource.

##### JAX-RS
JAX-RS is the JEE Specification for Restful web services implemented by all JEE compliant web servers (and application servers).
Important Annotations
- @ApplicationPath("/"). @Path("users") : used on class and methods to define the url path.
- @GET @POST : Used to define the HTTP method that invokes the method.
- @Produces(MediaType.APPLICATION_JSON) : Defines the output format of Restful service.
- @Path("/{id}") on method (and) @PathParam("id") on method parameter : This helps in defining a dynamic parameter in Rest URL. @Path("{user_id}/followers/{follower_id}") is a more complicated example.
- @QueryParam("page") : To define a method parameter ex: /users?page=10.

Useful methods:
- Response.OK(jsonBuilder.build()).build() returns json response with status code.
- Json.createObjectBuilder(). add("id",user.getId()); creates a user object.

##### How should you document your REST Web Services?
- Swagger is the popular option
- Restdocs is popular too

### Microservices Architecture
![](http://eugenedvorkin.com/wp-content/uploads/2014/06/micro-service-architecture.png)
- Challenges with Monolith Applications - Longer Release Cycles because of Large Size, Large Teams and difficulty in adopting Automation testing and modern development practices
- “Keep it Small”. Small deployable components.
- Flights
 - Points
 - Offers
 - Trips
- Customer
 - Product
 - Order
 - Recommendations
- Key question to ask : Can we make a change to a service and deploy it by itself without changing anything else?

#### Microservices Characteristics
- Small, Lightweight
- Loosely coupled service-oriented architectures with bounded contexts
- Bounded Scope, Limited Scope, Solve one problem well
- Interoperable with message based communication
- Independently deployable and testable services
- Building systems that are replaceable over being maintainable
- “Smart endpoints and dump pipes”

#### Advantages
- Faster Time To Market
- Complete Automation Possibility
- Experimentation
- Technology Evolution
- Speed of innovation
- Team Autonomy : Enables creation of independent teams with End-to-End ownership
- Deployment Simplicity
- Flexible Scaling

#### Challenges
- Visibility
- Standardization
- Operations Team
- Determining Boundaries

#### What is the difference between Microservices and SOA?
Microservices have similar goals from SOA : Create services around your business logic.

Key Differences
- Big vendors hijacked SOA to link SOA with products like Enterprise Services Bus (Websphere ESB, Oracle ESB, TIBCO Business Works) etc
- SOA was tied to XML and its formalities and complexities
- SOA had centralized governance whereas microservice architecture recommends a decentralized governance.
- SOA did not focus on the independent deployability of the components.
- ESB was brought in to enable loose coupling for SOA based systems. The complexity with the ESBs etc in SOA lead to coining the term “dumb pipes smart endpoints”
Example:
![SOA](docs/SOA-For-Sales-App.png)
- Consider a banking application selling multiple products
- Along with Saving Account, a customer gets a Debit Card Free and a Insurance
Saving Account and Debit Card are different products managed by different product systems
- In SOA Architecture, the ESB took the order request from the sales application and handled the communication with creating the appropriate products. ESB ends up having a lot of logic. Heavy weight ESB.
- Microservices use more of an event driven architecture!
