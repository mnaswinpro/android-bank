# Coroutines

## What are coroutines?
* Coroutines is a concurrency framework that can be used to write asynchronous code.
* They can improve the performance and responsiveness of applications by suspending and resuming the task execution.
* They are similar to threads but lightweight.
* They are implemented using cooperative nature of functions.
* Useful for long-running tasks like network requests, database operations, file operations, data transformations etc.

## Difference between a thread and a coroutine.
| Thread                                                                            | Coroutine                                                                        |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| Managed by the operating system                                                   | Managed by the user                                                              |
| Creating and managing threads can be expensive in terms of memory and performance | Coroutines are lightweight and can be created and destroyed quickly              |
| Threads run in parallel and share resources                                       | Coroutine cooperates with each others to complete a task                         |
| Threads require explicit synchronization mechanisms to safely access resources    | Coroutines can use language constructs to synchronize access to shared resources |

## Why are coroutines lightweight?
* They are managed by the coroutine runtime and not the Operating System like threads.
* They share Threads concurrently, which has less memory footprint compared to threads with their own memory stack.
* They suspend execution to free up the thread for other coroutines to be run on the same thread.

## How does a coroutine work internally?
* When kotlin compiler converts file to byte code, in suspend functions a new Continuation param is added, which 
  captures the state of the coroutine at the point of suspension, including local variables and the program counter.
* When the coroutine encounters a suspension point, it hands over the control to the coroutine runtime, which can
  either resume another coroutine or return control to the main thread.
* On completion of long-running task, the state helps to resume the suspended function from where it left off.

## What is a coroutine Dispatcher?
* A coroutine dispatcher is a component that determines on which thread or thread pool the coroutine should run to execute its code.
* It manages the execution of single or multiple coroutines on same or different threads to improve concurrency.

## What are the different types of Dispatchers?
* Dispatchers.Main - To run on Main thread - 1 thread only
* Dispatchers.IO - For I/O bound tasks like network requests, database operations etc - 64 to 128 threads based on kotlin version
* Dispatchers.Default - For CPU intensive tasks like File reading, complex data computations - Number of threads equals to number of CPU cores available
* Dispatchers.Unconfined - runs the coroutine in the current thread until it suspends, and then resumes it in the
  thread that calls its continuation - No dedicated thread pool

## What are the Different ways to start a coroutine?
* launch() - suspending function that starts a new coroutine and runs it
* async() - suspending function that starts a new coroutine which returns a Deferred object
* runBlocking { } - non-suspending function that blocks the current thread until the coroutine that it starts completes

## What are the Different scopes in coroutine?
* GlobalScope - To run coroutine with application scope, this scope will continue to run until the application stops
* CoroutineScope - General purpose scope that needs to be cancelled by the user upon task completion. Scope is also cancelled 
  when one of the child job fails. Used with a single try-catch to capture error.
* SupervisorScope - Specialized scope that does not cancel entire scope when one of the child jobs fails, instead other child 
  jobs will continue to run. Used with multiple try-catch to capture individual errors.
* LifecycleScope - To run coroutine in lifecycle owner (Activity/Fragment)
* ViewModelScope - To run coroutine in ViewModel
* MainScope - To execute tasks on the main ui thread

## Difference between join() vs await()
| join()                                                   | await()                                               |
|----------------------------------------------------------|-------------------------------------------------------|
| Used to wait for the coroutine to complete its execution | Used to retrieve a Deferred result from the coroutine |
| Can be used with both launch and async                   | Only used with async                                  |

## What are the ways to handle exception in coroutine when created with launch()?
1. Try-catch
   ````
   fun doSomething() {
    val job = coroutineScope.launch {
        try {
            val result = fetchSomeData()
            processData(result)
        } catch (e: Exception) {
            // Handle specific exceptions
            println("Exception caught in coroutine: ${e.message}")
        }
      }
      job.join()
    }
   ````
2. Coroutine Exception handler
    ````
    val coroutineExceptionHandler = CoroutineExceptionHandler { coroutineContext, throwable ->
        // Handle the exception on the main thread
        GlobalScope.launch(Dispatchers.Main) {
            handleException(throwable)
        }
    }
    
    val scope = CoroutineScope(Dispatchers.Main + coroutineExceptionHandler)
    
    scope.launch {
        // Coroutine code here
        throw Exception("Something went wrong")
    }   
    ````
   Note: Supervisor job can be mixed with coroutine exception handler
    ````
    val exceptionHandler = CoroutineExceptionHandler { _, exception ->
        println("Caught an exception: ${exception.message}")
    }
   
    val supervisorJob = SupervisorJob()
    
    val scope = CoroutineScope(Dispatchers.Default + supervisorJob + exceptionHandler)
    ````
3. Supervisor job to capture only failed child jobs

## How to handle exception in coroutine when created with async()?
* Try-catch around await() function call

## Why should you not use Default Dispatcher for making network calls?
* Default dispatcher is optimized for cpu intensive tasks not for waiting on external resources
* As no operation is performed while getting result from outside, cpu would be idle leading to resource wastage
* As there are only limited number of threads, app performance can degrade quickly when used for io tasks
* There are no error handling for io tasks like Network timeouts, Dns resolution etc.

## How to change default thread size for io dispatcher?
  ````
  class App : Application() {
  
      override fun onCreate() {
          super.onCreate()
          System.setProperty(IO_PARALLELISM_PROPERTY_NAME, "1000")
      }
  }
  ````

## What happens to a coroutine when a network request is fired and screen is locked/app minimised?
It continues to execute until the scope is alive or task is completed. Updates on view layer happens when
app is resumed. The data will be set on the background thread. Observers observe the change when app is resumed and
acts accordingly.

## How to make serial function calls using coroutine?
  ````
  fun makeSerialUsingLaunch() {
    val job1 = launch {
      task1()
    }
    job1.join()
    val job2 = launch {
      task2()
    }
    job2.join()
  }
  
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
  ````

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
* Yes, when using IO dispatcher, coroutine can be suspended on one thread and later resumed on another thread when the 
  initial thread is busy with other task. This thread switching is managed by the Dispatcher to efficiently manage resources.

## What happens when all threads are busy in Dispatcher.io and new request comes in?
* The new requests are added to a queue maintained by the dispatcher.
* If the queue grows too large, it causes the dispatcher to be overloaded, dropping application performance like increased latency 
  and decreased reliability.
* Dispatcher queue overloading can be avoided using certain back pressure strategies with a bounded queue.

## How to switch between dispatchers in a coroutine?
* using withContext(Dispatcher.IO) { } - It does not create a new coroutine

## What are the lifecycle of a coroutine?
* New: A coroutine is created but not yet started.
* Active: The coroutine is running.
* Suspended: The coroutine has voluntarily suspended its execution, waiting for an event to occur.
* Cancelled: The coroutine has been cancelled and will not complete its execution.
* Completed: The coroutine has finished executing successfully.
