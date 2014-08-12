---
layout: 
title: Reactive Design Patterns - Skimmers Guide Chapter 1
published: True
categories: []
tags: [reactive, software, architecture]
---

+ Outlines the importance of responsiveness to users. Books will give tips how to stay responsive in the face of variable load, partial outages and program failure. 
+ Presents the reactive manifesto which is a small summary of the challenges facing computer systems. 
  1. It must react to its users (responsiveness)	
  1. It must react to failure and stay available (resilience)	
  1. It must react to variable load conditions (scalability)	
  1. It must react to events (event orientation)	

1. Responsiveness - must react to users.
  * Old way, consider val x*  = f(30) - evaluation of the function occurs synchronously. Falls over when it needs to be distributed among multiple cores etc.
  * In 94, used to be a huge difference between local and remote calls.
  * Major advances in networking hardware. No longer the case, a remote call is almost the same. Need to treat remote and local calls the same.
  * To be responsive, we need the latency to be as small as possible. We need to define an upper limit on this latency, why? it allows users to cap their wait times accordingly.
  * Parrallization - 3 sub tasks executed sequentially, total response is sum of all 3 latencies. 3 sub tasks executed in parallel, the total response is the time to complete the slowest of the 3 sub tasks.
  * Sequential way
  
      ```
	  	val response1 = getVal1()
	  	val response2 = getVal2()
	  	val response3 = getVal3()
	  	def compose(response1, response2, response3)
      ```
  This way is blocking. 
  A better way would be when the last service completes its job it organises dispatching the event.Think of it as the last sub task that comes back dispatches the event.
  Better way is through ```CompletableFuture``` or the scala way as below:

	    val fa: Future[ReplyA] = taskA()
	   	val fb: Future[ReplyB] = taskB()
	   	val fc: Future[ReplyC] = taskC()
	    
	    val fr: Future[Result] = for (a <- fa; b <- fb; c <- fc)
		                         yield aggregate(a, b, c)

  * How do you choose best case latency? 3 mins 16 is too long, 50 ms sounds good, but 1 in 100 requests might never go through even though the service is working.
  * This is basically saying that choosing the upper latency bound is always going to be a tradeoff between reliability and responsiveness.
  * Bounding latency, can use bounded queues. Reqeusts coming in faster then we can process responses. In 1s we receive 110 requests, we process 100. This leads to an extra 0.1s of latency. Latency can quickly add up. Use explicit queues. Kind of confusing this section [REVISIT]
  * Circuit Breakers - interest concept. When a service is being overwhelmed, and the time is consistently rising above the threshold. The circuit breaker will trip and requests will now take a different route - degraded service or failing fast etc. When it has time to recuperate, circuit breaker will resume as normal allowing requests to flow correctly through teh system.
   
2. React to failure
  * Systems will go down, software, hardware, human. It's only a matter of when.
  * Install bulkheads to compartments to isolate failure.
  * Manifesto calls it resilience instead of reliability as it's about how quickly the service(s) can bounce back from a production issue.
  * Supervisor role, instead of coupling fault tolerance with business logic, seperate out the fault tolerance section.

3. Reacting to load
  * To react to load, a system needs to do be able to do 2 things:
    1. Need to split up individual streams of work items that can be worked on on different machines in parallel.
    2. Route traffic in the direction of another service depending on load, or spin up new instances if reaching capactity or wind down services if very quiet. 
  * number of requests serviced by the system = average number of requests * time spent processing. There is no reason why we can't automate this figure. This will allow us to determine how much we should parallelize. We can capture requests at entry and exit. 
  * This service would act like a supervisor or monitoring service that polls the system and gathers this metric to determine whether it needs to scale up.
  
4. Reacting to events
  * The focus should be on events instead of method calls. 
  * Should focus on high level interactions instead of micromanaged getters/setters that promote tight coupling etc.
