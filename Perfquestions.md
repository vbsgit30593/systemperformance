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

## Request from A to C takes more time than B to C

| Phase | Comment |
| ----- | ------- |
| Question | Why does request from A to C take longer than B to C |
| Hypothesis | A and B are in different data centers |
| Prediction | Moving both to same might solve the issue |
| Test | Move host A and check perf |
| Analysis | Perf fixed - consistent with hypothesis. |

`If the hypothesis wasn't correct then I would first move the device back to
its place as I don't want multiple variables in my perf analysis.`

## how to analyse resource saturation?
* We can probably look at `run-queue-length`
* Look at memory `swapping`, `oom-killer`
* Linux `overruns`

## Why are failed malloc/callocs a rarity on Linux?
* This is because Linux overcommits and keeps adjusting.
