# Data Transformation

## Examples of data transformation operators supported in flow.

### filter

Emits only the values that meet a given condition.

```
flowOf(1, 2, 3, 4, 5)
         .filter { it %2 == 0 } // Emit only even numbers
         .collect { println(it) } // Output: 2, 4
```

### transform

The transform operator allows you to perform arbitrary transformations on the emitted values of a flow.
Unlike map, which transforms each value into a single new value

```
flowOf(1, 2, 3, 4)
    .transform { value ->
        emit(value) // Emit the original value
        if (value % 2 == 0) {
            emit(value * value) // Emit the square if even
        } else {
            println("Odd number: $value") // Log a message if odd
        }
    }
    .collect { println(it) }
```

### map

Transforms each emitted value into a new value using a provided transformation function.

```
flowOf("a", "b", "c")
         .map { it.uppercase() }
         .collect { println(it) } // Output: A, B, C
```

### zip

Combines values from two flows pairwise. Emits a new value whenever both flows have emitted a value at the corresponding index.

```
val flow1 = flowOf(1, 2, 3)
     val flow2 = flowOf("a", "b", "c")
     flow1.zip(flow2) { num, letter -> "$num$letter" }
         .collect { println(it) } // Output: 1a, 2b, 3c
```

### combine

Combines values from two flows. Emits a new value whenever either flow emits a value, using the latest value from the other flow.

```
val flow1 = flowOf(1, 2, 3)
     val flow2 = flowOf("a", "b", "c")
     flow1.combine(flow2) { num, letter -> "$num$letter" }
         .collect { println(it) } // Output: 1a, 2a, 2b, 3b, 3c
```

### flatMap

Transforms each emitted value into a new flow and flattens the emissions from these inner flows into a single flow.

```
flowOf(1, 2, 3)
         .flatMap { value -> flow { emit(value); emit(value * 2) } }
         .collect { println(it) } // Output: 1, 2, 2, 4, 3, 6
```

### flatMapConcat

Similar to flatMap, but concatenates the emissions from inner flows sequentially, ensuring that the order of
emissions from the original flow is preserved.

```
flowOf(1, 2, 3)
         .flatMapConcat { value -> flow { emit(value); emit(value * 2) } }
         .collect { println(it) } // Output: 1, 2, 2, 4, 3, 6 (order is maintained)
```

### reduce

Accumulates the values in the flow using a provided operation and emits the final accumulated value.

```
flowOf(1, 2, 3, 4)
         .reduce { accumulator, value -> accumulator + value }
         .collect { println(it) } // Output: 10
```

### fold

Similar to reduce, but starts with an initial value and accumulates values using a provided operation. Emits the final
accumulated value.

```
flowOf(1, 2, 3, 4)
         .fold(0) { accumulator, value -> accumulator + value }
         .collect { println(it) } // Output: 10
```

### catch

Handles exceptions thrown in the upstream flow. You can emit a value, rethrow the exception, or perform other actions.

```
flow { 
         emit(1)
         throw Exception("Error!") 
     }
         .catch { e -> emit(-1) } 
         .collect { println(it) } // Output: 1, -1
```

### onEach

Performs an action for each emitted value without modifying the value itself.

```
flowOf(1, 2, 3)
         .onEach { println("Emitting: $it") }
         .collect { println(it) } // Output: Emitting: 1, 1, Emitting: 2, 2, ...
```
### buffer

Buffers emitted values before sending them to the collector. Can be used to handle backpressure or group values.

```
flow { emit(1); delay(50); emit(2); emit(3) }
         .buffer(capacity = 2) // Buffer up to 2 items
         .collect { println(it) } // Output: [1, 2], [3]
```

### conflate

Drops emitted values if the collector is not ready to receive them, keeping only the latest value.
Useful for situations where only the most recent value is important.

```
flow { emit(1); delay(50); emit(2); emit(3) }
         .conflate() 
         .collect { println(it) } // Output: 1, 3 (2 might be dropped)
```

### debounce

Emits a value only after a specified period of inactivity (no new values emitted). Useful for filtering out rapid emissions.

Behavior:
When a value is emitted, debounce starts a timer.
If a new value is emitted within the timer duration, the timer is reset.
If the timer expires without any new values, the last emitted value is emitted downstream.
Use Cases:
Filtering out bursts of emissions (e.g., handling rapid user input, network requests).
Ensuring that only the final value in a sequence is processed.

```
flow { emit(1); delay(100); emit(2); delay(50); emit(3) }
         .debounce(150) // Wait 150ms after last emission
         .collect { println(it) } // Output: 1, 3 (2 is dropped)
```

### throttleFirst

Emits the first value and then ignores subsequent values for a specified duration. Useful for limiting the rate of emissions.

Behavior:
When a value is emitted, throttleFirst emits it immediately.
It then starts a timer and ignores any new values emitted within the timer duration.
Once the timer expires, it's ready to emit the next value.
Use Cases:
Limiting the rate of actions (e.g., preventing multiple button clicks, API calls).
Ensuring a minimum delay between emissions.

```
flow { emit(1); delay(100); emit(2); delay(50); emit(3); delay(200); emit(4) }
         .throttleFirst(200) // Emit at most once every 200ms
         .collect { println(it) } // Output: 1, 4
```