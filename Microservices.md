# Microservices Architecture

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
