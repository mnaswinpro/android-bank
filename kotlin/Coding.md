# Coding

## How to sort collections of Data object with a property

```
    intervals.sortWith(compareBy { it.id })
```

## How to iterate next element together with current element

```
    val list = listOf(1, 2, 3, 4, 5)

    list.zipWithNext().forEach { (current, next) ->
        println("Current: $current, Next: $next")
    }
```