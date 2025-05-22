# Section 1\:Variables loops and functions

---

## Topics Covered in Section 1:

* Variables & Constants (with all variations)
* Data Types & Literals
* Operators
* Control Flow (if, switch, for loops)
* Functions (normal, variadic, anonymous, closures)
* Error handling (basic and advanced)
* `defer`, `panic`, and `recover`
* JS analogies for each part

---

## 1. Variables and Constants

### Variable Declarations in Go

**Variation 1: Explicit type**

```js
var name string = "Covlant" // variable with specified type
```

**Variation 2: Implicit type (type inferred)**

```js
var age = 25 // Go automatically infers type as int
```

**Variation 3: Short-hand declaration (inside functions only)**

```js
city := "Guwahati" // Shortest way to declare and assign
```

**Variation 4: Multi-variable block**

```js
var (
    country = "India"
    zipcode = 560001
)
```

> **JavaScript Comparison:**

* Go's `var` is like `let` in JS
* Go’s `const` is like `const` in JS

---

## 2. Constants

```js
const pi = 3.1415
```

**Multi-constant declaration:**

```js
const (
    AppName = "GoApp"
    Version = 1.0
)
```

> Constants are immutable values known at compile-time.

---

## 3. Data Types & Literals

**Primitive Types:**

* int, float64, string, bool, byte, rune

**Literals Examples:**

```js
42, 3.14, true, "hello", 'A', 0xFF, 0b1010
```

> **Literals** are fixed hard-coded values in your program.

**Composite Literals:**

```js
nums := []int{1, 2, 3} // Slice literal
```

> 🔁 **JS Analogy:**

```js
const nums = [1, 2, 3];
```

---

## 4. Operators

**Arithmetic:** `+ - * / %`
**Comparison:** `== != < <= > >=`
**Logical:** `&& || !`
**Assignment:** `= += -= *= /=`
**Bitwise:** `& | ^ << >>`

```js
x := 10
x += 5 // now x = 15
```

---

## 5. Control Flow

### If-Else

```js
age := 18
if age >= 18 {
    fmt.Println("Adult")
} else { 
    fmt.Println("Minor")
}

#points to remember
1. Always remeber in Go there should be no parenthesis() in if 
2. else statement should always be written just beside the closing } of if 
```

**With short statement:**

```js
//In go you can even assign and declare a variable in if statement itself
if score := 90; score > 80 {
    fmt.Println("Excellent")
}
```

### Switch

```js
day := "Monday"
switch day {
case "Monday":
    fmt.Println("Start work")
case "Saturday", "Sunday":
    fmt.Println("Rest day")
default:
    fmt.Println("Mid-week")
}
```

**Expression-less switch:**

In Go you can skip if else chain and write it simply with switch-cases like this :-

```js
score := 85
switch {
case score > 90:
    fmt.Println("A+")
case score > 80:
    fmt.Println("A")
default:
    fmt.Println("B")
}
```

### Loops (Only `for` loop is in Go)

**Variation 1: Classic for loop**

Remeber here in For loop too we dont have to give the parenthesis
The conditions of For loop should not be inside any parenthesis

```js
for i := 0; i < 5; i++ {
    fmt.Println(i)
}
```

**Variation 2: While-style loop**

```js
i := 0
for i < 5 {
    fmt.Println(i)
    i++
}
```

**Variation 3: Infinite loop**

```js
for {
    fmt.Println("Forever running")
}
```

**Variation 4: Range loop**

```js
langs := []string{"Go", "Python", "JS"}
for index, lang := range langs {
    fmt.Println(index, lang) // index is 0-based, lang is value
}
```

> **JS Analogy:**

```js
langs.forEach((lang, index) => {
  console.log(index, lang);
});
```

---

## 6. Functions (All Variations)

Functions in Go are first-class citizens: they encapsulate reusable logic, improve modularity, and enable abstraction.

**How to Declare and Use Functions?**

* **Declaration:** `func` keyword, function name, parameter list, optional return types, and function body.
* **Zero-value arguments:** You must supply values matching the parameter types; Go does not allow default parameter values.

### 6.1 Basic Function

```js
// greet prints a greeting message for the given name.
// Parameters:
//    name string: the name of the person to greet.
// Returns: none.
func greet(name string) {
    fmt.Println("Hello", name)
}

// Usage:
greet("Prahlad") // Output: Hello Prahlad
```

**Usage Scenario:**
Use basic functions for simple tasks such as logging, formatting output, or grouping statements.

**JS Equivalent:**

```js
function greet(name) {
  console.log("Hello", name);
}
```

### 6.2 Function with Return Value

```js
// add returns the sum of two integers.
// Parameters:
//    a, b int: numbers to add.
// Returns:
//    int: the sum of a and b.
func add(a int, b int) int {
    return a + b
}

// Usage:
result := add(3, 5) // result == 8
```

**Why & When:**

* Use return values to pass results back to the caller.
* Common in math operations, data transformations, and any logic that computes a value.

**JS Equivalent:**

```js
function add(a, b) {
  return a + b;
}
```

### 6.3 Multiple Return Values

```js
// divide divides a by b and returns both the quotient and an error if b is zero.
// Parameters:
//    a, b int: dividend and divisor.
// Returns:
//    int: result of division.
//    error: non-nil if b is zero.
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("cannot divide by zero")
    }
    return a / b, nil
}

// Usage:
quotient, err := divide(10, 2)
if err != nil {
    fmt.Println("Error:", err)
} else {
    fmt.Println("Quotient:", quotient)
}
```

**Why & How:**

* Go’s ability to return multiple values simplifies error handling without exceptions.
* The caller must handle both results, making error conditions explicit.

**Visual Example:**

```plaintext
┌───┬───┐         ┌─────────────┐
│ a │ b │ --call--> divide()   │
└───┴───┘           └─────────┬─┘
                                │ returns (result, err)
            if b==0 ──> err
                              ✔ else result
```

### 6.4 Named Return Values

```js
// calc returns the sum and difference of a and b using named returns.
// Named return values act like local variables initialized to zero values.
func calc(a, b int) (sum int, diff int) {
    sum = a + b
    diff = a - b
    return // returns sum and diff
}

// Usage:
s, d := calc(7, 3) // s==10, d==4
```

**Benefits:**

* Improves readability by naming outputs.
* Avoids repetition in return statements.
* Use sparingly to prevent confusion in longer functions.

### 6.5 Variadic Functions

```js
// sum computes the total of an arbitrary number of integers.
// nums ...int: accepts zero or more int arguments.
// Under the hood, nums is a slice of ints.
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}

// Usage:
sum()         // 0
sum(1, 2, 3)  // 6
values := []int{4, 5, 6}
sum(values...) // unpack slice, result 15
```

**When & How:**

* Use when the number of arguments varies.
* Ideal for utility functions (e.g., printing, aggregations).

**JS Equivalent:**

```js
function sum(...nums) {
  return nums.reduce((acc, n) => acc + n, 0);
}
```

### 6.6 Anonymous Functions

```js
// Assign an anonymous function to a variable.
// Anonymous functions have no name and can capture surrounding variables.
double := func(x int) int {
    return x * 2
}
fmt.Println(double(5)) // Output: 10

// Immediately invoked function:
fmt.Println(func(x, y int) int {
    return x + y
}(3, 4)) // Output: 7
```

**Use Cases:**

* Short-lived functions passed to higher-order functions (e.g., map, filter).
* Closures that capture and maintain state.

**Visual Diagram:**

```plaintext
[double variable] ---> [func(x int) int { ... }] ---> execution
```


Anonymous functions (also known as function literals) are functions defined without a name. In Go, they are first-class citizens, which means they can be assigned to variables, passed as arguments, and returned from other functions.



# Lets deep dive into some use cases of Anonymous functions in GO


Anonymous functions are unnamed function literals that you can declare inline. They're powerful for one-off logic, closures, and dynamic behavior.

**What?**
A function literal without a name, assigned to a variable or passed directly as an argument.

**Why?**

* **Conciseness:** No need to create a separate named function for small, localized logic.
* **Encapsulation:** Limits scope; the function exists only where it’s needed.
* **Dynamic behavior:** Can capture surrounding variables (closures) and adapt behavior at runtime.

**When & How?**

* Use when you need a short-lived, specific operation.
* Common in callbacks, goroutines, tests/mocks, and closures that maintain state.

---

#### 6.6.1 Callbacks & Higher-Order Functions

```js
// apply accepts a function and a value, then invokes the callback.
func apply(callback func(int) int, value int) int {
    return callback(value)
}

func main() {
    // Anonymous function passed directly as callback:
    result := apply(func(x int) int {
        return x * x
    }, 5)
    fmt.Println("Square:", result) // Square: 25
}
```

**Execution Flow:**

```
main()
 ├─> apply(callbackLiteral, 5)
 │     ├─ callbackLiteral(5) executes anonymous body x*x
 │     └─ return 25
 └─ fmt.Println prints "Square: 25"
```

**Visual Diagram:**

```
[main]--passes-->[apply]
                 callbackLiteral value=5
                 │
                 ↓
           execute x*x => 25
                 ↓
             return 25
```

**Use Cases:**

* `map`, `filter`, `reduce` patterns.
* Event handlers, UI callbacks, dynamic pipelines.

---

#### 6.6.2 Concurrency with Goroutines

```js
func main() {
    // Inline goroutine
    go func(name string) {
        fmt.Println("Hello from goroutine,", name)
    }("Prahlad")

    fmt.Println("Main function executing...")
    time.Sleep(time.Second) // allow goroutine to finish
}
```

**Execution Flow:**

```
main() starts
 ├─ launch goroutine with argument "Prahlad"
 │     └─ runs concurrently:
 │         fmt.Println("Hello from goroutine, Prahlad")
 └─ fmt.Println("Main function executing...")
 └─ Sleep 1s to wait for goroutine
```

**Visual Diagram:**

```
[main goroutine]          [anonymous goroutine]
     │                            │
     │ go func(...)              │
     │──────────────────────────→ │
     │ fmt.Println(main text)     │
     │                            │ prints goroutine text
     │ time.Sleep                │
```

**Use Cases:**

* Fire-and-forget tasks, background workers, parallel pipelines.

---

#### 6.6.3 Testing & Mocking

```js
func process(fn func(string) string, input string) string {
    return fn(input)
}

func main() {
    // Mock logic inline:
    mock := func(s string) string {
        return "Mocked: " + s
    }
    fmt.Println(process(mock, "Data")) // Mocked: Data
}
```

**Execution Flow:**

```
main()
 ├─ define mock literal
 ├─ call process(mock, "Data")
 │     └─ invoke fn("Data") => "Mocked: Data"
 └─ fmt.Println prints result
```

**Use Cases:**

* Stub external dependencies (HTTP, DB) in unit tests.
* Isolate behavior without polluting global test scope.

---

#### 6.6.4 Closures: Capturing Outer Variables

```js
func counter() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}

func main() {
    next := counter()
    fmt.Println(next()) // 1
    fmt.Println(next()) // 2
}
```

**Execution Flow with Memory:**

```
counter() called:
 └─ allocate count=0
 └─ return inner literal capturing count

next() invoked first time:
 ├─ count=0 → count++ → count=1
 └─ return 1

next() invoked second time:
 ├─ count=1 → count++ → count=2
 └─ return 2
```

**Visual Diagram:**

```
+-------------+
| counter env |
| count = 0   |
+------+------+      +---------------+
       │             │ closure literal │
       └─ returns ──▶│ func() int     │
                     └──────┬──────────┘
                            │ capture count
                            ↓
                         invoke
                            │
                            ↓ update and return
```

**Why & When:**

* Maintain state between calls.
* Encapsulate counters, caches, or configuration in self-contained functions.

---

> Anonymous functions provide incredible flexibility—inline logic, scoped state, and effortless concurrency. Use them to write cleaner, more expressive Go code.
