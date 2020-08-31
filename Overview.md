# Primary Motivation

Software development exists to automate business processes. Software enable business to become more effective. Business needs to innovate faster, hence the software must be able to iterate faster as its complexity grows.

Aspects like software quality, developer productivity, continuous integration and delivery, and designs like Microservices Architecture exist to allow software to iterate faster, ultimately allowing the business to adapt faster.

## Fitness function

Different aspects that impact a business' success

- product market fit
- software quality -> maintanability -> how quick it can adapt to new or changing requirements

# Monolithic Architecture

## Benefits

- Simple to start
  - when the system complexity is small -> fast to iterate
  - easy to reason about the entire system
  - easy to develop, debug, deploy
  - cheap to throw away during initial experimentation when product-market fit is unclear

## Costs

- Slow down the iterations quickly as it grows
  - development (in IDE, dev build, CI) becomes slow -> optimizing it is hard
  - too big for developers to keep the mental model in their head
  - hard to gain familiarity of the codebase -> lead to one context "owner" -> fewer new ideas
- Change one part can easily break another part
  - more careful, test and checks in place -> slower
  - requires lots of coordination (leaky team abstraction)
- Hard to scale
  - CPU bound workload vs Memory or I/O bound
  - stateful vs stateless aspects of the system
- Long deploy and start-up time
  - cannot react quickly to increased loads
  - slower emergency (incident) recovery
- Lock-in to the initial decisions (e.g. initial stack, design, database and schema choices)
  - very expensive to change -> slow -> not optimized for ability to adapt to changes quickly

# Microservices Architecture

> (Maybe) an experienced developer can rewrite an entire service in 2 weeks.

## Properties

- Most changes must only impact one service (high-cohesion and loose-coupling).
  - If you frequently have to coordinate multiple services to deploy changes, you have a distributed monolith.

## Benefits

- Smaller, more contained code -> easier to understand and maintain -> more productive developers
  - Easier to onboard new members -> flexibility in moving developers around to new teams
  - More energy spent solving business problems (rather than maintaining complex mental model)
  - Safer changes -> confidence -> faster changes
  - The architecture discourage unintented coupling
- Leverage new technologies (frameworks, programming languages)
  - Reduce effort in undiffirentiated heavy-lifting
  - Being more productive
- Fast to build and deploy (or it is easier to optimize)
- Fast startup time
- Teams can develop in parallel -> reduced need for coordination
- Fault isolation
  - infrastructure could fail for a set of services but part of the system could still be working
  - avoid cascading failure
- Less likely to be locked in to the initial decisions
  - Easy (cheap) to experiment with new technologies to see whether they could solve business needs better

## Costs

- Baseline complexity is high (though it doesn't grow as quickly as monolith)
  - Inter-process communication (non-determinism)
  - Partial failure  
  - Multiple databases, distributed transaction (eventual consistency)
  - Local development and testing is harder -> can't just spin up
  - Testing and debugging distributed system
- Need more investments into toolings and automations upfront
  - operation: monitoring, observability, deployment
  - development, debugging and testing environment
  - continuous deployment
  - containerized infrastructure
- Developing and deploying changes that span multiple services requires careful coordination
