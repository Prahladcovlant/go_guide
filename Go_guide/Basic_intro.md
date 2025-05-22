# Section 0: Getting Started with Go

---

## 0.1 Introduction to Golang

### What is Golang?

Golang (or Go) is an open-source, statically typed, compiled programming language created at Google in 2007 by Robert Griesemer, Rob Pike, and Ken Thompson. It was officially released to the public in 2009.

### Why was it created?

The creators of Go were frustrated with:

* Slow build times (especially in C++)
* Complex dependency management
* Heavy concurrency problems in C/Java
* Verbose and error-prone code

Hence, they aimed to build a language that is:

* Fast like C/C++
* Simple like Python
* Powerful concurrency model
* Statically typed and compiled to machine code

---

## 0.2 Why use Go? (Pros, Cons, Use Cases)

### Pros:

* Fast Compilation & Execution
* Built-in Concurrency (Goroutines, Channels)
* Cross-Platform (Compile once, run anywhere)
* Single binary output — No need for VM or interpreter
* Great standard library
* Easy syntax & readability
* Strong tooling: fmt, lint, vet, go build, go mod


### Use-Cases:

* Scalable backend systems and microservices
* CLI tools (Docker, Kubernetes, Terraform)
* Networking and system tools
* Real-time services

---



## 0.3 Hello World Program

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Prahlad!")
}
```

### Explanation:

* `package main` → Entry point of an executable program
* `import "fmt"` → Standard I/O package
* `func main()` → Entry point function (like C's `int main()`)
* `fmt.Println()` → Print line to terminal

### Run:

```sh
go run hello.go
```

Output:

```
Hello, Prahlad!
```

### Build:

```sh
go build hello.go
```

This creates a single binary(which you can run anywhere): `hello` → run using `./hello`

---

## 0.4 `go mod init` = Go’s version of `npm init`

### Why use `go mod init`?

* Helps manage dependencies for your Go project
* Required for modern Go projects
* Generates `go.mod` file like `package.json`
* Lets you work outside of GOPATH

### Usage:

```sh
mkdir myapp
cd myapp
go mod init github.com/username/myapp
```

Creates:

```txt
go.mod
```

Add dependency:

```sh
go get github.com/gin-gonic/gin
```

Automatically updates `go.mod` and creates `go.sum`

---

## 0.5 What is Single Binary Compilation?

### Go produces a single binary file:

* No need for external dependencies
* No Go installation needed on target machine
* Fully self-contained executable

### Comparison Table:

| Language | Needs Interpreter/VM? | Output |
| -------- | --------------------- | ------ |
| Python   | Yes                   | .py    |
| Java     | Yes (JVM)             | .class |
| Go       | No                    | Binary |

### Command:

```sh
go build main.go
```

Creates: `main` → portable, can be run anywhere.

### Benefits:

* Fast execution
* Secure (no source code exposure)
* Easy deployment (just copy binary)
* Cross compilation supported:

```sh
GOOS=linux GOARCH=amd64 go build main.go
```

---

## 0.6 Concurrency vs Parallelism (Conceptual Deep Dive)

### Concurrency:

Multiple tasks in progress at the same time (via task switching)

Analogy: 1 person switching between WhatsApp, Insta, and Mail.

### Parallelism:

Multiple tasks running at the exact same time on multiple cores

Analogy: 3 people doing WhatsApp, Insta, and Mail simultaneously.

### Go Handles Both:

* Concurrency via Goroutines (`go func()`) → Lightweight tasks
* Parallelism via `runtime.GOMAXPROCS(n)` → Use multiple CPU cores



---