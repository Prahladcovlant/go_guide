## 8. Defer, Panic, Recover

Go provides powerful tools for handling **unexpected events and cleanup operations** using three keywords:

* `defer`
* `panic`
* `recover`

They work together to simulate something similar to try-catch-finally, but in Go's idiomatic way.

---

###  What is `defer`?

`defer` is used to delay the execution of a function until the **surrounding function returns**.
It is commonly used for **resource cleanup**, like closing files or network connections.

```js
func show() {
    defer fmt.Println("Last") // Deferred function runs at the end
    fmt.Println("First")
}
```

###  Execution Flow:

```
Output:
First
Last
```

* `defer` statements are pushed onto a stack
* They are executed in **Last-In-First-Out (LIFO)** order

---

### What is `panic`?

`panic()` is used to **stop normal execution** of the program. It is like throwing an exception.

```js
func crash() {
    panic("Something went wrong!")
}
```

When a panic occurs:

* The program starts **unwinding the call stack**
* Any deferred functions are still executed
* If not recovered, the program **crashes** with a stack trace

---

###  What is `recover()`?

`recover()` is used to **regain control** of a panicking goroutine. It only works **inside a deferred function**.

```js
func safeOperation() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from:", r)
        }
    }()

    panic("Some failure")
}
```

If `recover()` is called:

* It **stops the panic**
* Program continues executing **after the deferred block**

If not recovered:

* Program crashes

---

### Combined Visual Example:

```js
func riskyOperation() {
    defer fmt.Println("Cleaning up...")
    panic("Something went wrong!")
}

func main() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from:", r)
        }
    }()

    riskyOperation()
    fmt.Println("After recovery")
}
```

### Output:

```
Cleaning up...
Recovered from: Something went wrong!
After recovery
```

---

### Why Use These?

| Keyword   | Use For                                   |
| --------- | ----------------------------------------- |
| `defer`   | Cleanups: closing files, unlock mutexes   |
| `panic`   | Critical errors you can't handle safely   |
| `recover` | Gracefully catching & resuming from panic |

---

###  Best Practices

* Use `panic` **rarely** — only for truly unrecoverable errors (e.g. corrupt memory, broken invariants)
* Never use `panic` for normal error handling
* Use `recover` to keep your app from crashing, especially in **goroutines, servers, or libraries**
* Always use `defer` to **clean up**, even if a panic happens

---

### Analogy (JS-style):

```js
try {
  throw new Error("Something went wrong!");
} catch (e) {
  console.log("Recovered:", e);
} finally {
  console.log("Cleaning up...");
}
```

Go version uses `panic`, `recover`, and `defer` to achieve the same outcome.

---