# Condition Variable

## implementation1

![Untitled](Condition%20Variable%20c5dc0e9c80144001997d58d43b75c11c/Untitled.png)

- it is hugely inefficient as the parent spins and wastes CPU time

### 基本概念

- A **condition variable** is an explicitqueue that threads can put themselves on when some state of execution(i.e., some **condition**) is not as desired (by **waiting** on the condition); some other thread, when it changes said state, can then wake one (or more) of those waiting threads and thus allow them to continue (by **signaling** on the condition)（条件变量是一个显式队列，当某些执行状态（即某些条件）不理想时（通过等待条件），线程可以设置自己；一些其他线程，当它改变所述状态时，可以唤醒一个（或多个）等待的线程，从而允许它们继续（通过对条件发出信令）
- pthread_cond_t c：declares c as a condition variable
- wait(): The wait() call is executed when a thread wishes to put itself to sleep
- signal():is executed when a thread has changed something in the program and thus wants to wake a sleeping thread waiting on this condition.
- an example:
    
    ![Untitled](Condition%20Variable%20c5dc0e9c80144001997d58d43b75c11c/Untitled%201.png)
    
    - wait() call is that it also takes a mutex as a parameter; it assumes that this mutex is locked when wait() is called. The responsibility of wait() is to release the lock and put the calling thread to sleep (atomically); when the thread wakes up (after some other thread has signaled it), it must re-acquire the lock before returning to the caller.(它必须在返回给调用者之前重新获取锁)

### 案例分析

![Untitled](Condition%20Variable%20c5dc0e9c80144001997d58d43b75c11c/Untitled%202.png)

![Untitled](Condition%20Variable%20c5dc0e9c80144001997d58d43b75c11c/Untitled%203.png)

- the importance of the state variable done; it records the value the threads are interested in
knowing. The sleeping, waking, and locking all are built around it.
- 用while而不是if的原因？

## The Producer/Consumer (Bounded Buffer) Problem

![Untitled](Condition%20Variable%20c5dc0e9c80144001997d58d43b75c11c/Untitled%204.png)

![Untitled](Condition%20Variable%20c5dc0e9c80144001997d58d43b75c11c/Untitled%205.png)

![Untitled](Condition%20Variable%20c5dc0e9c80144001997d58d43b75c11c/Untitled%206.png)

- using while instead if: When checking for a condition in a multi-threaded program, using a while loop is always correct; using an if statement only might be, depending on the semantics of signaling. Thus, always use while and your code will behave as expected.
- use *two* condition variables: instead of one, in order to properly signal which type of thread should wake up when the state of the system changes
- A producer only sleeps if all buffers are currently filled (p2); similarly, a consumer only sleeps if all buffers are currently empty (c2). And thus we solve the producer/consumer problem; time to sit back and drink a cold one.
- p3 mutex的作用：防止race，

## Covering Conditions

![Untitled](Condition%20Variable%20c5dc0e9c80144001997d58d43b75c11c/Untitled%207.png)

- 存在的问题：the thread waking other threads does not know which thread (or threads) to wake up。
- 解决方案：replace the pthread cond signal() call in the code above with a call to pthread_cond_broadcast(), which wakes up *all* waiting threads.
    - 优点：guarantee that any threads that should be woken are
    - 缺点：might needlessly wake up many other waiting threads that shouldn’t (yet) be awake