# Assignment 3 - Complete Documentation

**Student Name**: [shatha abdulrahman alrafiah]  
**Student ID**: [445052018]  
**Date Submitted**: [7 may]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [May 6, 2026, 2:30]
**What I implemented**: changed the student ID in the scheduler simulation file and started the project. I looked over the starting code and found every shared resource that can lead to race problems in a multithreaded setting.

**Challenges encountered**: 
I first had trouble figuring out which variables were shared by several threads and where synchronization was necessary.
**How I solved it**: 
I examined the scheduler's execution flow and examined how various threads accessed shared counters and logs.
**Testing approach**: 
Before putting synchronization into practice, the software was compiled and run after the student ID was updated to ensure the scheduler operated properly.
**Time spent**: 
Approximately 1 hour and 30 minutes.
---

### Entry 2 - [6 may, 7]
**What I implemented**: 
ReentrantLock was used to safeguard shared counters such as totalWaitingTime, completedProcessCount, and contextSwitchCount.
**Challenges encountered**: 
In order to prevent deadlocks, I had to make sure that locks were always released properly.
**How I solved it**: 
To ensure correct unlocking even in the event of exceptions, I used try-finally blocks around every crucial portion.
**Testing approach**: 
To make sure that values remained precise and constant during executions, the scheduler was run several times while the shared counters were observed.
**Time spent**: 
30 min
---

### Entry 3 - [6 may, 8]
**What I implemented**: 
Concurrent logging activities were tested and synchronization protection was included for the execution log ArrayList.
**Challenges encountered**: 
Because ArrayList is not thread-safe and may create concurrent modification issues, the execution log was susceptible.
**How I solved it**: 
To avoid concurrent access problems, I used ReentrantLock to synchronize all changes to the execution log.
**Testing approach**: 
continually ran the scheduler while keeping an eye on the execution log to make sure all log entries were accurately captured and free of errors or exceptions.
**Time spent**: 
40 min
---

### Entry 4 - [6 may, 8]
**What I implemented**: 
Semaphore synchronization was used to regulate CPU access, and the scheduler simulation's final debugging and verification were finished.
**Challenges encountered**: 
At first, I had problems with semaphore placement and improper release handling, which had an impact on thread execution.
**How I solved it**: 
To ensure safe CPU resource management, I put semaphore acquire and release activities inside try-finally blocks.
**Testing approach**: 
To confirm steady execution, proper synchronization behavior, accurate statistics, and the lack of deadlocks or race conditions, more than five full test runs were carried out.
**Time spent**: 
20 min
---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:
Prior to synchronization, race situations and inconsistent outcomes might arise from several threads accessing and modifying shared resources at the same time. For instance, several threads may simultaneously update the shared counter contextSwitchCount inside:

contextSwitchCount++;

The shared executionLog ArrayList served as another illustration.

executionLog.add (msg);

Concurrent access may result in corrupted log entries or ConcurrentModificationExceptions because ArrayList is not thread-safe. I used ReentrantLock to secure these crucial areas in order to address this issue.
[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[Your answer here - explain your implementation choices]
Although they are both synchronization systems, ReentrantLock and Semaphore have different functions. ReentrantLock is mostly used for mutual exclusion, which limits access to a critical area to one thread at a time. I used ReentrantLock in my code to safeguard shared resources like:

executionLog.add(message); contextSwitchCount++; completedProcessCount++; totalWaitingTime += time;
I used the following to build the lock:

lock = new ReentrantLock(); is the public static final ReentrantLock lock.

and employed it within try-finally blocks using lock.lock() and lock.unlock().

Semaphore, on the other hand, is used to restrict access to a finite resource. I employed a binary semaphore with a single permit in my implementation:

cpuSemaphore = new Semaphore(1); is a public static final Semaphore;

As a result, the simulated CPU could only be used by one process at a time. The process obtains the semaphore prior to execution:SharedResources.cpuSemaphore.acquire();
after execution it releases it:

SharedResources.cpuSemaphore.release();

I used ReentrantLock for protecting shared data and Semaphore for controlling CPU access and process execution order.
---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Your answer here - reference try-finally blocks, lock ordering, etc.]
A deadlock occurs when two or more threads wait indefinitely for one another, which stops the program from moving forward. This typically occurs when resources are locked but improperly released.

Try-finally blocks are one preventive strategy that ensures locks and semaphores are always released, even in the event of an exception. I used the following in my code:

lock.lock(); try { contextSwitchCount++; } finally { lock.unlock(); }
I applied the same strategy to the semaphore as well:

SharedResources.cpuSemaphore.acquire(); try { // process execution } finally { SharedResources.cpuSemaphore.release(); }

Avoiding needless nested locks is another preventative strategy. To lessen the possibility of deadlock, I used a single shared lock object in my code and kept important parts brief. Stable execution and safe synchronization were made possible by these methods.
---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]
For all three shared counters in my solution, I used ONE shared ReentrantLock, which is regarded as coarse-grained locking. I chose this option since it decreased implementation complexity and streamlined synchronization logic. Using a single lock made the code easier to manage and troubleshoot because the assignment concentrates on comprehending synchronization ideas.
Coarse-grained locking has the benefit of simplicity and a reduced chance of synchronization errors. Even though the counts are independent, only one thread can update any shared counter at a time, which has the drawback of less concurrency.
Because multiple threads can change various counts at the same time, fine-grained locking increases concurrency by using distinct locks for each shared variable. Fine-grained locking would increase concurrency and possibly boost performance because contextSwitchCount, completedProcessCount, and totalWaitingTime are independent variables.

However, because coarse-grained locking is safer, simpler to maintain, and adequate for the scheduler simulation needs, I chose it for this task.
---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, and totalWaitingTime
**Why they need protection**: 
Several process threads share these variables. Inaccurate final statistics and race circumstances could result from concurrent revisions.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
// Paste your implementation here
```
public static final ReentrantLock lock = new ReentrantLock();

public static void incrementContextSwitch() {
    lock.lock();
    try {
        contextSwitchCount++;
    } finally {
        lock.unlock();
    }
}
**Justification**: 
In order to prevent inconsistent values, the lock guarantees mutual exclusion, allowing only one thread to update shared counts at a time.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog ArrayList
**Why it needs protection**: 
It is not thread-safe to use an ArrayList. The log could be corrupted or exceptions could be thrown if many threads write to the list at the same time.

**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
// Paste your implementation here
```
public static void logExecution(String message) {
    lock.lock();
    try {
        executionLog.add(message);
    } finally {
        lock.unlock();
    }
}

**Justification**: 
The lock prevents concurrent modifications and guarantees safe access to the execution log.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
to restrict CPU access and limit the number of processes that can run simultaneously.
**Number of permits and why**: 
Because the scheduler mimics a single CPU, there is only one permit.
**Where implemented**: 
Inside the run() and runToCompletion() methods.
**Code snippet**:
```java
// Paste your implementation here
```
SharedResources.cpuSemaphore.acquire();

try {
    // process execution
} finally {
    SharedResources.cpuSemaphore.release();
}

**Effect on program behavior**: 
The semaphore guarantees consistent execution behavior and stops many processes from using the CPU at once.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
javac SchedulerSimulationSync.java
java SchedulerSimulationSync

# Repeated 5 times
```

**Results**: 
(Show that running multiple times produces consistent, correct results)
There were no crashes, deadlocks, or corrupted data during any of the executions.
Every procedure completed successfully, and synchronization statistics were consistently produced.

The outcomes consistently demonstrated:

15 processes in all were completed.
No processes are absent.
Appropriate queue behavior
Consistent context-switching behavior
No shared data values that are inconsistent
**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)
Race conditions could result from several threads accessing shared resources at the same time in the absence of synchronization.

Shared resources that need to be protected include:

Context Switch Counter in Ready Queue
Variables of Waiting Time
Logs of Execution

Processes may be lost or duplicated if two threads alter the ready queue simultaneously.
Likewise, updating counters such as:
contextSwitchCount++;
Due to the possibility of multiple threads updating the variable at once, a lack of synchronization could result in inaccurate values.

Only one thread can access crucial areas at a time thanks to mutex locks and semaphores, preventing inconsistent outcomes.
**Conclusion**: 
Throughout several runs, synchronization successfully safeguarded shared resources and guaranteed steady, predictable performance.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException occurs during queue operations.

**Testing procedure**: 
Several threads kept adding and removing processes from the ready queue while the scheduler was running.
**Results**: 
During execution, there was no ConcurrentModificationException.
Every procedure was properly scheduled, and the program finished smoothly.
**What this proves**: 
This demonstrates that synchronization techniques appropriately safeguarded shared collections and avoided risky concurrent changes.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
All 15 processes should complete execution
Remaining burst times should become 0
Context switches should increase whenever a process yields the CPU
Waiting times should be calculated correctly
**Actual values**: 
From the execution results:

Total Completed Processes = 15
Total Context Switches = 30
Average Waiting Time = 59270ms
All processes finished with Remaining Time = 0ms
**Analysis**: 
The expected scheduler behavior was consistent with the actual values.

Properly preempted and put back in the ready queue were processes whose burst times exceeded the time quantum.
Smaller burst times allowed for instantaneous completion of processes.
This attests to the proper implementation of the Round Robin scheduling and synchronization mechanism.
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]
Using a 4000 ms time quantum, test processes with various burst times and priorities.
**Purpose**: 
To verify that the scheduler handles:

Short processes
Long processes
Multiple context switches
Different priorities
Queue reordering
**Results**: 
Short processes like P1 and P5 completed in one quantum
Longer processes such as P2, P7, and P10 required multiple quantums
Processes were successfully re-added to the ready queue after yielding the CPU
No deadlocks or synchronization issues occurred
**What I learned**: 
I discovered that synchronization, particularly when several threads use the same resources, aids in maintaining consistency and fairness during concurrent scheduling processes.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]
his assignment taught me how synchronization keeps threads from interfering with one another while they are using common resources.
I recognized the significance of employing semaphores and mutex locks to safeguard important parts.
I also discovered that even though the program occasionally seems to function well, race conditions can produce inaccurate results.
Only one thread could access shared data at a time thanks to the use of synchronized blocks.
Semaphores were helpful in preventing conflicts between programs and managing CPU access.
I also discovered how Round Robin scheduling is used by concurrent scheduling systems to fairly manage several processes.
Because thread behavior varies between executions, debugging synchronization issues proved difficult.
I now have a better understanding of operating system scheduling and multithreading
---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Synchronization is a technique used by banking systems to stop several users from changing the same account balance at the same time.
**Example 2**: 
Synchronization is used by operating systems to reliably handle memory access, CPU scheduling, and task coordination.
---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]
Organizing pupils to enter a classroom through a single door is analogous to synchronization.
Chaos happens if everyone tries to enter at the same moment.
Only one learner can enter at a time thanks to synchronization, which functions as a teacher managing the entrance.

Threads in programming share resources such as counters and queues.
Without synchronization, threads could give inaccurate results by overwriting each other's work.
Semaphores and locks aid in coordinating access and maintaining order and accuracy.
---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
