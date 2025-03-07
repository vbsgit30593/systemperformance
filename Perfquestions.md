## How would you diagnose slow queries?
* Application level info wouldn't provide a good picture
* Try to look at where the CPU time is getting spent
* Look for disk I/O performance

### Case: A slow web app experiencing slow resp times
* from htop we notice that CPU usage is very high but just for a single core.
* Using perf we can get to know that most of the time is getting wasted due to mutex locks
* Using iostat or vmstat we can know that disk I/O is very high


This can point to relevant appln level improvements that we can make.

## When to stop analysis?
* When we have explained the bulk of the problem.
* When the potential ROI is less than the cost of analysis
* When there's higher ROI somewhere else

## Scenario where the way the work is performed seems correct but there are tons of tasks in the queue.
* This might simply point at too much load compared to capacity.

## Degrading throughput
* Can happen when component comes close to the saturation point.
* Leads to excessive queueing
* For computation heavy systems, we may choose to increase the number of threads frequently
    * this can lead to contention and consume CPU time in context switching and scheduling
    * Response time will take a hit as CPU scheduler time increases.    
