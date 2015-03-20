---
layout: 
title: Book Selection - Microservices Architecture
categories: []
tags: []
published: True

---

Chapter 1- Key benefits

   * Technology Heterogeneity - pick the right tool for the right job
   * Resilience - the notion of a bulkhead, if one component fails, then the failure doesn't cascade, on a monolithic service, if the service fails, everything fails.
   * Improved scaling - with smaller services, you can scale those services that need scaling
   * Ease of deployment - make a change to an individual service and deploy it independently
   * Smaller teams working on smaller services tend to be more efficient
   * Individual service being so small, the cost of replacement is tiny in comparison to a larger monolithic app

Chapter 2- Principles/Practices role of an architect


Think of an architect like a town planner, different zones, you should care less about what happens between the zones/service boundaries. Need to spend a lot of time thinking about how the services talk to each other. Important to pick principles/practices that your team sticks too.


   * Example principles might be (consistent interfaces between services, no silver bullets, make choices that favor rapid feedback and change)
   * Example practices might be (standard HTTP/Rest, Standard monitoring service (nagios etc) Encapsulate legacy, Published integration model)

Questions: Have never done this before, has anyone else you know?


Chapter 3 - What makes a good service?


   * Loosely coupled - make a change to a service and deploy independent of other services
   * Strong cohesion - related behaviour to sit together (same zone), if we have to make changes to behaviour and it effects lots of different services, not good. Good to find boundaries in our problem domain that helps ensure related behaviour in one place - leads to chatty apps.

Questions: We don't do this in DCJ, why?


Bounded Context


   * From DDD, the idea that a given problem domain consists of multiple bounded contexts. Within a bounded contexts, there are things that communicated within the boundary, and thingNbcbchocb b b Khulna bank s (models) that communicate outside. Shared externally. 

Questions/Discussion: How to you go about implementing bounded contexts? He suggests not by data that is shared, but by business capability. 
Answer: He suggests starting off with coarse grained services, sub divide y into nested contexts if required.
Discussion:


Chapter 4 - Integration


   * Introduce a service to talk to the database rather than the external apps talking directly to the database.
   * Talks a lot about different technologies, RPC (SOAP/RMI), HTTP(REST), HATEOAS, JSON vs XML.
   * Async, he recommends wrap an overall interaction which is asynchronous into a bunch of synchronous calls. Discussion point below.


Discussion - Above, HATEOAS over REST. Chatty apps.
Discussion - (Pg 66) Lifecycle of key domain events
Discussion - wrap an overall interaction which is asynchronous into a bunch of synchronous calls. Discuss. Event models with synchronous rest over http can be a very powerful combination


Chapter 5 - Integratating with 3rd Party software


   * Nuno did this, they had a micro service that acted as a nice facade to a 3rd party system.
   * This facade/service should be split/placed in the bounded context that makes sense. ie Employee or Payments

Chapter 6 - Breaking the monolith/legacy


   * Identify what high level bounded contexts exists with the system/organisation
   * Create package/modules based on these bounded contexts.
   * What seam do you start with first? lots of factors, technology choice, pace of change
   * Talks a lot about breaking up databases which i didn't really investigate

Chapter 7 - Deployment

   * Recommends a single CI build per micro service
   * One of the big takes from this book is that he stresses that we should be able to deploy our services independent of each other.
   * Various types of artefacts that we can move across pipeline to production these include:
   * 
      * Platform specific artefacts: use configuration management tools like puppet and chef, downside is the time it might take to run scripts on a machine (in AWS, download and install java, mysql etc -  prob 10 mins) especially in on demand computing platforms, complex deployment scripts 
      * Operating system artifacts: rpm packages, msi etc. use tools native to OS to install it. upside, simplified development approach. 
      * Custom images: build image once, launch copies, no need to install dependencies. Down side, can take long to build an image. Large image sizes over a slow wifi connection.
      * Images as artefacts: ie Netflix AWS AMIs, pro’s easy way to implement an immutable server.
   * Immutable servers, not allowing anyone to make config changes to a box once an image has been built. If config changes differ to whats in source control, called configuration drift. Steps around this, we can disable SSH.
   * Continuous delivery, moving through the pipelines, problems can occur if environments differ greatly. multiple load balanced hosts versus a single stand alone laptop. You want to ensure that your environments are more production like to catch issues (i.e. session replication etc), although there is a balance between time to reproduce production environment and speed of development and feedback time.
   * Differing artefacts across different environments, i.e. Customer-service-test artefact, and customer-service-prod artefact, how can we be sure that we have verified the software that ends up in production. 
   * Better to use a single artefact and manage configuration separately. 
   * Multiple services per host - while cost efficient has downsides: 
   * 
      * limit your deployment options, image based deployments and immutable servers (unless we tie various services together in a single artefact which we really don’t want to do)
      * Want to be able to deploy each service independently.
      * Sometimes end up treating different services with different needs the same (some might requrie a faster box, others more scaling options)
   * Using application containers (mostly downsides), 5 java services in 1 servlet container (and 1 jvm) versus 5 jvms, while this is nice the downsides outweigh this:
   * 
      * Lots offer shared in memory state which is something we really want to avoid as affects scaling our services
      * slow spin up times
      * netty better.
   * Single service per host
   * 
      * monitoring and remediation much easier.
      * single point of failure, does not affect multiple services
      * more hosts to manage though
   * Platform as a service
   * 
      * Very useful, think heroku, handles scaling etc.
      * not great if you have a non standard application

      Physical to virtual

   * Traditional world of virtualization
   * 
      * Allows us to slice up a physical server into separate hosts
      * Sock drawer analogy, dividers take up space also. 
   * Vagrant
   * 
      * provides you with a virtual cloud on your laptop
      * Makes it easier for you to create production like environments on your local machine.
      * Spin up multiple vms, have them mapped to individual directories, makes changes to files and see changes reflected directly
      * Downside, running lots of VMs will tax the average developer machine.
   * Docker
   * 
      * 


Chapter 8 testing 


   * Use consumer driven contracts instead of end to end tests
   * What's wrong with end to end tests, can be brittle and flaky, people lose trust quickly.
   * Martin fowler talks about test double as a substitute for spy's mocks stubs etc. read his post 

Chapter 9 monitoring 


   * Correlation ids is a useful concept, where you pass this along to all services. 
   * Graphite seemed like a nice too, simple Ali for generating charts of your service 
   * Ssh multiplexors allow you to perform the same command on multiple ssh terminals.
   * Use metrics to see what services/features are being used.
   * Hysterix is a circuit breaking library 



Chapter 10 security 

   * Types
   * 
      * HTTP Basic Authentication - username password sent as part of HTTP header - highly problematic as username and password not sent in a secure manner, should only be used over HTTPS
      * HTTP Basic Authentication over HTTPS - Due to nature of https, the client gains strong guarantees that the server it is talking to is who it thinks it is. Challenge is that each server needs to manage it’s own certificates which is a problem when shared over multiple machines, certificate issuing process but an operational burden.
      * TLS - Transport Layer Security. Used to verify that the client is who we think we are talking to (unlike HTTPS). Each client has an X.509 certificate installed, used to establish a link between client and server. 
      * HMAC over HTTP - Body request along with key is hashed.Request uses its own copy of the private key and the request body to recreate the hash, if it matches then it allows the request. Man in the middle messes with  the request then the hash won’t match.Also, private key is never sent in transit, so cannot be compromised. Downside is that the client and server need a shared secret. Amazon use this for S3, if use it, use a long key SHA-256.
      * API Keys - Not security as such, but more to protect services for others. Using an issued API key, twitter can limit how many requests a given person has, stopping overload. Amazon use API keys to limit what services a user has.
   * Where do we store our keys? One option is to use a key vault which your server can access when it needs a key.


Chapter 11 Conways law and system design

   * Geographical boundaries between people involved with the development of a system can be a great way to drive when services can be decomposed. This problem came from when one service was worked on my 2-3 geolocated teams. Nightmare, fine grained communication lost. Align service ownership to co located teams, which themselves are aligned around the same bounded contexts of the organisation.
   * Internal open source - one solution to service ownership. Instead people submit changes for acceptance. Need a core team of committers (gatekeepers)



Chapter 12 Microservices at scale

   * Failure is everywhere. Many organisations try to put processes and controls to try and stop failure from occurring. But put little or no-thought into actually making it easier to recover from failure in the first place.
   * Degrading functionality - Sample page on an ecommerce site, we might use 3-4 services, basket, catalogue, 
