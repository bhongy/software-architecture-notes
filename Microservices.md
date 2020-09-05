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

## Notes
- database per service
  - a service owns its database(s)
  - the database is treated as an implementation detail of the service
  - the service expose APIs (not the database)
  - the service owns the _data_
  - the service is responsible for its data access performance (throughput, qps, write/read heavy, etc)
