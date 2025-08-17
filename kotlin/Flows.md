# Flow

## What is a flow?
A flow is a type of asynchronous data stream that sequentially emits values and completes normally or with an exception.

### Emits values sequentially
A flow emits values one after the other in a sequential manner.
### Asynchronous
Flows are asynchronous and can execute operations without blocking the main thread.
### Cold
Flows are cold, meaning that the code inside the flow builder does not run until the flow is collected.
### Can be operated on
Flows can be transformed and combined using operators such as map, filter, and combine.

## What are the advantages of flow?

### Clarity and Readability
Flows can make your asynchronous code more concise and readable compared to traditional callbacks.
The use of suspending functions and operators like map, filter, and collect allows you to express complex data 
transformations in a more sequential and understandable way.

### Built-in Backpressure Handling
Flows have built-in mechanisms for handling backpressure, which occurs when the producer of data emits items faster 
than the consumer can process them. Operators like conflate, buffer, and collectLatest help you manage 
backpressure effectively.

### Lifecycle Awareness
When used with lifecycleScope, flows can be automatically cancelled when the lifecycle of an Activity or Fragment 
ends, preventing potential memory leaks and crashes.

### Kotlin Coroutines Integration
Flows are deeply integrated with Kotlin coroutines, allowing you to seamlessly combine them with other coroutine 
concepts like suspending functions, dispatchers, and structured concurrency.

### Testability
Flows are relatively easy to test due to their sequential nature and the ability to control their emission of values.

### Error Handling
Flows provide mechanisms for handling exceptions using operators like catch and retry.

### State Management
Flows can be used effectively for state management, allowing you to represent and observe changes in your 
application's state over time.

## What is the difference between cold flow and hot flow?

| Feature       | Cold Flow                                      | Hot Flow                                                        | 
|---------------|------------------------------------------------|-----------------------------------------------------------------| 
| Activation    | Starts executing on collection (e.g., collect) | Always active and emitting values                               | 
| Execution     | A new instance is created for each collector   | A single instance is shared by all collectors                   | 
| Value Stream  | Independent streams for each collector         | Shared stream for all collectors                                | 
| Analogy       | Vending machine, function call                 | Radio station, live broadcast                                   |
| Android usage | Network requests or database queries           | state management or for observing events like user interactions |
| Example       | flow { ... } builder, flowOf(...)              | StateFlow, SharedFlow                                           |
| Data holding  | Does not hold any data                         | Holds data                                                      |


## How to convert a cold flow into a hot flow?

```
val coldFlow = flow { 
    emit(1)
    emit(2)
    emit(3)
}
```

### shareIn operator

```
val hotFlow = coldFlow.shareIn(
    viewModelScope,
    started = Sharing.Lazily, // Starts sharing when the first subscriber appears
    replay = 1 // Replays the last emitted value to new subscribers
)
```
Use shareIn when you want to multicast the values of a cold flow to multiple collectors and don't need to hold the latest value.

### stateIn operator

```
val hotStateFlow = coldFlow.stateIn(
    viewModelScope,
    started = SharingStarted.Eagerly, // Starts immediately and emits the initial value
    initialValue = 0
)
```
Use stateIn when you need to represent a state that can be observed by multiple collectors, and you want to have access 
to the latest emitted value.

## SharedFlow vs StateFlow?

| Feature           | SharedFlow                                   | StateFlow                            | 
|-------------------|----------------------------------------------|--------------------------------------| 
| Value Retention   | Doesn't hold a value (unless replay is used) | Always holds the latest value        | 
| Replay            | Configurable replay (0 by default)           | Always replays the latest value      | 
| Buffering         | Configurable buffer size                     | No buffering (only the latest value) | 
| Typical Use Cases | Event streams, multicasting                  | State management, observing state    |

## StateFlow vs LiveData.

| Feature             | StateFlow                                                            | LiveData                                                                | 
|---------------------|----------------------------------------------------------------------|-------------------------------------------------------------------------| 
| Library             | Kotlin Coroutines                                                    | Android Architecture Components                                         | 
| Type                | Hot flow                                                             | Observable data holder                                                  | 
| Lifecycle Awareness | Not inherently lifecycle-aware (but can be used with lifecycleScope) | Inherently lifecycle-aware                                              | 
| Value Holding       | Always holds the latest value (Cannot be null, we can use wrapper)   | Can hold nullable values                                                | 
| Collection          | Collected using collect                                              | Observed using observe                                                  |
| Emitting            | Emits values even when there are no active observers                 | Only emits updates to active observers in the STARTED or RESUMED state. |
| Initial value       | required                                                             | optional                                                                |

## What is back pressure handling?
Backpressure handling involves mechanisms like buffering or dropping values to manage situations where the 
producer emits values faster than the consumer can process them.

### buffer(capacity)

```
val flow = flow {
    emit(1)
    delay(100) // Simulate slow emission
    emit(2)
}.buffer(capacity = 5) // Buffer up to 5 items
```
### conflate() 
Buffers only the latest emitted item, dropping intermediate values.
### onBufferOverflow
Allows you to specify a strategy for handling buffer overflows 
(e.g., BufferOverflow.SUSPEND, BufferOverflow.DROP_OLDEST, BufferOverflow.DROP_LATEST)

## How to drop every third emitted value in a flow?

```
fun <T> Flow<T>.dropEveryThird(): Flow<T> = flow {
    var count = 0
    collect { value ->
        count++
        if (count % 3 != 0) { 
            emit(value)
        }
    }
}
```

## Is setting value to a StateFlow thread safe?
Yes, You don't need to worry about synchronization or race conditions when modifying the value of a MutableStateFlow.
If you collect a StateFlow in a coroutine that's running on a background thread, the updates will be received on that 
background thread. If you need to update the UI from a StateFlow, make sure to collect it on the main thread.

## What is a channelFlow?

### Channels
Channels are communication primitives in coroutines that allow you to send and receive values between coroutines. 
They can be buffered or unbuffered, and they support different sending and receiving policies.
### Flows
Flows are a declarative way to represent asynchronous data streams. They provide a rich set of operators for 
transforming and manipulating data.
### ChannelFlow
Combines the strengths of both channels and flows. You can use channel operations (like send and receive) to emit 
values into the flow, and you can use flow operators to transform and collect the values. 

## Why use ChannelFlow?

### Fine-grained control
You have precise control over when and how values are emitted into the flow.
### Backpressure handling
You can leverage channel backpressure mechanisms to manage the flow of data.
### Integration with channels
You can seamlessly integrate ChannelFlow with other coroutines that use channels for communication.

```
// ChannelFlow for sensor data
val sensorFlow = channelFlow {
    // Collect sensor data and send it to the flow
}

// ChannelFlow for network events
val networkFlow = channelFlow {
    // Receive network events and send them to the flow
}

// Combine flows and apply transformations
val combinedFlow = sensorFlow.combine(networkFlow) { sensorData, networkEvent ->
    // Combine data from both sources
}

// Collect the combined flow on the main thread
lifecycleScope.launch(Dispatchers.Main) {
    combinedFlow.collect { combinedData ->
        // Update UI with combined data
    }
}
```