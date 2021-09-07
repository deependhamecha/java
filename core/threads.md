# Threads

In Java, "thread" means two different things:
- An instance of class java.lang Thread
- A thread of execution

An instance of Thread is just...an object. Like any other object in Java, it has variables and ethods, and lives and dies on the heap. But a thread of execution is an individual process (lightweight process) that has its own call stack.
In Java, there is one thread per call stack- or, to think of it in reverse, on call stack per thread. Even if you dont create any new threads in your program, threads are back there running.

The `main()` method that starts the whole ball rolling, runs in one thread, called the *main* thread.

In some JVM, the Java threads are actually mapped to native OS threads. You and the OS can create a second kind of thread called a *daemon thread*.

## Making Thread
You write code to be run inside a method named `run()` for thread to run.
```java
public void run() {
// CODE
}
```

You always write the code that needs to be run in a separate thread in a run() method. The run() method will call other methods, of course, but the thread of execution- the new call stack- always begins by invoking run().

You can define and instantiate a thread in one of two ways:
- Extend the java.lang.Thread class
- Implement the Runnable interface

The only time it really makes sense to extend thread is when you havve a more specialized version of Thread class. In other words, becuase you have more specialized thread-specific behaviour. If you implement `Runnable`, then you can also extend any other class.

## Defining Thread

```java
class MyClass implements Runnable {
  public void run() {
    // CODE
  }
}
```

If you overload the `run` method it will be ignored unless you call it youself.

## Instantiating a Thread

Every thread of execution begins as an instance of class Thread. Regardless of whether your `run()` method is in a Thread subclass or a Runnable implementation class, you still need a Thread object to do the work.

```java
MyClass c = new MyClass();
Thread t = new Thread(c);
c.start()
```

If you create a thread using the no-arg constructor, the thread will call its own `run()` method when it's time to start working. That's exactly what you want when you extend Thread, but when you use Runnable, you need to tell the new thread to use your `run()` method rather than its own. The Runnable you pass to the Thread constructor is called the *target* or the *target Runnable*.

> Thread class itself implements Runnable.

When a thread has been instantiated but not started (start() is not called), the thread is said to be in the new state. At this stage, the thread is not yet considered to be aive. Once the start() method is called, the thread is considered to be alive(even if run() isnt started yet). A thread is considered dead(no longer alive) after the run() method completes. The `isAlive()` method is the best way to determine if a thread has been started but has not yet completed its run() method.

We can use the `getName()` method of class Thread, and have each Runnable print out the name of the thread executing that Runnable object's `run()` method. Main thread already has a name *main*.

`Thread.currentThread()` method returns a reference to the currently executing thread, and then we invoked `getName()` on that return reference.

*A thread is done being a thread when its target `run()` method completes.*
The stack of that thread dissolves, and the thread is considered dead.

It's a stil a Thread object, just not a thread of execution. So if you've got a reference to a Thread instance, then even when, that Thread instance is no longer a thread of execution, you can still call methods on Thread instance, just like any other Java object.

*Once a thread has been started, it can never be started again.*

## Thread Scheduler

Only one stack can ever be executing at one time. Any thread in the runnable state can be chosen by the scheduler to be the one and only running thread.

*The order in which runnable threads are chosen to run is not guaranteed.*

### Some Methods from Thread class

```java
public static void sleep(long millis) throws InterruptedException
public static void yield()
public final void join() throws InterruptedException
public final void setPriority(int newPriority)
```

> Note that both `sleep()` and `join()` have overloaded versions.

### Methods from Object class

```java
public final void wait() throws InterruptedException
public final void notify()
public final void notifyAll()
```

The `wait()` method has three overloaded versions.

One important point is that one thread does not tell another thread to block. Some methods may look like they tell another thread to block, but they don't.

```java
t.sleep();    OR    t.yield();
```

But those are actually static methods of the Thread class- they ney are defined to always affect the thread that's currently executing.

There is a method, `suspend()`, in the Thread class, that lets one thread tell another to suspend, but the `suspend()` method has been deprecated.

> A thread in a blocked state is still considered to be *alive*.

A thread is *dead* when its `run()` method completes.

## Preventing Thread execution

- Sleeping
- Waiting
- Blocked because it needs an object's lock

### Sleeping

The `sleep()` method is static method of clss Thread. You use it  to "slow" a thread down.

```java
try {
  Thread.sleep(5*60*1000); // 5min // Throws checked exception
} catch(InterruptedException e) {}
```

If it is interrupted then it throws the Exception.

> It puts the currently running thread to sleep no matter which object is used to call it.

### Thread Priority

### yield()

`yield()` is supposed to do is make the currently running thread head back to runnable to allow other threads of the **same priority** to get their turn. So the intention is to use `yield()` to promote graceful turn-taking among equal-priority threads. In reality, though the `yield()` method isn't guaranteed to do what it claims.

A `yield()` won't ever cause a thread to go to the waiting/sleeping/blocking state. At most, a `yield()` will cause a thread to go from running to runnable, but again, it might have no effect at all.

### join()

```java
Thread t = new Thread();
t.start();
t.join();
```

It takes the currently running thread to join this Thread instance. This blocks the current thread from runnable until after the thread referenced by `t` is no longer alive. You can also call one of the overloaded versions of `join()` that takes a timeout duration, sothat you're saying, "wait until thread t is done but if takes longer than 5,000 ms, then stop waiting and become runnable anyway".