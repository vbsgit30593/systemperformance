# Keywords
* IOPS
  * inp/out per sec
* throughput
  * rate of work performed - data rate or operation rate
* resp time	
* latency
  * time spent waiting for a req to be serviced.
  * ex - player presses the key and then the time spent in converting that into an action.
* utilization
  * In terms of disk it can be considered the amt of consumption.
  * for some other resources it can be considered as the amount of busy time
* bottleneck
  * when resource reaches its limiting point
* workload	
  * inp to a system or the load applied
* pertubations	
  * external disturbances added to a SUT

## Latency
```
Network request                   total response time                   Completion 
   │ ─────────────────────────────────────────────────────┬──────────────────── │ 
   │                                                      │                     │ 
   │                                                      │                     │ 
   │                                                      │                     │ 
   │              Latency                                 │                     │ 
   │ ──────────────────────────────────────────────────── │                     │ 
   │                                                      │                     │ 
   │                                                                            │ 
   │                                                                            │ 
   │                                                        ────────────────────┤ 
   │                                                         data xfr time      │ 
                                                                                │ 
                                                                                │ 
```

### Contextual latency
* load time of a website
    * DNS latency
    * TCP latency
    * data transfer time

#### Why should we convert other metrics to latency?
* Case: Comparing 100 disk iops vs 50 network requests
    * How would you compare them? its very contextual
    * Many factors to consider
        - network hops
        - drops and retxs
        - disk size
        - I/O size
        - disk access patterns

    * Its much simpler to compare 100ms of network I/O vs 50ms of disk I/O

## Trade-offs
* A common tradeoff is between memory and CPU
    * As memory increases, more stuff can be cached and hence CPU usage can be reduced
    * With tons of CPU we can spend CPU time to reduce memusage.
* Always look for tradeoffs while deciding something
    * like small/large network buffers
    * small/large disk block size

## Tunable parameters
* A param set that might be ideal now would not be the same a week from now.
* Use version control for the tunable params.
    * Use user config management tools like `Puppet`, `Salt`, `Chef`

## Load vs Architecture
### Load-based issue
* Excessive queueing but task being performed as expected
    * Similar to socket cache situation.
    * usually points at excessive load and warrants the need for horizontal scaling.

### Arch-based
* Single threaded application busy on CPU with queued resources
    * Idle and available CPUs
* multi-threaded application waiting on a single lock contention.

## Profiling
* Usually done by sampling the state of a system at periodic intervals
    * Follow it up by analysis of samples
### Ex - CPU Profiling
* Sample the CPU Inst Ptr or stack traces to understand accessed codepaths that are consuming a lot of resources.

## Caches
### Algorithms
LRU MRU LFU MFU NFU

### Hot, Cold and Warm Caches
* Hot caches
    * Cache with frequently accessed items.
* Warm cache
    * Has useful data but not high enough hit ratio
* Cold cache
    * Either empty cache or filled with unnecessary items
* Warmth of a cache
    * Describes how hot, cold or warm the cache is.

## Known-Unknowns
### Known-Knowns
* Metrics for which we know the expected numbers
    * Like CPU utilization
        * We know that we should check this metric and expect a certain value.

### Known-Unknowns
* Things we know that we do not know
    * Basically metrics that we know that we need to check but we haven't observed yet.
    * Like we know that perf might help us but we haven't tried it yet.
### Unknown-Unknowns
    * Things we dont know that we dont know.
    * Example - SKTCACHE vs SRCSTATE caching algorithm difference.

## Perspectives

Top down or bottom up analysis of operating system software stack

### Resource Analysis
| Metrics |
| ---- |
| utilization | 
| saturation | 
| throughput | 
| iops | 
| latency |

|Tools|
| ---- |
| vmstat |
| iostat |
| mpstat |


### Workload Analysis
#### Target
* Request
* Latency
* Completion

`The fastest query is the one that you don't do at all.`

# Methodologies
## Streetlight-Anti method
* An absence of a deliberate methodology.
* User analyses perf based on tools found on internet and hopes to get some useful results.
    * hit and miss
    * Similar to trial and error with tunable parameters.

## Random-Change Anti method
* user guesses where the problem might be and then randomly attempts changing
it until that problem goes away
* picks a metric and keep monitoring it in both directions.

### Problems
* Time consuming
* solution tailored to the current workload.
* could cause worse issues at the time of production
    * like the use of an atomic varible
    * or a spinlock.

## Blame-someone-else Anti-pattern
* Finding a problematic component and blaming that to be the issue
* Example - "May be its the network."

## Problem statement
* Ask the following questions when you are given a perf issue

| Questions |
| ---- |
| What makes you think it's a performance issue? |
| Has this system ever performed well? |
| What changed recently? Software ? Hardware? Load? |
| Can you express the problem in latency or runtime? |
| Does this problem affect only you, a certain group or everyone? |
| What is the env? Version? Arch? Config? Software ? hardware |

`This should the first thing to do when assigned a perf issue`

## Scientific Method
This consists of the following phases - 

| Phases |
| ---- |
| Question |
| Hypothesis |
| Prediction |
| Test |
| Analyse |

* The hypothesis might turn out to be incorrect during analysis
    * We still have the benefit of eliminating huge components and iterate again.

## The USE method
* For every resource check utilization, saturation and errors.
* The benefit of this method over a blind tools check method is that we will
have a list of questions first that we need to target
    * Now we can try to find relevant tools to approach those questions.
    * Even if we don't have the right tools, we have atleast switched to a
    Known-Unknowns stage.

`dont include hardware cache in USE method analysis as caches improve perf with
increased utilization while USE method analyses perf degradation on increased utilization.`

### Use method on Software resources
* Mutex locks

| Metric | Behavior |
| ----- | ---- |
| Utilization | the time the lock was held |
| Saturation | the threads queued waiting for locks |

## The RED method

| Metric | Info |
| --- | --- |
| RequestRate | RPS |
| Error | requests failed |
| Duration | time for req completion |

### Why USE for Machine Health and RED for User Health?
* Machines (hardware, infrastructure) suffer from resource exhaustion, so monitoring utilization, saturation, and errors is key.
* Users care about request rates, failures, and response times, making RED more relevant.

* Request rate provides an early clue about the performance.
    * if the rate is same but duration has increased, then it points at a problem with the `architecture`.
    * If both load and duration increased, then it points at a `load` issue.

## Workload characterization
* helps in identifying issues due to load applied.
    * focusses only on the input load rather than eventual performance.

### Questions
1. Who is causing the load? UID/PID/Remote IP?
2. Load characteristics - IOPS, Throughput, direction (R/W)
3. How does load change over time?

## Drill Down Analysis
* Starts at a high level and gradually drills down
* Eliminate uninteresting findings as we drill down

### Stages
* Monitoring
    * Keep monitoring and generate alert in case of issues.
* Identification
    * Scope of the affected components gets narrowed down based on the alerts.
* Analysis
    * Further analysis for RCA

### Netflix example
| stage | comments |
| --- | --- |
| monitoring | Atlas identifies the microservice seeing the problem |
| identification | perfdash narrows the problem to a resource | 
| analysis | flamecommander shows codepaths causing the problems |

## Latency Analysis
* Analyzing the time taken to complete an operation in a layered manner
* Start with the main operation and keep dividing it into sub operations
    * Workload -> Application -> System Libraries -> Syscalls -> the kernel -> device drivers

### MySQL latency analysis
* Is this a query latency issue? Yes
* Is the latency spent mostly on-cpu or off-cpu? off cpu
* What is the off cpu time spent waiting for? file system i/o
* Is the file I/O because of disk I/O or lock contention? disk I/O
* Is the disk I/O spent on queuing or servicing the I/O ? servicing
* Is the disk service time mostly I/O init or data transfer? (data transfer) 

## Baseline Statistics
### Approach 1: Extra lines in visualization
* Netflix dashboards have an extra line to show the same time range for the previous week
* 3PM today can then be directly compared with 3PM of the previous week.

### Approach 2: Capture for future reference
* Capture a lot of information using shell scripts and tools.
    * A lot more than usual monitoring.
* Capture these periodically but also whenever an application state changes so that differences can be captured.

## Static Performance Tuning
* Issues to deal with in absence of workload.
### Examples
* Network interface - 1Gbps configured instead of 10Gbps
* A costly debug session left open
* Remote auth instead of local
* Disk almost full
* Mismatch FS record size compared to load

## Statistics
### Geometric mean
* This is useful when we are fixing something in layers as we tend to have a multiplicative impact there
* This happens since all layers contribute to a packets performance

### harmonic mean
* this accounts  for cases such as rates
    * Example - for an 800MB data transfer, the first 100MB might be sent at 50MB/s while the rest might be sent at throttled 10Mpbs
    * Harmonic mean assigns more weight to the lower rate accordingly.
        * overall - 800/(100/50 + 700/10) = 11.1Mbps

