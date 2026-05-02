# Assignment 3 - Complete Documentation

**Student Name**: [Turki Khaild Bin Askar]  
**Student ID**: [445050245]  
**Date Submitted**: [5/2/2026]

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

### Entry 1 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 2 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 3 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

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
it is affectd on the contextswitch resource because multipuble threads want to change it at the same time 
and Concurrent access is problem because the java operations used to update these resources are not atomic
he program might produce different outcomes each time it is run even with the same seed because
the race between threads is unpredictable
[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:The locks protects data variables counters and logs from race conditions during simultaneous updates
where semaphore ensure only one process thread executes on the CPU at a time

from my code for lokcks
contextSwitchLock.lock(); 
        try {
            contextSwitchCount++;
        } finally {
            contextSwitchLock.unlock(); 
        }
      ------- 
        for semaphore
         try {
        SharedResources.cpuSemaphore.acquire();   

        
[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
deadlock is a situation in operating systems where two or more threads are blocked forever, each waiting for a resource that the other thread holds
Mutual Exclusion Avoidance : If a resource can be shared multiple threads can use it at once removing the need for a lock.
Hold and Wait Prevention: Requiring a process to request all its required resources  at the same time before it begins execution or to release its current resources before requesting new ones.
[Your answer here - reference try-finally blocks, lock ordering, etc.]
 I placed all unlock() and release() calls inside a finally block to ensure that resources are always freed, even if an exception occurs during execution.
 } finally {
            // TODO #4: Release CPU semaphore here
            // Always release in finally block to prevent deadlocks!
             SharedResources.cpuSemaphore.release(); 
    }
---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**: I chose to implement separate locks for each counter, which is known as fine-grained locking locking, to protect contextSwitchCount, completedProcessCount, and totalWaitingTime.
I made this choice because the three counters are logically independent; updating the context switch count does not affect the calculation of total waiting time. Fine-grained locking provides better concurrency because it allows multiple threads to update different counters simultaneously without waiting for a single global lock to be released. The trade-off is that fine-grained locking locking increases code complexity and consumes more memory by creating multiple lock objects
By using separate locks, I minimized thread contention, ensuring that a thread updating the completion count doesn't block another thread from logging waiting time.
[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextswitch

**Why they need protection**: avoide race condition

**Synchronization mechanism used**: Locks

**Code snippet**:
```java
contextSwitchLock.lock(); 
        try {
            contextSwitchCount++;
        } finally {
            contextSwitchLock.unlock(); 
        }
```

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: executionLog

**Why it needs protection**: avide multipable threads req at the same time 

**Synchronization mechanism used**: ReentrantLock

**Code snippet**:
```java
 executionLogLock.lock();
        try {
            executionLog.add(message);
        } finally {
            executionLogLock.unlock();
        }
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: ensures that only a specific number of processes can"execute their code simultaneously

**Number of permits and why**: The semaphore is initialized with 1 permit This makes it a binary semaphore, which is used to enforce mutual exclusion, ensuring that only one process thread can run its time quantum at a time

**Where implemented**: Process.run()

**Code snippet**:
```java
try {
        SharedResources.cpuSemaphore.acquire();
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
(Show that running multiple times produces consistent, correct results)
the "Total Processes Completed" always matched the number of processes initialized (20), and the "Total Context Switches" remained consistent for the given workload. There were no ConcurrentModificationException errors, and the "Average Waiting Time" remained mathematically sound across all runs.

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)
Synchronization is necessary because the simulation uses shared variables like totalWaitingTime and contextSwitchCount. Without it, Race Conditions could occur

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: I increased the number of processes in the main method (from 20 to 50) to create higher thread contention.

**Results**: The program executed all 50 processes successfully without throwing any exceptions.

**What this proves**: This proves that the fine-grained locking (separate locks) used to protect the executionLog is working perfectly.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**:
Total Processes Completed: 20
Total Burst Time: The sum of all individual process burst times
Context Switches: At least 20 (one for each process starting) plus any additional switches caused by the Round Robin time quantum

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:
What I learned about synchronization:
Through this assignment, I learned that synchronization is not just about making code work, but about ensuring thread safety in a shared environment. I discovered that even simple operations like incrementing a counter can fail due to race conditions because they are not atomic at the cpu level. Using ReentrantLock taught me how to create critical sections that protect data integrity, while the Semaphore illustrated how to manage limited logical resources like a CPU core. One of the biggest challenges was understanding the necessity of the try-finally block; I realized that failing to release a lock in the finally section could lead to a permanent deadlock, freezing the entire simulation. I also gained insight into lock granularity, learning that fine-grained locking can improve performance by allowing independent tasks to run in parallel

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Banking and ATM Systems

**Example 2**: Ticket Booking

---

### How I would explain synchronization to others:
The Gatekeeper (Semaphore): Imagine the CPU is a small room that only fits one person. The Semaphore is the "Key" hanging on the door. To enter the room and run your code, you must take the key. If the key is gone, you wait in line. When the person inside finishes their turn, they hang the key back up for the next person.

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/TurkiBinAskar/OS-Assignment3-Turki-Askar/tree/main

**Number of commits**: 4

**Commit messages**: 
1. Add CPU semaphore acquisition and release in runToCompletion
2. Acquire and release CPU semaphore in run method
3. Implement locks and semaphore
4. Set my student ID: 445050245

---

## Summary

**Total time spent on assignment**: 6 hours

**Key takeaways**: 
1. Race Conditions
2. Resource Management
3. Locks Code

**Most challenging aspect**: time

**What I'm most proud of**: im really not proud at all . i know about it late and do it under prussrue and that is my mistake

---

**End of Documentation**
