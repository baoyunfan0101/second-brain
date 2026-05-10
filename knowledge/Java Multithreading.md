---
tags:
  - java
  - concurrency
summary: synchronized/volatile/ReentrantLock, thread pool
use_cases:
  - backend
  - server
  - multithreading
---

# Java Multithreading

## Creating Threads

**Method 1: Extending `Thread`**

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("New thread running");
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

`t.start()`
- Creates a real new thread
- Executes `run()` concurrently

`t.run()`
- Only a normal method call
- Still runs in the current thread

`t.join()`
- Blocks the current thread
- Waits until thread `t` finishes execution

**Method 2: Implementing `Runnable`**

```java
class Task implements Runnable {
    @Override
    public void run() {
        System.out.println("Task running");
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(new Task());
        t.start();
    }
}
```

## `synchronized`

```java
class Counter {
    private int count = 0;

    // 1. synchronized instance method
    public synchronized void add1() {
        count++;
    }

    // 2. synchronized block
    public void add2() {
        synchronized(this) {
            count++;
        }
    }

    // 3. synchronized static method
    public static synchronized void test1() {
        System.out.println("static method lock");
    }

    // 4. synchronized on class object
    public void test2() {
        synchronized(Counter.class) {
            System.out.println("class lock");
        }
    }

    public int get() {
        return count;
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        Counter c = new Counter();
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                c.add1();
            }
        });
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                c.add2();
            }
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        Counter.test1();
        c.test2();

        System.out.println(c.get());
    }
}
```

1. `synchronized` Instance Method
	- Locks the ==current object== (`this`)
	- Different instances use different locks

2. `synchronized(this)`
	- Explicitly locks the current object

3. `static synchronized` Method
	- Locks the ==class object==
	- All instances share the same lock

4. `synchronized(Counter.class)`
	- Explicitly locks the class object

## `volatile`

```java
public class Main {
    volatile static boolean running = true;

    public static void main(String[] args) throws Exception {
        Thread t = new Thread(() -> {
            while (running) {
            }
            System.out.println("Thread stopped");
        });

        t.start();

        Thread.sleep(1000);

        running = false;
    }
}
```

`volatile static` Field
- Guarantees visibility between threads  
- Prevents instruction reordering  
- Does NOT guarantee atomicity (NOT thread-safe)

## ReentrantLock

```java
import java.util.concurrent.locks.ReentrantLock;

public class Main {
    static int count = 0;
    static ReentrantLock lock = new ReentrantLock();

    public static void main(String[] args) throws Exception {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                lock.lock();

                try {
                    count++;
                } finally {
                    lock.unlock();
                }
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                lock.lock();

                try {
                    count++;
                } finally {
                    lock.unlock();
                }
            }
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println(count);
    }
}
```

`ReentrantLock lock = new ReentrantLock()`
- Locks ==this `ReentrantLock` instance==

`static ReentrantLock lock = new ReentrantLock()`
- Locks ==all `ReentrantLock` instances of the class==

`lock.lock()`
- Current thread tries to acquire the lock
- Waits until the lock is acquired
- Threads using the same lock object are mutually exclusive

`lock.unlock()`
- Releases the lock
- Usually written in `finally`

`lock.tryLock()`
- Current thread tries to acquire the lock ==immediately==
- Returns `true` if success, `false` if failed

`lock.lockInterruptibly()`
- Current thread tries to acquire the lock
- Waits until the lock is acquired
- Waiting ==can be interrupted==

**Fair lock**
`ReentrantLock lock = new ReentrantLock(true)`
- Threads acquire lock in waiting order (FIFO)
- First waiting thread gets the lock first
- Prevents thread starvation
- Slower than non-fair lock (default)

**Mechanism**
- <span class="abbr" data-title="Compare And Swap">CAS</span>
- AQS (AbstractQueuedSynchronizer)

**Compared with `synchronized`**
- More flexible
- Supports interruptible lock
- Supports fair lock
- Supports `tryLock()`

## Thread Pool

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {
    public static void main(String[] args) {
        ExecutorService pool = Executors.newFixedThreadPool(3);

        for (int i = 1; i <= 5; i++) {
            int taskId = i;

            pool.submit(() -> {
                System.out.println("Task " + taskId + " running in " +
                        Thread.currentThread().getName());
            });
        }

        pool.shutdown();
    }
}
```

`Executors.newFixedThreadPool(3)`
- Creates a thread pool with 3 worker threads
- Threads are reused instead of recreated
- Submitted tasks execute concurrently
- Extra tasks wait in the queue

`pool.shutdown()`
- Stops accepting new tasks
- Allows existing tasks to finish

**Advantages**
- Reuses threads
- Reduces creation overhead

**Parameters**
```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) {
		ThreadPoolExecutor pool =
            new ThreadPoolExecutor(
                3,                      // corePoolSize
                6,                      // maximumPoolSize
                60,                     // keepAliveTime
                TimeUnit.SECONDS,       // time unit
                new ArrayBlockingQueue<>(2), // workQueue
                new ThreadPoolExecutor.AbortPolicy() // reject policy
            );

        for (int i = 1; i <= 5; i++) {
            int id = i;

            pool.execute(() -> {
                System.out.println(
                    Thread.currentThread().getName()
                    + " running task " + id
                );
            });
        }

        pool.shutdown();
    }
}
```

`corePoolSize`
- Minimum number of worker threads

`maximumPoolSize`
- Maximum number of worker threads

`keepAliveTime`
- Time before idle extra threads are removed

`workQueue`
- Queue storing waiting tasks

`rejectHandler`
- Strategy when the pool is full

## CAS

```java
import java.util.concurrent.atomic.AtomicInteger;

public class Main {
    public static void main(String[] args) throws Exception {
        AtomicInteger count = new AtomicInteger();
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                count.incrementAndGet();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                count.incrementAndGet();
            }
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println(count.get());
    }
}
```

`AtomicInteger`
- Creates a thread-safe integer

`count.incrementAndGet()`
- Reads current value as expected value
- updates only if `memory value == expected value`
- Retry if another thread modified the value

`count.get()`
- Returns the current value

## ThreadLocal

```java
public class Main {
    static ThreadLocal<Integer> local = new ThreadLocal<>();

    public static void main(String[] args) throws Exception {

        Thread t1 = new Thread(() -> {
            local.set(100);

            System.out.println(
                Thread.currentThread().getName() + ": " + local.get()
            );

            local.remove();
        });

        Thread t2 = new Thread(() -> {
            local.set(200);

            System.out.println(
                Thread.currentThread().getName() + ": " + local.get()
            );

            local.remove();
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();
    }
}
```

`ThreadLocal<Integer> local = new ThreadLocal<>()`
- Creates a thread-local variable

`local.set(100)`
- Stores value in current thread's own `ThreadLocalMap`
- `map.put(local, 100)`

`local.get()`
- Reads value from current thread's own `ThreadLocalMap`
- `map.get(local)`

`local.remove()`
- Removes value from current thread's own `ThreadLocalMap`
- `map.remove(local)`

**Structure**
```text
Thread
 └── ThreadLocalMap
      ├── key: ThreadLocal
      └── value: thread-local value
```

**`ThreadLocalMap`**
- Underlying storage of `ThreadLocal`
- Key is `ThreadLocal`
- Value is current thread's value

**Memory Leak Risk**
- `ThreadLocalMap` holds a ==weak reference== to `ThreadLocal` (GC can collect the object even if referenced)
- `ThreadLocalMap` holds a ==strong reference== to value (GC cannot collect the object while referenced)
- User code usually does not directly reference `ThreadLocalMap`, so value may still be strongly referenced and cannot be GCed
- In thread pools, always call `remove()` after use