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

 
