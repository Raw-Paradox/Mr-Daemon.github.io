---
title: "Basic MultiThread Concept and Application"
---

## Race Condition

This article will begin with talking about `race condition` .   

When you start to learn something about multithread programming, you cannot avoid  talking about the race condition. So what is a race condition? In this part I will introduce this annoying problem.

Let's start with a simple Java program:

```java
public class Test {
    public static int count = 0;

    public static void main(String[] args) {
        ExecutorService service = Executors.newFixedThreadPool(500);
        IntStream.range(0, 1000).forEach(i -> service.submit(Test::multiTest));
        service.shutdown();
        try {
            service.awaitTermination(1000, TimeUnit.MILLISECONDS);
        } catch (Exception e) {
            // TODO: handle exception
        }
        System.out.println("terminate with value " + count);
    }

    public static void multiTest() {
        count++;
        System.out.println("count: " + count);
    }
}
```

Guess what the final result is, you might thought the `count` must be 1000 after the program terminating, but the result is not always be 1000, the value of `count` is  nondeterministic. To execute `count++` , we need to go through 3 steps: read, increase value and write back. If two threads read the value of `count` simultaneously, the outcome of the operation could be wrong.

The example above is very simple, but if we do not be careful enough, race conditions can trigger serious and difficult-uncovered bugs.

Fortunately, we already have many solutions to solve race conditions and we will introduce a couple of primitive ways in the later chapter.

## Lock (python)

The `Lock` object can be created by `threading.Lock()` , and it has two basic functions: `.acquire()` and `.release()` .

A thread can call `my_lock.acquire()` to get the lock, if the lock is already held, the thread will wait until it is released. That is important because if the lock will never be released, the program will be stuck there. So it is highly recommended that use lock as a `context manager` , if you use the lock in the with statement, it will automatically released while the with block exit.

Here is a simple code snippet about how to use lock:

```python
testInt = 0

lock=threading.Lock()

def increment():
    global testInt
    local_copy = testInt
    time.sleep(0.5)
    local_copy += 1
    testInt = local_copy

with futures.ThreadPoolExecutor(max_workers=3) as executor:
    for i in range(10):
        executor.submit(increment)
print(testInt)
```

Like the example that we mentioned in the Race Condition part, the outcome of `print(testInt)` is also nondeterministic, but we can use lock like that:

```python
def increment():
    global testInt
    lock.acquire()
    local_copy = testInt
    time.sleep(0.5)
    local_copy += 1
    testInt = local_copy
    lock.release()
```

After doing some modifications on `increment()` , the result will meet our expectation -- it will print 10 to the terminal.

## Synchronized

Now back to the beginning sample, we raise the problem in the sample and now it's time to give a solution to this simple sample.

We just need to add a `synchronized` modifier to `Test::multiTest` method, this will make `Test::multiTest` a synchronized method, it means that `multiTest` can be executed by at most one thread at any time.

If you do not want to make whole method synchronized, you can use `sunchronized block` instead:

```java
synchronized(object){
    //some code
}
```

You need to pass an object to construct the synchronized block which is called "monitor object", the synchronized block only can be executed in only one thread on the same monitor object.

These two cases you use synchronized block:

* In a instance method:

  This case for example we can pass `this` to construct the block.

* In a static method:

  This case for example we can pass `ClassName.class` to the block.

## Producer-Consumer Problem

### Introduction

The producer-consumer problem is a standard computer science problem used to look at threading synchronized issues. 

Let's consider a situation that you need to read message from network and write them to disk, the program do not request message from network but has to listen to the network and accept messages when them come in. 

On the one hand, the race of messages come in is not stable, sometimes the system is idle (no messages come in), and other time the messages may come in burst, this part is called the producer. 

On the other hand, the speed that we write messages into disk is fast and stable but not fast enough to handle the burst of message, this part is called the consumer.

 Due to the difference of the race of producing and consuming, we need to construct a pipeline to balance them.

### Note

Note that if you want to use semaphore to give a solution to producer-consumer problem, you must need to carefully think about whether your use of your semaphore could cause deadlock.

We will not discuss above topic in this article, we just give a solution based on the library of high-level language.

### Use `Lock` to solve this problem

>  _Talk is cheap, show me the code._

There is a simple implementation of pipeline:

```python
class PipeLine:
    def __init__(self, bufferSize):
        self.lock = threading.Lock()
        self.bufferSize = bufferSize
        self.queue = list()

    def consume(self):
        while len(self.queue) == 0:
            pass
        with self.lock:
            item = self.queue.pop(0)
            return item

    def produce(self, item):
        while len(self.queue) == self.bufferSize:
            pass
        with self.lock:
            self.queue.append(item)
            print('queue append '+str(item))
```

In this `PipeLine` class, we have two functions: `consume` and `produce`, these two functions share one lock which initialized in the `__init__` function, this lock ensures that only one thread can manipulate (whether produce or consume)  `self.queue`, we can do some simple test on it:

```python
def produce(pipeLine):
    for i in range(1, 10):
        pipeLine.produce(i)
    pipeLine.produce(False)

def consume(pipeLine):
    item = pipeLine.consume()
    print(str(item))
    return item

if __name__ == "__main__":
    pipeLine = PipeLine(2)
    with ThreadPoolExecutor(max_workers=3) as executor:
        for i in range(3):
            print('thread '+str(i))
            executor.submit(produce, pipeLine)
            while executor.submit(consume, pipeLine).result():
                pass
    print('terminated')

```

Use name guard we can easily add some test on our library, don't warry about the mess output in the terminal because we paid no attention to guarantee the terminal is tidy and clean (ok I'm too lazy lol).

You can see that our `PipeLine` class has done its works perfect~ Apparently that above solution is a successful solution.

But according to the output, it seems that our executor did its work one by one and did not work asynchronous as I expect, this will be a food for thought cause I am a little busy recently.

## Food for Thought

* Do in all producer-consumer problems, consumers and producers have an bijective relationship?

* If the global interpreter lock make the executor done its work thread by thread OR my use of the `ThreadPoolExecutor` is not correct?

## Reference

1.  https://realpython.com/intro-to-python-threading/#producer-consumer-threading 
2.  https://en.wikipedia.org/wiki/Race_condition 
3.  https://en.wikipedia.org/wiki/Semaphore_(programming)#Semaphores_vs._mutexes 
4.  [https://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem](https://en.wikipedia.org/wiki/Producer–consumer_problem) 
5.  https://www.baeldung.com/java-synchronized 