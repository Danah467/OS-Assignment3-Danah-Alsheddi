# Assignment 3 - Complete Documentation

**Student Name**: Danah Alsheddi
**Student ID**: 445052125
**Date Submitted**: 7/5/2026

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

### Entry 1 - [april27, 8:30pm]
**What I implemented**: 
I started by reviewing the starter code and identifying all shared resources that require synchronization. I added the ReentrantLock for the three counters and verified that each update happens inside a protected critical section.
**Challenges encountered**: 
Understanding which variables were truly shared across threads required careful reading of the code.
**How I solved it**: 
I traced how each counter was accessed inside the scheduler loop and confirmed they were modified by multiple threads.
**Testing approach**: 
I ran the program several times to ensure the counters updated consistently.
**Time spent**: 
45 minutes.
---

### Entry 2 - [april28, 4:10pm]
**What I implemented**: 
I added a second lock to protect the executionLog ArrayList to prevent concurrent modifications.
**Challenges encountered**: 
The log was updated from multiple threads, and I needed to ensure no thread could write while another was reading.
**How I solved it**: 
I wrapped all log updates inside a dedicated logLock to isolate log operations.
**Testing approach**: 
I intentionally increased the number of processes to stress‑test the log and ensure no ConcurrentModificationException occurred.

**Time spent**: 
1 hour.
---

### Entry 3 - [april 29, 9:20pm]
**What I implemented**: 
I implemented the binary semaphore to control CPU access and ensure only one process executes its quantum at a time.
**Challenges encountered**: 
Ensuring that the semaphore is always released, even when exceptions occur.
**How I solved it**: 
I used a try–catch–finally structure so the semaphore is released inside the finally block.

**Testing approach**: 
I ran the scheduler multiple times and verified that no deadlocks occurred and that each process executed sequentially.
**Time spent**: 
50 minutes.
---

### Entry 4 - [april 30, 7:45pm]
**What I implemented**: 
I refined the run() method to include progress bars, waiting time calculation, and proper logging for each quantum execution.
**Challenges encountered**: 
Maintaining clean structure while handling multiple responsibilities inside run().

**How I solved it**: 
I reorganized the code into clear steps: acquire CPU, execute quantum, update progress, and release CPU.
**Testing approach**: 
I tested with different time quantum values to ensure the behavior remained consistent.
**Time spent**: 
1 hour.
---

### Entry 5 - [may 1, 3:30 pm]
**What I implemented**: 
I completed the runToCompletion() method for the final process and verified that the scheduler terminates correctly.
**Challenges encountered**: 
Ensuring the last process does not re-enter the queue and that waiting time is calculated correctly.
**How I solved it**: 
I added a direct call to runToCompletion() when the ready queue becomes empty.
**Testing approach**: 
I ran the program five times and confirmed that all processes completed and the final statistics were correct.

**Time spent**: 
40 minutes.
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:The first race condition occurred in the shared counters: contextSwitchCount, completedProcessCount, and totalWaitingTime. These variables were updated by multiple threads at the same time, which could lead to lost updates because increment operations are not atomic. For example, two threads could read the same value and both write back an outdated result, producing incorrect totals.

The second race condition occurred in the shared executionLog ArrayList. Since ArrayList is not thread‑safe, concurrent writes could cause inconsistent log entries or even a ConcurrentModificationException. Multiple threads adding messages at the same time could corrupt the internal structure of the list.

Both race conditions were solved by protecting the counters with a ReentrantLock and protecting the log with a separate logLock.

[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:A ReentrantLock provides mutual exclusion for critical sections where only one thread should access a shared variable at a time. It is ideal for protecting small, fast operations like updating counters or writing to a log. In my code, I used ReentrantLock to protect the counters and the execution log because these operations require strict atomicity.

A Semaphore controls access to a shared resource with a limited number of permits. I used a binary semaphore (new Semaphore(1)) to ensure that only one process can use the CPU at a time. This models real CPU scheduling and prevents multiple threads from executing their quantum simultaneously. The semaphore enforces exclusive CPU access, while locks protect shared data structures.


[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:Deadlock is a situation where two or more threads are waiting for each other forever, causing the program to freeze. It happens when threads hold resources and wait for others in a circular dependency.

Two prevention techniques I used are:

1- Using try‑finally blocks for releasing resources
I always release the semaphore inside a finally block. This guarantees that the CPU permit is released even if an exception occurs, preventing a thread from holding the CPU forever.
2- Using simple and consistent locking structure
I avoided nested locks and used a single lock for the counters. This prevents circular waiting and reduces the chance of deadlock.


These techniques ensure that the scheduler never freezes and all processes complete successfully.



[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:I used one lock to protect all three counters (coarse‑grained locking). This design is simpler and ensures that updates to related counters remain consistent. It also avoids the complexity of managing multiple locks and reduces the risk of deadlock.

The trade‑off is that coarse‑grained locking reduces concurrency because only one thread can update any counter at a time. Fine‑grained locking (one lock per counter) increases concurrency but adds complexity and requires careful lock ordering to avoid deadlocks.

Since the counters are independent and the operations are very fast, fine‑grained locking could theoretically provide better concurrency. However, for this assignment, coarse‑grained locking is safer, easier to maintain, and fully meets the synchronization requirements.


[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, totalWaitingTime

**Why they need protection**: 
These counters are shared by all running threads. Without synchronization, two or more threads could update the same counter at the same time, causing lost updates and incorrect final values. Increment operations are not atomic, so race conditions would occur.
**Synchronization mechanism used**: 
A single ReentrantLock (coarse‑grained lock)

**Code snippet**:
```java
// Paste your implementation here
```
SharedResources.lock.lock();
try {
    contextSwitchCount++;
} finally {
    SharedResources.lock.unlock();
}

**Justification**: 
Justification:
Using one lock for all counters ensures atomic updates and prevents inconsistent results. The operations are short, so the overhead is minimal. This approach is simple, safe, and avoids deadlocks.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog — a shared ArrayList used by all threads to record events.
**Why it needs protection**: 
ArrayList is not thread‑safe. Concurrent writes can corrupt the internal structure or cause ConcurrentModificationException. Multiple threads adding log entries at the same time would lead to inconsistent or missing logs.
**Synchronization mechanism used**: 
A dedicated logLock (ReentrantLock)
**Code snippet**:
```java
// Paste your implementation here
```

SharedResources.logLock.lock();
try {
    executionLog.add(message);
} finally {
    SharedResources.logLock.unlock();
}

**Justification**: 
Using a separate lock improves concurrency because log operations are independent from counter updates. This prevents log corruption and ensures that all entries are recorded correctly.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To ensure that only one process can use the CPU at a time. This models a single‑core CPU and prevents multiple threads from executing their quantum simultaneously.

**Number of permits and why**: 
1 permit — binary semaphore
This enforces exclusive CPU access.
**Where implemented**: 
Inside both run() and runToCompletion() methods.

**Code snippet**:
```java
// Paste your implementation here
```
try {
    SharedResources.cpuSemaphore.acquire();
    // CPU execution
} finally {
    SharedResources.cpuSemaphore.release();
}

**Effect on program behavior**: 
The semaphore guarantees that only one process runs at a time, preventing overlapping execution. It also ensures fairness and prevents deadlocks because the permit is always released in the finally block.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

**Results**: 

**What this proves**: 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

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

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

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
