# Detailed Notes on Go's OOP Concepts vs Traditional OOP

---

## 1. Classes and Objects

### Traditional OOP

In traditional OOP languages like Java or C++, classes define templates, and objects are instances created from these classes.

```js
class User {
    String name;
    String email;

    User(String name, String email) {
        this.name = name;
        this.email = email;
    }
}

User u = new User("Prahlad", "prahlad@covlant.ai");
```

### Go Equivalent

Go doesn't support classes explicitly. Instead, it uses `struct` to achieve similar behavior:

```js
type User struct {
    name  string
    email string
}

func main() {
    // Creating an instance of User struct
    u := User{name: "Prahlad", email: "prahlad@covlant.ai"}

    // Printing user details
    fmt.Println(u)
}
```

**Why?**

* Go aims for simplicity and clarity, reducing complexity by avoiding explicit class-based syntax.
* Structs + methods in Go provide similar functionality with less overhead.

---

## 2. Inheritance vs Composition

### Traditional OOP (Inheritance)

Inheritance creates an "is-a" relationship, allowing subclasses to inherit properties and methods.

```js
class Person {
    String name;
}

class Employee extends Person {
    String company;
}

Employee emp = new Employee();
emp.name = "Prahlad";
emp.company = "Covlant";
```

### Go Equivalent (Composition)

Go uses composition, embedding one struct within another:

```js
type Person struct {
    name string
}

type Employee struct {
    Person  // Embedding Person struct
    company string
}

func main() {
    emp := Employee{Person: Person{name: "Prahlad"}, company: "Covlant"}

    // Directly accessing embedded struct's fields
    fmt.Println(emp.name, emp.company)
}
```

**Why Composition?**

* Composition is flexible: it allows struct to include multiple types.
* Reduces complexity and dependency issues common with inheritance.
* Encourages "has-a" relationships over "is-a," making code reusable.

---


---

## 3. Interfaces (Duck Typing)

### Traditional OOP

In traditional OOP languages like Java, interfaces explicitly define methods that implementing classes must have. A class explicitly declares it implements an interface.

```js
interface Notifier {
    void notifyUser(); // Declaring a method that classes must implement
}

class User implements Notifier {
    // User explicitly implements the Notifier interface
    public void notifyUser() {
        System.out.println("Notified"); // Implementation of interface method
    }
}

class Main {
    public static void main(String[] args) {
        User user = new User();
        user.notifyUser(); // Calling the implemented method
    }
}
```

### Go Equivalent (Implicit Interface)

Go doesn't require explicit declarations of interfaces. Instead, if a struct has methods with the exact signatures matching an interface, the struct implicitly satisfies the interface. This is known as duck typing.

```js
type Notifier interface {
    Notify() string // Interface method signature
}

// Struct with a method matching the interface's method signature
type User struct {
    name string
}

// Method defined on User struct that implicitly satisfies the Notifier interface
func (u User) Notify() string {
    return "Hello, " + u.name
}

// Function that accepts any type implementing Notifier
func SendNotification(n Notifier) {
    fmt.Println(n.Notify())
}

func main() {
    u := User{name: "Prahlad"}
    // User struct implicitly satisfies the Notifier interface because of Notify method
    SendNotification(u)
}
```

**Why Duck Typing?**

* **Flexibility:** Allows any type that fulfills the method contract to automatically satisfy an interface.
* **Reduced Boilerplate:** Eliminates the need for explicit implementation declarations.
* **Loose Coupling:** Code becomes easier to maintain and extend, as dependencies are minimized.

**Detailed Explanation:**

* **Interface Definition:** Defines a method or set of methods without specifying their implementation. Any struct providing those methods satisfies the interface automatically.
* **Implicit Satisfaction:** If a struct has methods matching all the signatures in the interface, Go recognizes this automatically, without explicit `implements` declaration. This reduces verbosity and makes code intuitive.
* **Practical Use:** Interfaces are widely used in Go for polymorphism, abstraction, and creating flexible, modular codebases. You can easily swap implementations without modifying the dependent code.


---

## 4. Enums in Go (Using `iota`)

###  What Are Enums?

Enums (short for "enumerations") are a way to define a **set of named constants** that belong to the same type.

They’re useful for:

* Representing a fixed set of values (like days of the week, user status, etc.)
* Making your code more **readable, safe, and structured**

### 🔹 Traditional OOP Languages (e.g., Java):

```java
enum Status {
    PENDING,
    APPROVED,
    REJECTED
}

Status s = Status.APPROVED;
```

Here `Status` is an enum that can take only the listed values. It helps with **type-safety** and makes code expressive.

---

###  Go's Approach to Enums — Using `const` + `iota`

Go does **not** have a native `enum` keyword, but you can achieve the same behavior using:

* `const` to define constants
* `iota` to automatically assign incrementing values

---

## Step-by-Step Enum Implementation in Go

```js
// Step 1: Define a new type 'Status' based on int
// This improves type-safety and groups related constants

package main

import "fmt"

type Status int

// Step 2: Declare constants using iota
const (
    Pending Status = iota // 0 → iota starts at 0
    Approved              // 1 → iota increments automatically
    Rejected              // 2 → continues incrementing
)

func main() {
    var s Status = Approved              // Assign one of the enum constants
    fmt.Println("Raw value:", s)         // Prints: 1 (int value of Approved)
}
```

### Why use `iota`?

* `iota` simplifies the declaration of incrementing values.
* Prevents manual errors like mistyped numbers.
* Keeps related constants readable and maintainable.

---

##  Improving Output: Add `String()` Method

By default, printing an enum shows its numeric value. Let’s fix that:

```js
// Add a method that attaches to the Status type
// It converts the enum value to a human-readable string

func (s Status) String() string {
    switch s {
    case Pending:
        return "Pending"
    case Approved:
        return "Approved"
    case Rejected:
        return "Rejected"
    default:
        return "Unknown"
    }
}

func main() {
    s := Approved
    fmt.Println("Human-readable:", s.String()) // Output: Approved
}
```

###  Benefits of String() method:

* Makes CLI outputs and logs more meaningful
* Useful for debugging and UI display
* Similar to Java/C++ enums where `toString()` is auto-supported

---

##  Skipping and Custom Values with `iota`

You can skip values or assign your own:

```js
const (
    _ Status = iota       // Skip value 0
    Started               // 1
    InProgress            // 2
    _                     // Skip 3
    Done = 10             // Manually set value
    Final                 // Auto-increments from Done → 11
)
```

###  Why skip or set manually?

* To align with API specs or external systems
* To avoid using invalid/reserved values
* For special mapping logic


---

## 5. Polymorphism in Go (via Interfaces)

**Code Example**

```js
// Shape interface defines a contract: any type with Area() float64 satisfies Shape
type Shape interface {
    Area() float64
}

// Circle type and its Area implementation
type Circle struct {
    radius float64
}
func (c Circle) Area() float64 {
    return 3.14 * c.radius * c.radius // πr² calculation
}

// Square type and its Area implementation
type Square struct {
    side float64
}
func (s Square) Area() float64 {
    return s.side * s.side // side² calculation
}

// PrintArea accepts any Shape and prints its area
func PrintArea(shape Shape) {
    fmt.Println("Area:", shape.Area())
}

func main() {
    c := Circle{radius: 2}
    sq := Square{side: 4}
    PrintArea(c)  // Output: Area: 12.56
    PrintArea(sq) // Output: Area: 16
}
```

**Why Polymorphism via Interfaces?**

* **Flexible**: PrintArea depends only on Shape, not concrete types. New types (e.g., Rectangle) can be added without modifying PrintArea.
* **Reusable**: A single function handles multiple types that implement the same method.
* **Decoupled**: Each type hides its internal area calculation; consumers only call Area().
* **Clean code**: No inheritance hierarchies; Go uses composition and interfaces instead.


## 6. Encapsulation in Go (Naming Convention)

**Code Example**

```js
package main
import "fmt"

// User has an exported field Name and an unexported field email
type User struct {
    Name  string // exported: accessible outside package
    email string // unexported: private to this package
}

func main() {
    u := User{Name: "Prahlad", email: "prahlad@covlant.ai"}
    fmt.Println(u.Name)  // works
    // fmt.Println(u.email) // compile error
}
```

**Why Naming Convention?**

* Uppercase identifiers are exported (public); lowercase are unexported (private).
* No extra keywords required; leverages Go’s package system.
* Prevents unintended access or modifications.

**When to use**

* To hide internal fields or helper data from other packages.
* In library code to expose only necessary API.

**Where it matters**

* Modules and packages where implementation details should remain private.

---

## 7. Abstraction in Go (Interfaces + Encapsulation)

**Code Example**

```js
package main
import "fmt"

// Storage defines a generic Save method for any storage implementation
type Storage interface {
    Save(data string)
}

// FileStorage writes data to a file
type FileStorage struct{}
func (f FileStorage) Save(data string) {
    fmt.Println("[File] Saving data:", data)
}

// MemoryStorage stores data in a slice
type MemoryStorage struct {
    store []string
}
func (m *MemoryStorage) Save(data string) {
    m.store = append(m.store, data)
    fmt.Println("[Memory] Stored data, count =", len(m.store))
}

func main() {
    var s Storage
    s = FileStorage{}
    s.Save("Example file data")

    ms := &MemoryStorage{store: []string{}}
    s = ms
    s.Save("Example memory data")
}
```

**Why Abstraction?**

* **Hides complexity**: Consumers call Save() without knowing implementation details.
* **Swappable implementations**: You can switch between file, memory, database, or network storage.
* **Testable**: Interfaces can be mocked in tests.