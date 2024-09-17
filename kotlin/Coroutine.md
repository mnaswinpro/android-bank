# Coroutines

## What are coroutines?
Coroutines are a concurrency design pattern to simplify code that executes asynchronously.
Coroutines help to manage long-running tasks that might otherwise block the main thread and cause 
your app to freeze or crash.
Coroutines build upon familiar concepts such as threads.

## Benefits of coroutines?
### Lightweight
You can run many coroutines on a single thread due to their ability to suspend execution without blocking the thread. 
### Simplified asynchronous code
Coroutines use Kotlin's suspend keyword to make asynchronous code look and behave a bit more like synchronous code. 
This can make your code easier to read and reason about.
### Built in cancellation
Coroutines provide a structured way to cancel ongoing operations when they are no longer needed.
### Exception handling
Coroutines offer structured concurrency, which makes it easier to handle exceptions thrown during asynchronous operations.

## What is CoroutineScope?
Defines the context in which coroutines are launched. It manages the lifecycle of coroutines and handles exceptions.

## What is CoroutineDispatcher?
Determines which thread the coroutine will run on. 

## Types of Coroutine Dispatcher.
Main : The main thread, responsible for UI interactions
IO : Optimized for input/output operations like networking, file reading, or database interactions.
Default : CPU intensive tasks like complex calculations or data processing

## What is Unconfined Dispatcher? Pros and Cons.
Unconfined start executing in the current thread. However, they may later resume on different threads depending 
on the suspending functions they call.
Unconfined doesn't tie a coroutine to any particular thread.

### Pros
Non-blocking Operations: 
Suitable for operations that don't consume CPU time or block the thread, such as simple calculations or delegating 
work to other dispatchers.
Optimizations: 
In some cases, can be used to optimize performance by avoiding unnecessary thread switches
### Cons
Unpredictable Behavior: 
The thread on which an unconfined coroutine resumes can be unpredictable, making it harder to reason about the execution flow.
Potential for Deadlocks: 
In complex scenarios, using Dispatchers.Unconfined can lead to deadlocks if not used carefully

## What are coroutine builders?
Functions like launch and async that start new coroutines.

## Difference between coroutine builders and runBlocking.
Coroutine builders(launch and async) start a new coroutine without blocking the current thread.

RunBlocking creates a coroutine and blocks the current thread until that coroutine finishes.

## Why is runBlocking useful?
### Testing
It allows you to write tests for suspending functions in a synchronous way.
### Main function
In Kotlin, you can use runBlocking in your main function to start a coroutine and keep the program alive 
until the coroutine completes.
### Interoperability
It helps bridge the gap between blocking and non-blocking code, allowing you to call suspending functions 
from regular functions.

## Difference between a thread and a coroutine.
| Feature                | Threads                                                                    | Coroutines                                                              | 
|------------------------|----------------------------------------------------------------------------|-------------------------------------------------------------------------| 
| Management             | Managed by the operating system                                            | Managed by the Kotlin runtime                                           | 
| Resource Consumption   | Heavyweight; each thread consumes significant system resources             | Lightweight; multiple coroutines can run on a single thread             | 
| Context Switching      | Expensive; switching between threads involves OS overhead                  | Cheap; switching between coroutines has minimal overhead                | 
| Blocking               | Blocking a thread halts its execution, potentially impacting other threads | Suspending a coroutine pauses its execution without blocking the thread | 
| Creation & Destruction | Creating and destroying threads is resource-intensive                      | Creating and destroying coroutines is lightweight                       | 
| Programming Model      | More complex; requires explicit synchronization mechanisms                 | Simpler; uses structured concurrency and suspending functions           |

## Why are coroutines lightweight?
### Suspension and Resumption
When a coroutine encounters a long-running operation it suspends its execution instead of blocking the thread. 
The thread can then proceed to execute other coroutines or tasks.
Once the long-running operation completes, the coroutine is resumed from where it left off.
This suspension and resumption mechanism is handled by the Kotlin coroutine library, not the operating system, 
resulting in minimal overhead.
### No Context Switching
Traditional threads require context switching, where the operating system saves the state of one thread and loads 
the state of another. This process is resource-intensive. Coroutines avoid this overhead as they operate within 
the same thread context.

## How does a coroutine work internally?
Coroutines rely on CPS and state machines to manage their execution.
### Continuation-passing Style (CPS) :
Every suspend function is transformed into a function that takes an extra parameter: a continuation. 
This continuation represents the rest of the code to be executed after the suspend function completes. 
### State Machines: 
The Kotlin compiler transforms suspend functions into state machines. Each suspension point within a coroutine 
becomes a state in this state machine. When a coroutine suspends, its current state is stored, and the 
thread is freed to do other work.
### Coroutine Context 
Each coroutine has a context, which includes information like the CoroutineDispatcher and the Job. This context 
is passed along as the coroutine suspends and resumes. 
### Resumption 
When a suspended coroutine is ready to resume, the coroutine scheduler uses the stored state and continuation 
to restart the coroutine on the appropriate thread.

## What are the Different ways to start a coroutine?
### launch() 
Launches a new coroutine without blocking the current thread. It's ideal for tasks that don't need to return a result.
Returns a Job object to manage the coroutine's lifecycle like canceling it.
### async() 
Launches a coroutine that returns a result. The result is represented by a Deferred object, which you can use 
to retrieve the value later.
### runBlocking { } 
This builder starts a coroutine and blocks the current thread until the coroutine completes. 
Primarily used for testing or bridging blocking and non-blocking code.
It inherits the dispatcher from the coroutine that calls it.
### coroutineScope { } 
This function creates a new coroutine scope and suspends the current coroutine until all child coroutines within 
the scope complete.
It doesn't inherit the dispatcher. You need to explicitly specify the dispatcher if you want to deviate from the 
default (Dispatchers.Default)

## What are the Different scopes in coroutine?
### ViewModel scope 
Bound to the lifecycle of a ViewModel
### Lifecycle scope 
Bound to the lifecycle of a LifecycleOwner
### Global scope
They are not tied to any specific lifecycle. They run independently and continue even if the activity or application is destroyed.
### Custom scope
Create custom coroutine scopes using CoroutineScope() and manage their lifecycle manually.
### SupervisorScope
If a child coroutine fails with an exception, it doesn't cancel the other child coroutines or the parent scope.

* ViewModel and Lifecycle scope runs on Main Dispatcher by default, Global scope runs on Default Dispatcher.
* Custom scopes and Supervisor scope uses the explicitly provided dispatcher or use inherited dispatcher from parent coroutine.

```val customScope = CoroutineScope(SupervisorJob() + Dispatchers.Main)```
* When no dispatcher is provided on creating job, it defaults to Default dispatcher.

```val customScope = CoroutineScope(Job())```

## Difference between supervisor scope and regular scopes
| Feature                  | Regular Scope                                                      | SupervisorScope                                                  | 
|--------------------------|--------------------------------------------------------------------|------------------------------------------------------------------| 
| Exception Handling       | Exception propagates up, potentially canceling parent and siblings | Exception is isolated to the failing child coroutine             | 
| Child Coroutine Behavior | Child failures can affect other children and the parent            | Child failures don't affect other children or the parent         | 
| Use Cases                | Structured concurrency, ensuring all children complete             | Independent operations, graceful handling of individual failures |

## Difference between join() vs await()
| Feature      | join()              | await()                                | 
|--------------|---------------------|----------------------------------------| 
| Return Value | None                | Returns the result of the coroutine    | 
| Used with    | Job (from launch)   | Deferred (from async)                  | 
| Purpose      | Wait for completion | Wait for completion and get the result |

## What are the ways to handle exception in coroutine when created with launch()?
CoroutineExceptionHandler
```
val coroutineExceptionHandler = CoroutineExceptionHandler { _, throwable ->
  // Handle the exception in the current scope
  viewModelScope.launch {
    handleException(throwable)
  }
}
val scope = CoroutineScope(Dispatchers.Main + coroutineExceptionHandler)

scope.launch(Dispatchers.Main) {
  // Coroutine code here
  throw Exception("Something went wrong")
}
```

try-catch Block within the Coroutine
```
launch {
  try {
    // ... code that might throw an exception ...
  } catch (e: Exception) {
    println("Caught exception: $e")
  }
}
```
supervisorScope
```
supervisorScope {
  launch {
    // ... code that might throw an exception ...
  }
  launch {
    // This coroutine will continue running even if the first one fails
  }
}
```

## What are the ways to handle exception in coroutine when created with async()?
Try-catch around await() function call

## Why should you not use Default Dispatcher for making network calls?
* Default dispatcher is optimized for cpu intensive tasks not for waiting on external resources
* As no operation is performed while getting result from outside, cpu would be idle leading to resource wastage
* As there are only limited number of threads, app performance can degrade quickly when used for io tasks

## What happens to a coroutine when a network request is fired and screen is locked/app minimised?
If the coroutine is launched within viewModelScope or lifecycleScope, and the associated ViewModel or LifecycleOwner 
is destroyed (e.g., due to configuration changes or the activity being finished), the coroutine will be canceled.
If the coroutine is launched in GlobalScope or a custom scope that's not tied to a lifecycle, it will continue 
running even if the screen is locked or the app is minimized.

## Why should you not use Global scope?
Even if the job.cancel() is specified in onDestroy of application class, there is no guarantee it will be called 
when system kills the app in the background. And operation can keep on running resulting in memory leaks.

## How to make serial function calls using launch coroutine?
### By using join()
By adding join() after launching task1, you ensure that the coroutine suspends until task1 is finished before 
proceeding to launch task2.
```
fun makeSerialUsingLaunch() {
  viewModelScope.launch {
    coroutineScope {
      launch { 
        task1() 
      }.join() // Wait fortask1 to complete
      launch { 
        task2() 
      }
    }
  }
}
```
### By using suspended function
Suspend functions are sequential by default, calling task1() before task2() guarantees that task1 completes before task2 starts.
```
suspend fun makeSerialUsingLaunch() {
  coroutineScope {
    task1()
    task2()}
}
```

## How to make serial function calls using async coroutine?
```
fun makeSerialUsingAsync() {
    val job1 = async {
      task1()
    }
    val result1 = job1.await()
    val job2 = async {
      task2(result1)
    }
    val result2 = job2.await()
  }
```

## How to make parallel function calls using coroutine?
  ````
  fun makeParallelUsingLaunch() {
    val job1 = launch {
      task1()
    }
    val job2 = launch {
      task2()
    }
    job1.join()
    job2.join()
  }
  
  fun makeParallelUsingAsync() {
    val job1 = async {
      task1()
    }
    val job2 = async {
      task2()
    }
    val result1 = job1.await()
    val result2 = job2.await()
  }
  ````

## Can a coroutine run on multiple threads?
A single coroutine doesn't run on multiple threads simultaneously. It can suspend and resume on different 
threads, but its execution remains sequential.

## What is Structured Concurrency?
In structured concurrency, coroutines are organized in a hierarchical structure, with parent coroutines and child coroutines.
A parent coroutine cannot complete until all its child coroutines have completed. This ensures that you don't leave 
any "orphaned" coroutines running in the background.
If a child coroutine fails or throws an exception, the parent coroutine and its other children are notified and can react accordingly.
Exceptions are propagated up the coroutine hierarchy, allowing you to handle errors in a centralized and predictable manner.

## What are benefits of withContext?
withContext is a suspending function. This means it can be used within other coroutines and can suspend the 
execution of the current coroutine.
Its primary purpose is to switch the context of the coroutine to a different dispatcher.
Ensures that UI operations are performed on the main thread and blocking operations are performed on background threads.
Maintains the structured concurrency of coroutines, ensuring that all operations within withContext complete before the 
coroutine continues.

## What happens when all threads are busy in Dispatcher.io and new request comes in?
The new requests are added to a queue associated with the Dispatcher.IO
The request waits in the queue until a thread becomes available.
If the queue grows too large, it causes the dispatcher to be overloaded, dropping application performance like increased latency
and decreased reliability.
The dispatcher typically uses a fair queuing policy to ensure that requests are processed in the order they arrive.

## What are the lifecycle of a coroutine?
Created -> Started -> Running -> (Suspending <-> Resuming)* -> Completing/Cancelling

## What does the coroutine runtime do?
- Creation of coroutines using coroutine builders
- Provides Dispatchers
- Handling exceptions
- Cancelling coroutines
- Integrations with other libraries like Flow

## What is Dispatcher.main.immediate? What are its advantages and disadvantages?
It is a special dispatcher that offers a way to execute a coroutine on the main thread immediately bypassing the 
regular dispatch mechanism (queue of tasks to execute).

### Use cases
- Initial UI setup: If you need to perform crucial UI initialization before the first frame is drawn, you might 
use Dispatchers.Main.immediate.
- Responding to critical user interactions: In cases where immediate feedback is essential (e.g., handling a 
touch event), this dispatcher can ensure responsiveness.

### Cautions
- Race Conditions: If your immediate coroutine depends on something that another coroutine in the queue was 
supposed to do first, you might get unexpected results or crashes.
- Visual Glitches: Imagine a coroutine in the queue was about to update the UI. If your immediate coroutine modifies 
the same UI elements before that, the user might see a flicker or an inconsistent state.

## How to check if a coroutine is active or not? How to cancel it?
```
val scope = CoroutineScope(Job() + Dispatchers.Main)
val job = scope.launch {
    // Your coroutine code here
}

if (job.isActive) {
    // Coroutine is still active
    job.cancel()
} else {
    // Coroutine has completed or been canceled
}
```

## What is thread confinement?
Thread confinement is a concurrency concept that operations on an object or a piece of code is only accessed from a single 
thread at a time. This helps prevent data races and other concurrency issues that can arise when multiple threads 
try to modify shared data simultaneously.



