# Chapter 1 - Reliable, Scalable, and Maintainable Applications

## Martin Kleppman

Questions and Answers

# Reliability

**1. What are the three main categories of faults that can occur in a system? Explain each one briefly.** (p6) #fault-categories

_Hardware faults, software errors, and human mistakes. Hardware faults refer to physical component failures. Software errors are bugs in the system code. Human mistakes are configuration errors or faulty input by operators._

**2. How does redundancy help improve system reliability? Illustrate with an example.** (p7) #redundancy

_Redundancy involves replicating critical system components so that the system can continue operating even if some redundant components fail. For example, replicating a database across multiple servers allows the system to continue serving data even if one server fails._

**3. What is fault isolation and how does it improve reliability?** (p8) #fault-isolation

_Isolating faults prevents localized errors from cascading to other components. For example, running processes in separate containers limits the impact of a process crash._

**4. What is a key advantage of distributed systems in terms of reliability?** (p7) #distributed-systems-reliability

_Distribution across many nodes can tolerate the failure of individual nodes. For example, in a distributed database, copies of data on multiple nodes allow some nodes to fail without data loss._

**5. How can unit testing and test-driven development improve software reliability?** (p8) #testing-for-reliability

_Unit tests and test-driven development help uncover software bugs and errors early during development. This prevents bugs from ending up in production systems._

**6. What is graceful degradation and how does it relate to reliability?** (p6) #graceful-degradation

_Graceful degradation is the ability of a system to continue providing a reduced level of service during partial failures, as opposed to completely failing. This improves overall system reliability._

**7. What is fault tolerance and how is it different from fault prevention?** (p6) #fault-tolerance

_Fault tolerance is the ability to continue operating properly despite faults. Fault prevention aims to prevent faults entirely. Since not all faults can be prevented, fault tolerance is also needed._

**8. What is the benefit of occasionally inducing failures deliberately in systems?** (p8) #induced-failures

_Deliberately inducing failures forces systems to exercise fault tolerance mechanisms. This uncovers hidden bugs and improves overall system reliability._

**9. How can you implement loose coupling between system components? Why is this useful?** (p8) #loose-coupling

_Loose coupling means reducing dependencies and interactions between components. This confinement limits the spread of local failures to other parts of the system._

**10. What is the role of monitoring and telemetry in system reliability?** (p9) #monitoring

_Monitoring system metrics and telemetry allows operators to notice anomalies and failures early. This allows preventative action before minor issues cascade into total failures._

**11. What are some examples of real-world system failures or outages caused by human operational errors?** (p9) #human-errors

_Deploying badly tested configuration changes, incorrect capacity planning, granting excessive access privileges, cascading failures from small mistakes, accidentally deleting production data, etc._

**12. Why is it important to have good documentation, processes and training around operating a system?** (p9) #operating-guidelines

_Clear documentation, defined processes, and training for operators reduce the chances of human mistakes that could cause system outages._

**13. What are some examples of redundancy mechanisms that improve hardware fault tolerance?** (p7) #hardware-redundancy

_RAID disk mirroring, redundant power supplies, hot-swappable components, geographic redundancy across datacenters, etc._

**14. How does having good abstractions and APIs improve system reliability?** (p8) #abstractions

_Good abstractions hide complex implementation details and provide predictability. This reduces the chance of unanticipated failures due to complexity._

**15. What are some examples of real-world catastrophic system failures caused by software bugs?** (p8) #software-bug-examples

_Therac-25 radiation machine bugs, Ariane 5 rocket explosion due to overflow, Mars Climate Orbiter loss due to unit conversion errors, etc._

# Scalability

**1. What are load parameters? Give some examples.** (p10) #load-parameters

_Load parameters measure aspects like requests/sec, read/write ratio, concurrent users, hit rate, etc. They characterize the "load" on a system._

**2. Why is it important to have good load parameter metrics when discussing scalability?** (p10) #load-parameters-importance

_Load parameters quantify aspects of system load. Having good metrics makes load measurable and allows systematic analysis of system scalability._

**3. What are some typical load parameters for a database system?** (p10) #db-load-parameters

_Queries/sec, ratio of reads to writes, volume of data written per second, number of simultaneous client connections, cache hit rate, etc._

**4. What are some examples of response time metrics and what do they measure?** (p13) #response-time-metrics

_Mean, median, and high percentile response times. Mean is the average. Median is the midpoint. High percentiles measure tail response times._

**5. Why measure the 95th or 99th percentile response times instead of just the average?** (p14) #high-percentile-metrics

_Average can hide poor worst-case performance. High percentiles measure response times for the slowest requests which directly impact users._

**6. What is head-of-line blocking and how does it impact latency?** (p15) #head-of-line-blocking

_Head-of-line blocking is when a slow resource makes subsequent waiting requests also slow, even if they are fast to process. It increases latency._

**7. How does buffering and batching help improve scalability?** (p12) #buffering-batching

_Buffering and batching combine multiple requests and process them together as a group. This amortizes overhead across multiple requests._

**8. What are the trade-offs between scaling up vs scaling out?** (p17) #scaling-tradeoffs

_Scaling up uses fewer, more powerful machines. Scaling out uses more, smaller commodity machines. Out often provides better availability._

**9. What kinds of applications are more suited to scaling up vs scaling out?** (p17) #scaling-strategies

_Applications with small data volumes but needing fast CPUs often scale up. Large datasets or I/O bound workloads often scale out._

**10. What does it mean for a system design to be elastic?** (p17) #elastic-systems

_Elastic systems can automatically scale computing resources up and down based on load, utilizing cloud computing platforms._

**11. What are some mechanisms used by databases to scale writes?** (p207) #scaling-writes

_Sharding/partitioning, replication, distributing requests across more nodes, batching/buffering, etc._

**12. Why can scaling reads be harder than scaling writes?** (p205) #scaling-reads

_Writes can be processed asynchronously and in isolation. Reads often need synchronous coordination between replicas._

**13. What are some examples of partitioning strategies for scalability?** (p204) #partitioning-strategies

_Key range, hash mod N, lookup tables, composite keys, etc. Goal is to spread load and avoid hot spots._

**14. What kinds of applications are most challenging for scaling databases?** (p197) #scaling-challenges

_High write throughput, large datasets, complex transactions across records, consistency needs, variable access patterns._

**15. How does caching help improve scalability and performance?** (p286) #caching

_Caching reduces load on databases by avoiding expensive queries for repeatedly needed data._

# Maintainability

**1. What does operability mean for a software system?** (p19) #operability

_How easily the operations team can keep the system running smoothly, avoiding and recovering from failures._

**2. Why is operability important for system maintainability?** (p19) #operability-importance

_Good operability lets the ops team focus on high-value activities rather than manual maintenance and firefighting issues._

**3. What are some examples of good operability practices for systems?** (p19) #operability-practices

_Monitoring, automation, avoiding dependency on individual machines, documentation, predictable behaviors, self-healing capabilities, manual control options._

**4. How can simplicity and abstractions improve maintainability?** (p21) #simplicity-abstractions

_They reduce complexity, making the system behavior more predictable and understandable for maintainers._

**5. What are some examples of good abstractions that improve maintainability?** (p21) #good-abstractions

_High-level programming languages, operating system APIs, file systems, databases, configuration management tools._

**6. What does evolvability mean for a system?** (p21) #evolvability

_How easily the system can be adapted for changing requirements, new use cases, and new platforms/environments._

**7. Why is evolvability important for long-lived systems?** (p21) #evolvability-importance

_Requirements inevitably change over time. Evolvable systems can be easily modified and adapted with minimal cost/disruption._

**8. How are simplicity and evolvability related?** (p21) #simplicity-evolvability

_Simpler systems are easier to modify and less likely to have hidden dependencies that break during an evolution._

**9. What are some Agile and DevOps patterns that improve maintainability?** (p21) #agile-devops

_Test automation, continuous delivery, decoupled architecture, configuration management, monitoring, loose coupling._

**10. How does extensive automated testing improve maintainability?** (p21) #automated-testing

_Automated testing allows large-scale changes while catching any regressions quickly, improving evolvability._

**11. What is technical debt and how does it impact maintainability?** (p583) #technical-debt

_Shortcuts and careless code that eventually catch up as debt that has to be repaid through extra maintenance effort._

**12. How can modularity and loose coupling reduce technical debt?** (p583) #modularity

_Well-defined module boundaries and loose coupling prevent messes that entangle components together._

**13. Why is good documentation important for system maintainability?** (p19) #documentation

_It preserves operational knowledge and architectural understanding as team members change over time._

**14. What types of documentation are most valuable for system maintainers?** (p19) #documentation-types

_High-level overviews, API docs, design docs, ops runbooks, onboarding docs, etc._

**15. How can you design applications to be cloud-native and maintainable?** (p532) #cloud-native

_Loose coupling, statelessness, automation, immutable infrastructure, managed services, etc._

#### Some page numbers will be wrong
