# Assignment 3 - Complete Documentation

**Student Name**: [reem khaled aljawf]  
**Student ID**: [445052182]  
**Date Submitted**: [7 may]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [https://drive.google.com/file/d/1jXahpKcJAPFpEPWQX66aLlHNWQ4eDzNY/view?usp=drive_link]

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

### Entry 1 - [may 6, 2026 - 8:30 PM]
**What I implemented**: 
I started the assignment by setting up the repository and updating my student ID in the SchedulerSimulationSync.java file. I also reviewed the assignment requirements and analyzed the shared resources that could cause race conditions.
**Challenges encountered**: 
At first, I was not fully sure which variables required synchronization and why they were considered critical sections.

**How I solved it**: 
I carefully reviewed the code and identified shared variables such as contextSwitchCount, completedProcessCount, totalWaitingTime, and executionLog.

**Testing approach**: 
I compiled and ran the program before making synchronization changes to observe the original behavior.

**Time spent**: 
1 and a half hour 
---

### Entry 2 - [may 7, 10pm]
**What I implemented**: 
I added ReentrantLock synchronization for the shared counter variables to prevent race conditions during concurrent thread execution.

**Challenges encountered**: 
I initially forgot to use finally blocks when unlocking the locks, which could potentially lead to deadlocks if exceptions occurred.

**How I solved it**: 
I updated all critical sections to use try-finally blocks to guarantee that locks are always released safely.

**Testing approach**: 
I repeatedly ran the program and checked whether the counter values remained consistent.

**Time spent**: 
1 hours
---

### Entry 3 - [may 7, 11pm]
**What I implemented**: 
I protected the executionLog ArrayList using a separate ReentrantLock to avoid concurrent modification issues.

**Challenges encountered**: 
I noticed that ArrayList is not thread-safe and concurrent writes could corrupt the log entries.

**How I solved it**: 
I implemented a dedicated logLock to synchronize access to the execution log independently from the counters.

**Testing approach**: 
I executed the program multiple times while monitoring the execution log consistency.

**Time spent**: 
1 hour and 45 minutes
---

### Entry 4 - [7 may, 1am]
**What I implemented**: 
I implemented a binary semaphore using Semaphore(1) to simulate controlled CPU access.

**Challenges encountered**: 
I needed to ensure that the semaphore would always be released even if exceptions occurred during execution.


**How I solved it**: 
I used a try-finally block around the semaphore acquire and release operations.

**Testing approach**: 
I tested the scheduler execution flow and verified that only one process could access the CPU at a time.

**Time spent**: 
2 hour
---

### Entry 5 - [may 7, 3am]
**What I implemented**: 
I completed the final testing phase, reviewed all synchronization mechanisms, and prepared the documentation and video demonstration.

**Challenges encountered**: 
The terminal initially displayed unreadable symbols instead of ANSI colored output.

**How I solved it**: 
I changed the terminal encoding using chcp 65001 to support UTF-8 characters properly.

**Testing approach**: 
I executed the program more than five times and verified consistent results across all runs.

**Time spent**: 
1 hour 
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

Race conditions existed in the shared counter variables and the execution log. The first race condition affected contextSwitchCount, completedProcessCount, and totalWaitingTime because multiple threads could update these shared variables simultaneously. Concurrent access is dangerous because two threads may read and modify the same value at the same time, causing lost updates and inconsistent results.

The second race condition affected the executionLog ArrayList. Since ArrayList is not thread-safe, concurrent write operations could corrupt the internal structure or produce ConcurrentModificationException errors. Incorrect behavior could include missing log entries, inconsistent statistics, or application crashes during concurrent execution.

[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

ReentrantLock is a mutual exclusion mechanism that allows only one thread to access a critical section at a time. Semaphore is a synchronization mechanism that controls how many threads can access a shared resource simultaneously.

In my implementation, I used ReentrantLock to protect shared counters and the execution log because these resources required exclusive access during updates. I used Semaphore(1) as a binary semaphore to simulate CPU access control, ensuring that only one process could execute on the CPU at a time.

ReentrantLock was more suitable for protecting critical sections, while Semaphore was better for controlling resource availability and concurrent execution limits.
[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

Deadlock is a situation where two or more threads wait indefinitely for resources held by each other, causing the program to stop making progress.

One prevention technique is using try-finally blocks to guarantee that locks and semaphores are always released even if exceptions occur. Another prevention technique is minimizing the time spent inside critical sections to reduce lock contention and waiting time.

In my code, I prevented deadlocks by always releasing ReentrantLocks and semaphores inside finally blocks. I also used a simple synchronization structure without nested locks, which reduced the possibility of circular waiting conditions.

[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

I used one shared ReentrantLock for all three counter variables, which is considered a coarse-grained locking approach. I made this decision because the counters are updated very quickly and using a single lock simplified the synchronization logic.

The advantage of coarse-grained locking is simplicity and easier maintenance. However, it can reduce concurrency because only one thread can update any counter at a time. Fine-grained locking uses separate locks for each counter, which improves concurrency because independent counters can be updated simultaneously.

Since the three counters are logically independent, fine-grained locking could provide better concurrency performance in highly parallel systems. However, for this assignment, the performance difference is minimal, and the single-lock approach improved readability and reduced implementation complexity.

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

Which variables:
contextSwitchCount, completedProcessCount, and totalWaitingTime

Why they need protection:
These variables are shared among multiple threads and can be modified concurrently, which may lead to race conditions and inconsistent values.

Synchronization mechanism used:
ReentrantLock

Code snippet:
counterLock.lock();

try {
    contextSwitchCount++;
} finally {
    counterLock.unlock();
}

Justification:
ReentrantLock guarantees mutual exclusion and prevents simultaneous updates to shared counters.
---

### Critical Section #2: Execution Log

What resource:
executionLog ArrayList

Why it needs protection:
ArrayList is not thread-safe, and concurrent modifications can corrupt the log or throw exceptions.

Synchronization mechanism used:
ReentrantLock

Code snippet:
logLock.lock();

try {
    executionLog.add(message);
} finally {
    logLock.unlock();
}
Justification:
The lock ensures that only one thread can modify the execution log at a time.

---

### Critical Section #3: CPU Semaphore

Purpose of semaphore:
To control concurrent access to the simulated CPU.

Number of permits and why:
One permit was used because the simulation represents a single CPU executing one process at a time.

Where implemented:
Inside the run() method before process execution.

Code snippet:
SharedResources.cpuSemaphore.acquire();

try {

    // process execution

} finally {
    SharedResources.cpuSemaphore.release();
}
Effect on program behavior:
The semaphore guarantees controlled execution order and prevents multiple processes from using the CPU simultaneously.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check

What I tested:
Running the program multiple times to verify consistent results.

Testing procedure:
I compiled and executed the program five times using the following commands:

javac SchedulerSimulationSync.java
java SchedulerSimulationSync

Results:
The program produced consistent execution statistics and completed successfully in all runs.

Why synchronization is necessary:
Without synchronization, shared variables could be updated incorrectly by concurrent threads, causing race conditions, inconsistent counters, and corrupted execution logs.

Conclusion:
Synchronization mechanisms successfully protected shared resources and improved execution consistency.

---

### Test 2: Exception Testing
What I tested:
Checking for ConcurrentModificationException.

Testing procedure:
I repeatedly executed the scheduler while multiple threads accessed the execution log concurrently.

Results:
No ConcurrentModificationException or corrupted log entries occurred after implementing synchronization.

What this proves:
The ReentrantLock successfully protected the ArrayList from unsafe concurrent access.

---

### Test 3: Correctness Verification
What I tested:
Verifying correct final statistics and execution counters.

Expected values:
All processes should complete successfully, and all counters should produce valid positive values.

Actual values:
The program completed all processes successfully and generated correct synchronization statistics.

Analysis:
The synchronization mechanisms prevented inconsistent updates and maintained accurate execution results.

---

### Test 4: Different Scenarios
Scenario tested:
Running the scheduler with different randomly generated process burst times and priorities.

Purpose:
To verify scheduler stability under different execution conditions.

Results:
The scheduler handled all generated workloads correctly without synchronization failures.

What I learned:
Synchronization mechanisms remain effective even when execution conditions change dynamically.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

Through this assignment, I learned that synchronization is essential when multiple threads share and modify common resources concurrently. I understood how race conditions can cause inconsistent program behavior and incorrect results if shared data is not properly protected. I also learned how ReentrantLock provides mutual exclusion and guarantees safe access to critical sections.

Another important lesson was the difference between locks and semaphores. Locks are mainly used to protect shared data, while semaphores control how many threads can access a resource simultaneously. I also learned the importance of try-finally blocks to ensure locks and semaphores are always released safely and to avoid deadlocks.

This assignment improved my understanding of thread safety, concurrent programming, and synchronization design decisions. It also helped me understand how operating systems coordinate multiple processes and manage CPU access safely.
---

### Real-world applications:

Give TWO examples where synchronization is critical:

Example 1:
Banking systems use synchronization to protect account balances during concurrent transactions such as deposits, withdrawals, and transfers. Without synchronization, incorrect balances or lost transactions could occur.

Example 2:
Airline reservation systems use synchronization to prevent multiple users from booking the same seat simultaneously. Synchronization guarantees that seat allocation remains accurate and consistent.

---

### How I would explain synchronization to others:
I would explain synchronization as a traffic control system for threads. Imagine multiple cars trying to cross a one-lane bridge at the same time. Without traffic rules, accidents and collisions would happen. Synchronization works like traffic lights and barriers that organize access and prevent conflicts.

In this assignment, multiple threads represented processes trying to access shared resources such as counters and logs. ReentrantLock acted like a door lock that only allowed one thread inside the critical section at a time, while Semaphore acted like a security guard controlling how many processes could use the CPU simultaneously.
[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/reemJouf/OS-Assignment3-Reem-aljawf.git

**Number of commits**: 
9
**Commit messages**: 
1. Set my student ID
2. adding reentrantLick and import package
3. adding semaphore and import its package for sync
4. protecting shared counters using reentrantlock
5. protect the shared variable completedProcessCount using reentrantLock… 
 and in finally release the lock to prevent deadlock
 6.protecting shared counter totalWaitingTime using reentlock
 7.protecting excutionLock
 8.useing semaphore to control cpu access in process excution and releasing semaphore in finally block
9.apply semaphore in runToCompletion method
---

## Summary

**Total time spent on assignment**: 
1 day
Key takeaways:
1. Synchronization is necessary to prevent race conditions in concurrent systems.
2. ReentrantLock and Semaphore provide different synchronization functionalities.
3. Proper synchronization improves program correctness, consistency, and thread safety.

Most challenging aspect:
The most challenging part was understanding how concurrent threads could modify shared resources simultaneously and designing safe synchronization mechanisms without causing deadlocks.

What I'm most proud of:
I am most proud of successfully implementing synchronization mechanisms correctly and ensuring that the scheduler produced consistent and reliable results across multiple executions.

**End of Documentation**
