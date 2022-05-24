---
title: Architectural design patterns
author: ianpowell
date: 2016-08-25 18:34:00 +0100
categories: [reading]
tags: [design-patterns]
---

## [Read Microsoft Cloud Design Patterns](https://docs.microsoft.com/en-us/azure/architecture/patterns/)

### [Ambassador pattern](https://docs.microsoft.com/en-us/azure/architecture/patterns/ambassador)
Create helper services that send network requests on behalf of a consumer service or application. An ambassador service can be thought of as an out-of-process proxy that is co-located with the client.

### [Anti-corruption layer](https://docs.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)
Implement a façade or adapter layer between different subsystems that don't share the same semantics. This layer translates requests that one subsystem makes to the other subsystem. Use this pattern to ensure that an application's design is not limited by dependencies on outside subsystems. This pattern was first described by Eric Evans in Domain-Driven Design.

### [Asynchronous Request-Reply](https://docs.microsoft.com/en-us/azure/architecture/patterns/async-request-reply)
Decouple backend processing from a frontend host, where backend processing needs to be asynchronous, but the frontend still needs a clear response.

### [Backends for Frontends](https://docs.microsoft.com/en-us/azure/architecture/patterns/backends-for-frontends)
Create separate backend services to be consumed by specific frontend applications or interfaces. This pattern is useful when you want to avoid customizing a single backend for multiple interfaces. This pattern was first described by Sam Newman.

### [Bulkhead](https://docs.microsoft.com/en-us/azure/architecture/patterns/bulkhead)
The Bulkhead pattern is a type of application design that is tolerant of failure. In a bulkhead architecture, elements of an application are isolated into pools so that if one fails, the others will continue to function. It's named after the sectioned partitions (bulkheads) of a ship's hull. If the hull of a ship is compromised, only the damaged section fills with water, which prevents the ship from sinking.

### [Cache-Aside](https://docs.microsoft.com/en-us/azure/architecture/patterns/cache-aside)
Load data on demand into a cache from a data store. This can improve performance and also helps to maintain consistency between data held in the cache and data in the underlying data store.

### [Choreography](https://docs.microsoft.com/en-us/azure/architecture/patterns/choreography)
Have each component of the system participate in the decision-making process about the workflow of a business transaction, instead of relying on a central point of control.

### [Circuit Breaker](https://docs.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker)
Handle faults that might take a variable amount of time to recover from, when connecting to a remote service or resource. This can improve the stability and resiliency of an application.

### [Claim Check](https://docs.microsoft.com/en-us/azure/architecture/patterns/claim-check)
Split a large message into a claim check and a payload. Send the claim check to the messaging platform and store the payload to an external service. This pattern allows large messages to be processed, while protecting the message bus and the client from being overwhelmed or slowed down. This pattern also helps to reduce costs, as storage is usually cheaper than resource units used by the messaging platform.

This pattern is also known as Reference-Based Messaging, and was originally described in the book Enterprise Integration Patterns, by Gregor Hohpe and Bobby Woolf.

### [Compensating Transaction](https://docs.microsoft.com/en-us/azure/architecture/patterns/compensating-transaction)
Undo the work performed by a series of steps, which together define an eventually consistent operation, if one or more of the steps fail. Operations that follow the eventual consistency model are commonly found in cloud-hosted applications that implement complex business processes and workflows.

### [Competing Consumers](https://docs.microsoft.com/en-us/azure/architecture/patterns/competing-consumers)
Enable multiple concurrent consumers to process messages received on the same messaging channel. With multiple concurrent consumers, a system can process multiple messages concurrently to optimize throughput, to improve scalability and availability, and to balance the workload.

### [Compute Resource Consolidation](https://docs.microsoft.com/en-us/azure/architecture/patterns/compute-resource-consolidation)
Consolidate multiple tasks or operations into a single computational unit. This can increase compute resource utilization, and reduce the costs and management overhead associated with performing compute processing in cloud-hosted applications.

### [CQRS](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs)
CQRS stands for Command and Query Responsibility Segregation, a pattern that separates read and update operations for a data store. Implementing CQRS in your application can maximize its performance, scalability, and security. The flexibility created by migrating to CQRS allows a system to better evolve over time and prevents update commands from causing merge conflicts at the domain level.

### [Deployment Stamps](https://docs.microsoft.com/en-us/azure/architecture/patterns/deployment-stamp)
The deployment stamp pattern involves provisioning, managing, and monitoring a heterogeneous group of resources to host and operate multiple workloads or tenants. Each individual copy is called a stamp, or sometimes a service unit or scale unit. In a multi-tenant environment, every stamp or scale unit can serve a predefined number of tenants. Multiple stamps can be deployed to scale the solution almost linearly and serve an increasing number of tenants. This approach can improve the scalability of your solution, allow you to deploy instances across multiple regions, and separate your customer data.

### [Edge Workload Configuration](https://docs.microsoft.com/en-us/azure/architecture/patterns/edge-workload-configuration)
The great variety of systems and devices on the shop floor can make workload configuration a difficult problem. This article provides approaches to solving it.

### [Event sourcing pattern](https://docs.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)
Instead of storing just the current state of the data in a domain, use an append-only store to record the full series of actions taken on that data. The store acts as the system of record and can be used to materialize the domain objects. This can simplify tasks in complex domains, by avoiding the need to synchronize the data model and the business domain, while improving performance, scalability, and responsiveness. It can also provide consistency for transactional data, and maintain full audit trails and history that can enable compensating actions.

### [External Configuration Store](https://docs.microsoft.com/en-us/azure/architecture/patterns/external-configuration-store)
Move configuration information out of the application deployment package to a centralized location. This can provide opportunities for easier management and control of configuration data, and for sharing configuration data across applications and application instances.

### [Federated Identity](https://docs.microsoft.com/en-us/azure/architecture/patterns/federated-identity)
Delegate authentication to an external identity provider. This can simplify development, minimize the requirement for user administration, and improve the user experience of the application.

### [Gatekeeper](https://docs.microsoft.com/en-us/azure/architecture/patterns/gatekeeper)
Protect applications and services by using a dedicated host instance that acts as a broker between clients and the application or service, validates and sanitizes requests, and passes requests and data between them. This can provide an additional layer of security, and limit the attack surface of the system.

### [Gateway Aggregation](https://docs.microsoft.com/en-us/azure/architecture/patterns/gateway-aggregation)
Use a gateway to aggregate multiple individual requests into a single request. This pattern is useful when a client must make multiple calls to different backend systems to perform an operation.

### [Gateway Offloading](https://docs.microsoft.com/en-us/azure/architecture/patterns/gateway-offloading)
Offload shared or specialized service functionality to a gateway proxy. This pattern can simplify application development by moving shared service functionality, such as the use of SSL certificates, from other parts of the application into the gateway.

### [Gateway Routing](https://docs.microsoft.com/en-us/azure/architecture/patterns/gateway-routing)
Route requests to multiple services using a single endpoint. This pattern is useful when you wish to expose multiple services on a single endpoint and route to the appropriate service based on the request.

### [Geode](https://docs.microsoft.com/en-us/azure/architecture/patterns/geodes)
The Geode pattern involves deploying a collection of backend services into a set of geographical nodes, each of which can service any request for any client in any region. This pattern allows serving requests in an active-active style, improving latency and increasing availability by distributing request processing around the globe.

### [Health Endpoint monitoring](https://docs.microsoft.com/en-us/azure/architecture/patterns/health-endpoint-monitoring)
Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals. This can help to verify that applications and services are performing correctly.

### [Index Table](https://docs.microsoft.com/en-us/azure/architecture/patterns/index-table)
Create indexes over the fields in data stores that are frequently referenced by queries. This pattern can improve query performance by allowing applications to more quickly locate the data to retrieve from a data store.

### [Leader Election](https://docs.microsoft.com/en-us/azure/architecture/patterns/leader-election)
Coordinate the actions performed by a collection of collaborating instances in a distributed application by electing one instance as the leader that assumes responsibility for managing the others. This can help to ensure that instances don't conflict with each other, cause contention for shared resources, or inadvertently interfere with the work that other instances are performing.

## Read up o here!

### [Materialized View](https://docs.microsoft.com/en-us/azure/architecture/patterns/materialized-view)
Generate prepopulated views over the data in one or more data stores when the data isn't ideally formatted for required query operations. This can help support efficient querying and data extraction, and improve application performance.

### [Pipes and Filters](https://docs.microsoft.com/en-us/azure/architecture/patterns/pipes-and-filters)
Decompose a task that performs complex processing into a series of separate elements that can be reused. This can improve performance, scalability, and reusability by allowing task elements that perform the processing to be deployed and scaled independently.

### [Priority Queue](https://docs.microsoft.com/en-us/azure/architecture/patterns/priority-queue)
Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority. This pattern is useful in applications that offer different service level guarantees to individual clients.

### [Publisher/Subscriber](https://docs.microsoft.com/en-us/azure/architecture/patterns/publisher-subscriber)
Enable an application to announce events to multiple interested consumers asynchronously, without coupling the senders to the receivers.

### [Queue based load leveling](https://docs.microsoft.com/en-us/azure/architecture/patterns/queue-based-load-leveling)
Use a queue that acts as a buffer between a task and a service it invokes in order to smooth intermittent heavy loads that can cause the service to fail or the task to time out. This can help to minimize the impact of peaks in demand on availability and responsiveness for both the task and the service.

### [Rate limiting](https://docs.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern)
Many services use a [throttling](#throttling) pattern to control the resources they consume, imposing limits on the rate at which other applications or services can access them. You can use a rate limiting pattern to help you avoid or minimize throttling errors related to these throttling limits and to help you more accurately predict throughput.

### [Retry](https://docs.microsoft.com/en-us/azure/architecture/patterns/retry)
Enable an application to handle transient failures when it tries to connect to a service or network resource, by transparently retrying a failed operation. This can improve the stability of the application.

### [Sage](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/saga/saga) [Example code](https://vasters.com/archive/Sagas.html)
The Saga design pattern is a way to manage data consistency across microservices in distributed transaction scenarios. A saga is a sequence of transactions that updates each service and publishes a message or event to trigger the next transaction step. If a step fails, the saga executes compensating transactions that counteract the preceding transactions.

### [Scheduler Agent supervisor](https://docs.microsoft.com/en-us/azure/architecture/patterns/scheduler-agent-supervisor)
Coordinate a set of distributed actions as a single operation. If any of the actions fail, try to handle the failures transparently, or else undo the work that was performed, so the entire operation succeeds or fails as a whole. This can add resiliency to a distributed system, by enabling it to recover and retry actions that fail due to transient exceptions, long-lasting faults, and process failures.

### [Sequential convoy](https://docs.microsoft.com/en-us/azure/architecture/patterns/sequential-convoy)
Process a set of related messages in a defined order, without blocking processing of other groups of messages.

### [Sharding](https://docs.microsoft.com/en-us/azure/architecture/patterns/sharding)
Divide a data store into a set of horizontal partitions or shards. This can improve scalability when storing and accessing large volumes of data.

### [Sidecar](https://docs.microsoft.com/en-us/azure/architecture/patterns/sidecar)
Deploy components of an application into a separate process or container to provide isolation and encapsulation. This pattern can also enable applications to be composed of heterogeneous components and technologies.

### [Static content hosting](https://docs.microsoft.com/en-us/azure/architecture/patterns/static-content-hosting)
Deploy static content to a cloud-based storage service that can deliver them directly to the client. This can reduce the need for potentially expensive compute instances.

### [Strangler fig](https://docs.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)
Incrementally migrate a legacy system by gradually replacing specific pieces of functionality with new applications and services. As features from the legacy system are replaced, the new system eventually replaces all of the old system's features, strangling the old system and allowing you to decommission it.

### Throttling](https://docs.microsoft.com/en-us/azure/architecture/patterns/throttling)
Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service. This can allow the system to continue to function and meet service level agreements, even when an increase in demand places an extreme load on resources.

### [Valet key](https://docs.microsoft.com/en-us/azure/architecture/patterns/valet-key)
Use a token that provides clients with restricted direct access to a specific resource, in order to offload data transfer from the application. This is particularly useful in applications that use cloud-hosted storage systems or queues, and can minimize cost and maximize scalability and performance.