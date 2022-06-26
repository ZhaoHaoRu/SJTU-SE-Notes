# Scheduling

## Preliminary knowledge

- **workload:** the processes running in the system
- 

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled.png)

- **turnaround time:**  the time at which the job completes minus the time at which the job arrived in the system
    - turnaround time is a **performance** metric

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%201.png)

- **response time:**  the time from when the job arrives in a system to the first time it is scheduled.
    - interactive performance from the system

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%202.png)

## First In, First Out (FIFO)

an example: Imagine three jobs arrive in the system, A, B, and C, at roughly the same time

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%203.png)

**problem**: a number of relatively-short potential consumers of a resource get queued behind a heavyweight resource consumer.

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%204.png)

## Shortest Job First (SJF)

### **it runs the shortest job first, then the next shortest, and so on.**

### *optimizes turnaround time, but is bad for response time.*

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%205.png)

## Shortest Time-to-Completion First (STCF)

**Any time a new job enters the system, the STCF scheduler determines which of the remaining jobs (including the new job) has the least time left, and schedules that one.**

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%206.png)

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%207.png)

## **Round-Robin (RR)**

- instead of running jobs to completion, RR runs a job for a **time slice** (sometimes called a **scheduling quantum**) and then switches to the next job in the run queue.
- It repeatedly does so until the jobs are finished.
- deciding on the length of the time slice presents a trade-off to a system designer, making it long enough to **amortize(摊销）** the cost of switching without making it so long that the system is no longer responsive.
- RR is indeed one of the *worst* policies if turnaround time
- optimizes response time but is bad for turnaround

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%208.png)

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%209.png)

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%2010.png)

## Incorporating I/O

- By treating each CPU burst as a job, the scheduler makes sure processes that are “interactive” get run frequently. While those interactive jobs are performing I/O, other CPU-intensive jobs run, thus better utilizing the processor.

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%2011.png)

![Untitled](Scheduling%20d1a40f56fb3d4f03ad3c8b71027776c7/Untitled%2012.png)