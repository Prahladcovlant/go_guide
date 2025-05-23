

## 1. Introduction

* Place tests in files ending with `_test.go` alongside the code under test. Go’s toolchain discovers these files automatically.

### Jest Analogy

```js
// Jest: basic test
test('sum of 2+3 is 5', () => {
  expect(add(2, 3)).toBe(5);
});
```

In Go, the equivalent is:

```js
func TestAdd(t *testing.T) {
    if Add(2, 3) != 5 {
        t.Errorf("Expected 5, got %d", Add(2, 3))
    }
}
```

---

## 2. Phase 1: Basic Unit Testing

### 2.1 What?

Testing individual, pure functions or methods in isolation, without external dependencies.

### 2.2 Why?

* To verify core logic correctness.
* To quickly detect errors in edge cases.

### 2.3 When?

* Immediately after writing each function.
* Always for bug fixes to lock in correct behavior.

### 2.4 How?

1. **File Naming:** For `foo.go`, create `foo_test.go`. Go’s test runner only picks up `_test.go` files.
2. **Package Declaration:** Use the same package (or `<pkg>_test` for black-box tests) at the top of your test file.
3. **Test Function Signature:** Must be `func TestXxx(t *testing.T)` where `Xxx` is any alphanumeric identifier starting with an uppercase letter.
4. **Using `t *testing.T`:** Provides methods:

   * `t.Error` / `t.Errorf`: Logs error, continues execution.
   * `t.Fatal` / `t.Fatalf`: Logs error and stops the test immediately.
   * `t.Skip`: Skips a test with a message.

#### Detailed Line-by-Line Example

```js
// math/add.go
package math

// Add returns the sum of two ints.
func Add(a, b int) int {
    return a + b
}
```

```js
// math/add_test.go
package math

import (
    "testing" // Go’s built-in testing support
)

// TestAdd verifies Add behaves correctly for basic inputs.
func TestAdd(t *testing.T) {
    // Arrange: setup input values and expected result
    a, b := 2, 3                // inputs for the function
    want := 5                   // expected result

    // Act: invoke the function under test
    got := Add(a, b)            // actual result from Add

    // Assert: compare actual vs expected
    if got != want {            // if mismatch
        // t.Errorf logs the formatted error and marks test as failed
        t.Errorf("Add(%d, %d) = %d; want %d", a, b, got, want)
    }
}
```

* **Why `if got != want`:** Go does not have built-in matchers; manual comparison is standard.
* **Why `t.Errorf`:** Allows multiple asserts in one test; test continues after failure.

### Running Tests

```bash
# Run all tests in current directory
go test
# Verbose: show each test name and output
go test -v
# Run tests for a specific package
go test ./math
```

---

## 3. Phase 2: Assertions with Testify

### 3.1 What?

`testify` is a popular library that provides assertion functions (`assert`, `require`) to simplify test code and improve readability.

### 3.2 Why?

* Reduces boilerplate of manual `if` checks.
* Provides clear failure messages and intuitive API.
* `require` variants stop execution on failure (hard fail), similar to Jest’s `expect(fn).toThrow()` behavior.

### 3.3 When?

* When tests involve multiple assertions or complex comparisons.

### 3.4 How?

1. **Installation:**

   ```
   go get github.com/stretchr/testify
   ```



````
2. **Import:**

import (
    "testing"
    "github.com/stretchr/testify/assert"
)
````

3. **Usage:** Replace manual checks.

   * `assert.Equal(t, expected, actual, msg…)`
   * `assert.True(t, condition, msg…)`
   * `assert.Nil(t, err)` etc.

#### Jest → Testify Comparison

| Jest Assertion                 | Testify Assertion       |
| ------------------------------ | ----------------------- |
| `expect(x).toBe(y)`            | `assert.Equal(t, y, x)` |
| `expect(x).toBeTruthy()`       | `assert.True(t, x)`     |
| `expect(() => fn()).toThrow()` | `assert.Panics(t, fn)`  |

#### Example with Detailed Notes

```js
func TestAdd_WithAssert(t *testing.T) {
    // Act & Assert in one line for brevity
    // Jest: expect(add(2,3)).toBe(5)
    assert.Equal(t, 5, Add(2, 3), "Add(2,3) should equal 5")
}
```

* **Why `assert.Equal`:** Under the hood, it checks `expected == actual` and logs an error with actual vs expected values.
* **Why pass message:** Optional context for failures.

---

## 4. Phase 3: Table-Driven Tests

### 4.1 What?

A pattern where a slice of test cases drives multiple subtests, reducing code duplication.

### 4.2 Why?

* **DRY:** Don’t Repeat Yourself. Centralize test data.
* **Scalable:** Add new cases by appending to slice.
* **Clear Reporting:** Each case runs as a named subtest, improving test output.

### 4.3 When?

* When a function must be validated against many input/output combinations.

### 4.4 How?

1. **Define test-case struct:** Fields for description, inputs, expected outputs.
2. **Loop and run subtests:** Use `t.Run(tc.name, func(t *testing.T) { ... })`.
3. **Optional parallelism:** `t.Parallel()` inside subtest to run tests concurrently.

#### Jest `test.each` → Go Table Test

```js
// Jest Parametrized Tests
test.each([
  [1,2,3],
  [0,0,0],
  [-1,-1,-2]
])('add(%i,%i)=%i', (a, b, expected) => {
  expect(add(a,b)).toBe(expected);
});
```

```go
func TestAdd_TableDriven(t *testing.T) {
    tests := []struct {
        name     string // human-readable test case name
        a, b     int    // inputs
        expected int    // expected output
    }{
        {"positive", 1, 2, 3},
        {"zeros", 0, 0, 0},
        {"negatives", -1, -1, -2},
    }

    for _, tc := range tests {
        tc := tc // capture range variable
        t.Run(tc.name, func(t *testing.T) {
            t.Parallel() // run subtests in parallel (optional)
            got := Add(tc.a, tc.b)
            assert.Equal(t, tc.expected, got,
                "Add(%d,%d)", tc.a, tc.b)
        })
    }
}
```

* **Why `tc := tc`:** Prevents all subtests from sharing the same loop variable.
* **Why `t.Parallel()`:** Speeds up suite, use only when tests have no shared state.

---

## 5. Phase 4: HTTP Handler Testing

### 5.1 What?

Testing HTTP handlers/controllers without spinning up a real network server, using Go’s `net/http/httptest` package.

### 5.2 Why?

* **Speed:** No network overhead.
* **Isolation:** Focus on handler logic.
* **Simplicity:** Direct invocation of handler functions.

### 5.3 When?

* Immediately after implementing any HTTP endpoint.

### 5.4 How?

1. **Create fake request:** `httptest.NewRequest(method, url, bodyReader)`.
2. **Capture response:** `httptest.NewRecorder()` acts as a fake `ResponseWriter`.
3. **Invoke handler:** Call your handler directly with `(recorder, request)`.
4. **Inspect result:** Use `rec.Result()` and read `rec.Body`.

#### Jest+Supertest → Go `httptest` Comparison

| Supertest + Jest                          | Go `httptest`                                       |
| ----------------------------------------- | --------------------------------------------------- |
| `const res = await request(app).get('/')` | `req := httptest.NewRequest('GET','/',nil)`         |
| `res.statusCode`                          | `rec.Result().StatusCode`                           |
| `res.text`                                | `rec.Body.String()`                                 |
| `expect(res.body).toEqual(...)`           | `assert.JSONEq(t, expectedJSON, rec.Body.String())` |

#### Example: Ping Handler with JSON

```js
// api/handler.go
package api

import (
    "encoding/json"
    "net/http"
)

// PingResponse represents JSON response.
type PingResponse struct { Message string `json:"message"` }

func Ping(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json") // JSON header
    w.WriteHeader(http.StatusOK)                          // 200 status
    json.NewEncoder(w).Encode(PingResponse{Message: "pong"})
}
```

```js
// api/handler_test.go
package api

import (
    "net/http"
    "net/http/httptest"
    "testing"
    "github.com/stretchr/testify/assert"
)

func TestPingHandler(t *testing.T) {
    // Arrange
    req := httptest.NewRequest(http.MethodGet, "/ping", nil)
    rec := httptest.NewRecorder()

    // Act
    Ping(rec, req)

    // Assert
    res := rec.Result()
    defer res.Body.Close()

    assert.Equal(t, http.StatusOK, res.StatusCode, "should return 200")
    assert.Equal(t, "application/json", res.Header.Get("Content-Type"))

    expected := `{"message":"pong"}`
    assert.JSONEq(t, expected, rec.Body.String(), "response body JSON mismatch")
}
```

* **Why `json.NewEncoder` vs manual `Write`**: Encodes struct to JSON automatically.
* **Why `assert.JSONEq`:** Ignores ordering/whitespace differences.

---

## 6. Phase 5: Mocking Dependencies

### 6.1 What?

Replacing real external dependencies (databases, HTTP clients, email services) with controllable fakes or mocks.

### 6.2 Why?

* **Isolation:** Tests focus on your code, not external systems.
* **Determinism:** Fake implementations return predictable values.
* **Performance:** No network or I/O delays.

### 6.3 When?

* Whenever your code invokes external services or I/O.

### 6.4 How?

#### A. Manual Fakes (Jest’s `jest.fn()` style)

1. **Define an interface** for the dependency.
2. **Implement a fake struct** that satisfies the interface and records calls.
3. **Inject** the fake into your function or object under test.

```js
// email/service.go
package email

// Service defines sending emails.
type Service interface { Send(to, body string) bool }

// email/fake_service.go
package email

type FakeService struct {
    Called     bool
    CalledWith []string
}

func (f *FakeService) Send(to, body string) bool {
    f.Called = true                 // record invocation
    f.CalledWith = []string{to, body} // record arguments
    return true                     // static fake response
}
```

```js
// email/email_test.go
package email_test

import (
    "testing"
    "your_project/email"
    "github.com/stretchr/testify/assert"
)

func TestEmailSend_Fake(t *testing.T) {
    // Arrange: create fake
    fake := &email.FakeService{}

    // Act: call code under test (here directly using fake)
    ok := fake.Send("x@y.com", "hello")

    // Assert: behavior and interaction
    assert.True(t, ok, "expected Send to return true")
    assert.True(t, fake.Called, "expected Send to be called")
    assert.Equal(t, []string{"x@y.com", "hello"}, fake.CalledWith)
}
```

* **Why interface:** Decouples your code from concrete implementations.
* **Why record args:** Allows assertions on how the fake was used.

#### B. Testify Mocks (Jest’s `jest.mock()` style)

1. **Embed** `mock.Mock` in your mock struct.
2. **Override** methods to call `m.Called(arguments...)`.
3. **Setup expectations** in tests using `On(...).Return(...)`.

```js
// email/mock_service.go
package email

import "github.com/stretchr/testify/mock"

type MockService struct { mock.Mock }

func (m *MockService) Send(to, body string) bool {
    args := m.Called(to, body) // record call and get return values
    return args.Bool(0)         // return first stubbed boolean
}
```

```js
// email/email_test.go
package email_test

import (
    "testing"
    "your_project/email"
    "github.com/stretchr/testify/assert"
)

func TestEmailSend_Mock(t *testing.T) {
    // Arrange
    m := new(email.MockService)
    m.On("Send", "a@b.com", "hi").Return(true) // stub

    // Act
    result := m.Send("a@b.com", "hi")

    // Assert
    assert.True(t, result)
    m.AssertCalled(t, "Send", "a@b.com", "hi") // verify invocation
}
```

* **Why `On`/`Return`:** Like `jest.mock()` + `mockReturnValue`.
* **Why `AssertCalled`:** Like `expect(mockFn).toHaveBeenCalledWith(...)`.

---

## 7. Phase 6: Benchmarks & Coverage

### 7.1 Benchmarking

**What?** Measure performance of functions.

**Why?** Identify slow code paths and regressions in performance.

**When?** As part of performance testing or optimization cycles.

**How?**

* Write functions prefixed with `Benchmark` and signature `func(b *testing.B)`.
* Loop `b.N` times inside benchmark body.

#### Example

```js
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(1, 2)
    }
}
```

* **Run:** `go test -bench=.`
* **Output:** ops/sec and ns/op metrics.

### 7.2 Coverage

**What?** Reports which lines of code were exercised by tests.

**Why?** Ensure critical logic paths are covered by tests.

**When?** Regularly, especially before releases.

**How?**

```bash
# Generate coverage data
 go test -coverprofile=coverage.out

# View HTML report
 go tool cover -html=coverage.out
```

* **Coverage %:** Shows lines covered vs total.
* **HTML report:** Highlights uncovered lines in red.

---
