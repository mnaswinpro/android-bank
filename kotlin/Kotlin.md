# Kotlin

## What are the advantages of Kotlin over Java?

- Kotlin reduces boilerplate code leading to cleaner readable code
- Explicitly declare nullability for a value
- Support for coroutines
- Extension functions
- Sealed Class, Data Class
- Modern features like lambda expressions, higher-order functions, and operator overloading
- String templates - "My name is $firstName $lastName"
- Interoperability - allowing you to use existing Java libraries and frameworks in kotlin and vice versa

## What is object class?
An object declaration creates a singleton class. This means that there's only one instance of this class 
throughout the entire application.

Single Instance
No Constructor
Lazily Initialized
Can Have Members(Properties and methods)

## lateinit vs lazy.

| Feature                | lateinit                                                                                                | lazy                                                                     | 
|------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------| 
| Type of Properties     | Works only with non-nullable properties (var)                                                           | Works with both nullable (var) and non-nullable (val) properties         | 
| Initialization         | Initialization is done manually later in the code                                                       | Initialization is done automatically on the first access                 | 
| Thread Safety          | Not thread-safe by default                                                                              | Thread-safe for val properties (initialized only once)                   | 
| Type of Initialization | Can be initialized multiple times                                                                       | Initialized only once                                                    | 
| Use Cases              | Properties that are guaranteed to be initialized before use (e.g., dependency injection, Android views) | Properties that might not be used always and are expensive to initialize | 
| Error Handling         | Throws UninitializedPropertyAccessException if accessed before initialization                           | No exception, initialization is triggered on access                      |
| Primitives             | Not allowed as primitives are already non nullable                                                      | allowed                                                                  |

## Why lateinit do not support primitives?
lateinit is designed for non-nullable properties. Primitive types in Kotlin are non-nullable by default, 
and the compiler can ensure their initialization at compile time.

## What is a lambda expression in kotlin?
A function that is not declared but passed immediately as an expression. It's a concise way to define anonymous 
functions, which are functions without a name.

Provides a shorter way to define anonymous functions.
Can be passed as arguments to higher-order functions.

Lambda function declaration (Int, Int) -> Unit
{ variable -> body_of_function}

## What are higher order functions in kotlin? Why do we need it?
Functions that can take other functions as arguments or return functions as results.
Promotes code reusability and flexibility.

```
fun operation(op: String): (Int, Int) -> Int {
    when (op) {
        "add" -> return { a, b -> a + b }
        "subtract" -> return { a, b -> a - b }
        else -> return { a, b -> a * b }
    }
}

val add = operation("add")
println(add(5, 3)) // Output: 8
```

## What are method references?
Method references provide a way to pass a function or a property as an argument to another function in a more concise 
and readable manner.

Function reference
```
fun isEven(number: Int): Boolean {
    return number % 2 == 0
}

val numbers = listOf(1, 2, 3, 4, 5)
val evens = numbers.filter(::isEven)
```

Property reference
```
data class Person(val name: String, val age: Int)

val people = listOf(Person("Alice", 25), Person("Bob", 30))
val names = people.map(Person::name)
```

## What are all the scope functions available in Kotlin?

Scope functions that allow you to execute a block of code within the context of an object.
Improves code readability and conciseness.

### let
Useful for performing operations on an object and returning a different value.
Takes the object as a lambda argument (it)
### run
Useful for performing operations on an object without returning a specific value.
The object is available as this within the lambda
### with
Useful for performing multiple operations on an object without returning a specific value.
Takes the object as an argument and makes it available as this within the lambda.
### apply
Useful for performing multiple operations on an object and returns the object itself.
### also
Useful for performing additional operations on an object without modifying its state and returning it.


## What are extension functions? How does it work?
Allows us to add new functionality to existing classes without inheriting from them or modifying their source code.
When you call an extension function on an object, the compiler translates the function call into a static function
call passing the object as the first argument.

## What are inline functions?
Reduce the overhead of function calls by replacing the function call with the function's body at compile time.
When you mark a function with the inline keyword, the compiler replaces each call to that function with the 
function's actual code. This eliminates the overhead of creating a new stack frame and jumping to the function's code.

```
inline fun calculate(a: Int, b: Int): Int {
    return a + b
}

val result = calculate(5, 3)
```

## When to use inline functions?
Functions with small bodies that are called frequently.
Functions that take lambda expressions as arguments.
Functions that need to use reified type parameters.

Function with small body that is called frequently.
```
inline fun View.setVisibility() {
    visibility = if (visibility == View.VISIBLE) View.GONE else View.VISIBLE
}
```

## What is noinline?
Used in inline functions to control the behavior of lambda arguments passed to the inline functions.
Prevents a lambda argument from being inlined due to potential performance issues or complexities.

```
inline fun performOperation(noinline action: () -> Unit) {
    // ...
    action()
    // ...
}
```

## What is crossinline?
Used in inline functions to control the behavior of lambda arguments passed to the inline functions.
Restricts a lambda argument from using non-local returns.

```
inline fun processValues(values: List<Int>, crossinline action: (Int) -> Unit) {
    values.forEach {
        action(it) // Non-local return not allowed here
    }
}
```
"action" lambda cannot use a return statement to exit the processValues function.

## What are infix functions?
Infix function allows you to call a function with a special syntax that looks like an operator.
We can omit the dot and parentheses when calling the function on the target object.

Must be member functions or extension functions.
Must have a single parameter.
The parameter must not accept variable number of arguments and must have no default value.

```
infix fun Int.add(other: Int): Int {
    return this + other
}

val sum = 2 add 3 // Equivalent to 2.add(3)
```

## What are Sealed classes? 
Sealed classes in Kotlin are a special kind of class that restrict the hierarchy of a class. A sealed class can 
have subclasses, but all of those subclasses must be declared in the same file as the sealed class.

### Restricted Hierarchy 
The compiler knows all possible subclasses at compile time. This allows for exhaustive checks when using when 
expressions, eliminating the need for an else branch in many cases.

### Abstract by Default
Sealed classes are abstract and cannot be instantiated directly.

### Subclasses Can Be Anything
Subclasses of a sealed class can be data classes, object classes, regular classes, or even sealed classes themselves.

## What is a data class?
Data classes are designed to hold data. The compiler automatically generates several helpful methods like 
equals(), hashCode(), toString(), and copy() for data classes.

## Is data class and sealed class thread safe?

If class hold immutable data, they are inherently thread-safe as their state cannot be
modified after creation otherwise to handle thread safety we may need to use synchronization, locks,
or thread-safe data structures(ConcurrentHashMap or CopyOnWriteArrayList).

## Can a data class be extended by another class?
No, data classes in Kotlin cannot be extended by other classes. They are implicitly declared as final, which means 
they cannot be inherited from.

Data classes are primarily designed to hold data and encourage immutability. Inheritance can introduce complexities 
and potentially violate the immutability principle.
Data classes have automatically generated methods like equals(), hashCode(), and toString(). Inheritance could 
lead to inconsistencies in these methods if subclasses modify the data structure.
Making data classes final ensures their behavior is predictable and prevents unexpected modifications from subclasses.

## open vs final vs abstract keyword in kotlin.

### open
Allows a class or member to be inherited or overridden in subclasses. It explicitly marks a class or member as extensible.
When you want to create a base class or member that can be customized or specialized by subclasses.

### final
Prevents a class or member from being inherited or overridden. This is the default behavior for classes and members in Kotlin.
When you want to ensure that a class or member cannot be modified or extended by subclasses, providing a guarantee of its behavior.

### abstract
Declares a class or member that must be overridden in subclasses. An abstract class cannot be instantiated directly, 
and an abstract member has no implementation in the base class.
When you want to define a common interface or structure for a group of related classes but leave the specific 
implementation details to the subclasses.

## What is delegation?
Delegation is a design pattern where an object delegates certain responsibilities or tasks to another object. 
This promotes code reuse, reduces boilerplate, and improves flexibility.

When working with custom delegation, a class is created with a getValue and setValue for a property.

```
class NotNullSingleValueVar<T> {
    private var value: T? = null

    operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
        return value ?: throw IllegalStateException("${property.name} not initialized")
    }

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
        this.value = if (this.value == null) value
        else throw IllegalStateException("${property.name} already initialized")
    }
}
```

## What are channels in kotlin?

## When to use internal class in kotlin?

## what is final keyword?

## What is typealias keyword?
To provide alternative names for existing types. This can improve code readability and make it more concise, 
especially when dealing with long or complex type names.
They help with refactoring by providing a level of abstraction.

```
typealias Email = String
typealias UserID = Int
typealias Coordinate = Pair<Double, Double>
typealias ProductResult = Result<List<Product>>
```


## What is reified keyword?

## What is volatile keyword?

## How to break forEach loop?

## How to execute thread safe operations in kotlin?

## What are locks? What are the different types of locks used in kotlin?

## What is !! in kotlin?

## What are suspend functions?
Functions marked with the suspend keyword. These functions can be paused and resumed later without blocking the thread.

## What is operator overloading?

Operator overloading allows you to define how existing operators, like +, -, *, /, >, <, ==, etc., behave with 
your custom classes.

```
data class Point(val x:Int, val y: Int)

operator fun Point.plus(other: Point): Point {
  return Point(x + other.x, y + other.y)
}

val point1 = Point(1, 2)
val point2 = Point(3, 4)
val sum = point1 + point2
```

## How to use labels to break or continue in loop?

```
fun breakOuterLoop() {
    outerLoop@ for (i in 1..5) {
        for (j in 1..5) {
            if (i * j == 15) {
                println("Breaking outer loop at i = $i, j = $j")
                break@outerLoop // Exit the outer loop
            }
            println("i= $i, j = $j")
        }
    }
}
```

```
fun skipInnerLoopIteration() {
    for (i in 1..5) {
        innerLoop@ for (j in 1..5) {
            if (j == 3) {
                println("Skipping inner loop iteration at j = $j")
                continue@innerLoop // Skip to the next iteration of the inner loop
            }
            println("i = $i, j = $j")
        }
    }
}
```