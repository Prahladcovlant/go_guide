## 7. Error Handling

Error handling in Go is **explicit** and **structured**. Unlike languages like JavaScript or Python which use `try-catch`, Go follows a pattern where errors are treated as values and returned alongside normal return values.

Go encourages developers to **handle every possible error** — this makes code more predictable, robust, and production-safe.

---

###  Why Error Handling is Crucial in Go

* Go **doesn't use exceptions** for runtime errors (except for critical failures like `panic`)
* Functions that may fail typically return an `error` type as the **last return value**
* You must explicitly check if `error != nil` and respond accordingly

---

### Basic Error Handling Pattern

```js
result, err := divide(10, 0)
if err != nil {
    fmt.Println("Error:", err)
} else {
    fmt.Println("Result:", result)
}
```

Here, `divide()` returns an `int` and an `error`. If `b == 0`, we return a custom error.

---

###  Creating and Returning Errors

#### 1. **Using `errors.New()`**

```js
import "errors"

func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("Cannot divide by zero")
    }
    return a / b, nil
}
```

#### 2. **Using `fmt.Errorf()` with formatting**

```js
if b == 0 {
    return 0, fmt.Errorf("Invalid divisor: %d", b)
}
```

>  `nil` is returned when there is **no error**

---

###  Custom Error Types

You can define your own error types by implementing the `Error()` method:

```js
type CustomError struct {
    Msg string
}

func (e *CustomError) Error() string {
    return e.Msg
}

func risky() error {
    return &CustomError{"Something went wrong in a custom way"}
}
```

This is helpful when you want to **differentiate error types** using type assertion:

```js
err := risky()
if e, ok := err.(*CustomError); ok {
    fmt.Println("Caught custom error:", e.Msg)
}
```

---

###  Common Mistake: Ignoring Errors

```js
val, _ := riskyDivide(10, 0) //  Ignoring the error completely
```

>  Never ignore errors in production code unless you're **100% sure** it's harmless

---

###  Best Practices for Error Handling in Go

| Principle          | Practice                                              |
| ------------------ | ----------------------------------------------------- |
| Always check `err` | Don’t skip error checks                               |
| Be descriptive     | Return meaningful messages                            |
| Wrap errors        | Use `fmt.Errorf` or third-party libraries for context |
| Custom types       | For complex systems, define your own error structs    |

---

###   Error Wrapping with `%w` (Go 1.13+)

```js
return fmt.Errorf("processing failed: %w", err)
```

You can later check with:

```js
if errors.Is(err, originalErr) {
    // matched
}
```

Or extract:

```js
var custom *CustomError
if errors.As(err, &custom) {
    fmt.Println("Custom error found")
}
```
---
