# Chapter 1: Reliable, Scalable and Maintainable Applications


## Introduction
The first part of the book focuses towards fundamentals of designing a data-intensive application. These fundamentals applies for a single-node as well as in a distributed setting.

The first chapter introduces the terminology and concepts of reliability, scalability and maintainability and how we can achieve these for our systems.

* Most applications these days are "data-intensive" rather than compute-intensive - CPU in not the bottleneck which means -
    * it needs to process large quantity of data
    * the complexity of data is high
    * time to process the data should be minimum

* Building blocks of any data-intensive application
    * Store data and retrieve later (databases)
    * Speed up read (cache)
    * Allow to search by keyword or filter (index)
    * Async send message to another process (stream processing)
    * Process a large batch of data at once (batch processing)

* Fundamental categories of data systems - databases, queues, cache, etc.

* Modern tools no longer fit into this traditional category. The lines are blurred. Optimized for various usecase, e.g.
    * Apache Kafka is a queue which can provide database-like durability
    * Redis being a datastore can also be used as a message broker/queue

* Each tool optimize for one specific task. So any problem -> break into multiple smaller tasks -> a tool to solve each task -> application code binds them all together. 
    * e.g. any backend service with an API interface is essentially a data system
    

![API](https://i.imgur.com/3nJpjA5.jpgt)


* Now comes the challenges and three primary concerns
    * Reliability - system should tackle faults
    * Scalability - no perf hit if the system load increases
    * Maintainability - multiple people can work and maintain over time


## Reliability

* Things should work as expected even if things go wrong
    * application should function as expected
    * tolerate unexpected inputs from users
    * no performance hit even if load increases
    * prevent unauthorized access and abuse

* Some terms to know
    * **Faults** - A part of the system deviates from its expected behavior 
    * **Resiliant** - Systems that anticipate and cope with faults
    * **Failure** - when the entire system stops working

* Impossible to prevent faults 100% - design mechanisms that can handle faults so as to prevent failures

* Types of faults
    * Hardware faults
    * Software faults
    * Human errors
    
### Hardware faults

* Problems
    * Hard disk crash
    * RAM failure
    * Power blackout
    * Pulling the network plug


* Solution - Redundancy of each hardware component
    * RAID setup
    * hot-swappable CPU
    * dual power supply
    * power backup battery


*  Extremely rare for all components to fail at once.
* When a hardware component fails, swap that with the backup
* Backup time is not catastrophic in most cases
* If high availability is a priority and cannot afford downtime, then do multi-machine redundancy.

### Software faults

* These occur when a set of assumptions stop being true

* Problems
    * Crash error due to bad input
    * A deadlock or "infinite loop" taking some shared resources
    * An external service being down or giving corrupted responses.
    * Cascade failure


* Solution - No quick easy solution but follow some principles

    * End-to-end testing
    * process isolation
    * self-recovery
    * continuous monitoring
    * alert discrepancy


### Human errors

* Humans are unreliable - major reason for downtime of internet services
* Configuration error - leading cause of outages whereas hardware fault only responsible for 10-25% of the outages

* Solution - No quick easy solution but follow some principles
    * Well-designed interfaces, APIs and abstractions 
    * Decouple production and dev environments
    * End-to-end testing
    * Monitoring telemetry
    * Easy to rollback changes

### Conclusion

* Reliability is important for not just critical applications but 'non-critical' applications as well.
* E.g. what if you lose your phone contacts or personal photos because of a database being corrupted
* In case of just developing a prototype, you might cut corners on reliability to reduce development cost.