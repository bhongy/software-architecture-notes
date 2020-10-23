# Microservices Architecture

## Complexity
Microservices architecture introduces new sets of challenges compare to the monolithic architecture.
- partial failure
- no transaction i.e. eventual consistency -> how to maintain invariants
- implementing transaction and queries that span across multiple services
- polyglot (different db expertise, different programming languages expertise)

## API Gateway
API Gateway is essential to decouple clients from the services.
- changing, splitting, and combining services is part of evolving microservices and should be able to achieve without the need to coordinate the changes with the clients.

API Gateway also solves:
- provide client-specific API
- reduce number of requests (aggregate result suitable for client)
- eliminate the need of client -> services discovery
- protocol translation (to web friendly: gRPC/kafka -> http)

## Communication
- it is critical to have an explicit interface between interprocess communication (e.g. use gRPC + protobuf)
- microservices are distributed and should be assumed to run on separate machines
- must deal with partail failures

## Dealing with partial failures
- use timeout (avoid starving thread pools when upstream services are down)
- use limited thread pool (max # of outstanding requets then fail fast)
- use error rate -> circuit breaker (fail immediately)

> Think about evolving APIs

## Messaging communication
- benefits: decouple client <> services i.e. if the service is down (temporarily) the broker can buffer the messages without affecting the client
- drawbacks:
  - add complexity to the system (high-availability service, message broker, to maintain)
  - hard to debug
  - not suitable (hard) to implement req-res API
  - error handling is harder
  - result aggregation (from multiple stream) is harder (you never know if the message will arrive)
  
## Service Registration
- register on startup
- unregister on shutdown
- unregister on crash
- unregister on unhealthy (e.g. running but unable to handle load)

## Service data characteristics
(from event-driven-microservices by Chris Richardson)
- velocity and volume
- what kind of data e.g. binary blob, relational, text, graph
- fixed or adhoc queries
- latency requirement of the data ("real-time" vs batch processing)
  - how soon an update being made in the data should show up in the query
- distributed across AZs? update simultaneously
- access patterns (write intensive, read intensive)

## Data Consistency
- avoid two-phase commit - not suitable for microservices (degrade availability, partition tolerance)
- ? how to atomically update the database **and** publish the event

## Ref: types of databases
- relational (SQL)
- key-value store
- text search
- document database
- graph database
- blob storage

## Automation
- API-driven machine provisioning - ability to provision isolated operating systems on-demand using an API
  - Declarative environment provisioning (e.g. Terraform)
- API-driven os configuration (Ansible, Chef, Puppet)
- CI/CD (build, test, container image creation, deploy)

## Database per service
- a service owns its database(s)
- the database is treated as an implementation detail of the service
- the service expose APIs (not the database)
- the service owns the _data_
- the service is responsible for its data access performance (throughput, qps, write/read heavy, etc)
- separate table, schema (rather than the database server) can be an intermediate compromised (lose independent scalability) as long as there is a strong barrier to make it impossible for a service to access another service's database
- benefits:
  - ensure that the services are loosely coupled and API is the only way to expose public interfaces
  - each service can use the type of database that is best suited to its needs (SQL, NoSQL, Graph DB)
  - each service can scale read/write capabilities based on its needs
- concerns:
  - difficult to implement business transactions that span multiple services (bad design around bounded context exacerbate this problem)
  - implementing queries that join data that is now in multiple databases is challenging
  - inconsistent database security across services
  - requires know-how in multiple database technologies

## Notes
- design services based on business processes/workflows i.e. capabilities (e.g. place an order) not entities (e.g. customer)

## Common Design Characteristics
- one service per container/vm -> scale independently
- communicate over APIs or messages over shared data/db
  - sharing db creates high-degree of tight coupling (all internal is public) and severely damage ability for services to evolve independently (speed)
- a service is owned entirely by a single team (never multiple teams own parts of a service)
  - autonomy -> reduce a need of coordination among teams (release in lock-step) -> conway's law
  - a sign of high-cohesion, loose coupling
- service boundaries are defined around business domains (bounded contexts) rather than technical boundaries like database vs APIs, etc.

## Benefits
- avoid the complexity arising from unintentional and inappropriate coupling between components that should not know about each other
  - from: [Martin Fowler Blog: Micro Frontend](https://martinfowler.com/articles/micro-frontends.html)
