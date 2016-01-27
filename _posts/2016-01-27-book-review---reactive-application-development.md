---
layout: 
title: Book Review - Reactive Application Development
categories: [IT]
tags: [IT, Programming]
published: True

---

Chapter 1 - Introduction

- It must react to its users (responsiveness)
- It must react to failure and stay available (resilience)
- It must react to variable load conditions (scalability)
- It must react to events (event orientation)

Discuss monolithic architectures, typically a centralised database with blocking drivers.
Tightly coupled middleware, cannot release a subset of our api because it’s impossible to version our API. Blocking nature, in order to create the composite view (waits for daily deals to render, images to download etc) before rendering the page. Tries to solve this problem by using concurrency. Synchronised blocks, hard to understand code. What was deterministic sequential code is now non deterministic. Job of the programmer is now to prune this non determinism.

Amdahls Law
It’s been proven that blocking of any kind, anywhere in the system will measurably impact scale due to:

1.   Contention; waiting for queues or shared resources.
2.   Coherency; the delay for data to become consistent”

Gunther law suggest that now matter how much hard you throw at a problem, you can make a blocking system worse.

CAP Theorem

-      Consistency
- Availability
- Partitioning

2 of 3 possible.

Consistency -

- Strong consistency is incredibly expensive and should be avoided at all costs.
- Eventual consistency - no new updates to a data item, eventually all accesses to that item will return the last updated value. Pillar in distributed computing.
- Casual consistency - stronger than eventual as ensures that operations are ordered.

Chapter 2

Akka - location transparency. Distributed by design. Local and remote interfaces are the same.

Good example of contention scenarios

Contention causes problems end of. What's the solution?

Share nothing

Actor model provides :

- A safe, efficient way to reason about computations in a concurrent environment

Main components an actor contains:

- State

Essentially Akka isolates each actor on a light weight thread, that protects it from the rest of the system. houses each thread on a lightweight thread. The actors themselves (housed inside a light-weight thread) run on a real set of threads, where a single thread may house many actors with subsequent invocations for a given actor occurring on a different thread. Akka also handles all the complexities of dealing with these concurrent interactions.

ActorRef - proxy for the implementation, can be local or remote. Location of the actor

Actor delivery mechanisms:

At most once :

Chapter 4

Tries to avoid 'A big ball of mud' - meaning a domain design that is  is “haphazardly structured, sprawling, sloppy, duct-tape and bailing wire, spaghetti code jungle.
This typically happens due to time contraints, budget, inexperience, visibility (UI visible, back end not so)

How can you avoid this?

- Control fragmentation with continuous integration - different teams breaking a domain down into smaller subsections and working on them independently is not good. To get around this, ideas and code should be merged daily.
- Avoid anaemic domain models - might at first seem reasonable and potentially map to a real life domain object, but when examined closer it has no real behaviour. An anaemic model would be a simple data structure with simple getters and setters.

Bounded Contexts

You begin modelling a domain by dividing up major areas into a set of bounded contexts or subdomains.
For example a flight domain could be divided into three major areas of behaviour:

- Aircraft
- Tower concerned with multiple aircraft arriving
- Ground control concerns itself with moving multiple airplanes around the airport

Each piece above can exist at runtime on separate hardware and across different physical locations.

Anti Corruption Layer
A subdomain can communicate with other contexts, when it does it uses what DDD term 'Anti Corruption Layer' or ACL.
It's an outer layer that sits inside a bounded context to convert and validate data to and from other contexts and external systems.
A request might come into the aircraft bounded context to reduce altitude by 2000 feet. The ACL has to determine whether this is a valid request and does not
smash the aircraft into the ground.

The use of bounded contexts within a domain allows for services to be decoupled and communicate via messaging.

Ubiquitous Language
The ubiquitous language is a set of terms describing all aspects of the domain that are understood by both the domain experts and the developers.