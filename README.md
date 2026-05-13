# Go Programming Cheatsheet
> **Comprehensive Go cheatsheet covering syntax, types, control flow, functions, slices, maps, pointers, structs, interfaces, error handling, concurrency (goroutines/channels/select), packages, testing, JSON, HTTP, sync primitives, and best practices. Perfect for quick reference—from basics to advanced patterns like worker pools and table-driven tests. Includes runnable examples and common pitfalls.**

## Table of Contents

1. [Basic Syntax](#1-basic-syntax)
2. [Variables & Types](#2-variables--types)
3. [Control Structures](#3-control-structures)
4. [Functions](#4-functions)
5. [Arrays, Slices & Maps](#5-arrays-slices--maps)
6. [Pointers](#6-pointers)
7. [Structs & Methods](#7-structs--methods)
8. [Interfaces](#8-interfaces)
9. [Error Handling](#9-error-handling)
10. [Concurrency (Goroutines & Channels)](#10-concurrency-goroutines--channels)
11. [Packages & Imports](#11-packages--imports)
12. [Testing](#12-testing)
13. [Common Packages](#13-common-packages)
14. [Best Practices](#14-best-practices)

---

## 1. Basic Syntax

### Hello World
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

### Run a program
```bash
go run main.go
go build main.go   # compile to binary
```

---

## 2. Variables & Types

### Declaration
```go
var name string = "Go"
var version int        // zero value: 0
var x, y int = 1, 2
var (                 // block declaration
    a string
    b bool
)

// Short-hand (inside functions only)
lang := "Golang"
count := 42
```

### Basic Types
```go
bool
string
int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr
byte // alias for uint8
rune // alias for int32 (Unicode code point)
float32 float64
complex64 complex128
```

### Constants
```go
const Pi = 3.14
const (
    StatusOK = 200
    StatusNotFound = 404
)
```

### Type Conversion
```go
i := 42
f := float64(i)
u := uint(f)
s := string(rune(i))
```

---

## 3. Control Structures

### If/Else
```go
if x > 0 {
    fmt.Println("positive")
} else if x < 0 {
    fmt.Println("negative")
} else {
    fmt.Println("zero")
}

// With short statement
if err := doSomething(); err != nil {
    fmt.Println(err)
}
```

### For Loop (Go has only `for`)
```go
// Classic for
for i := 0; i < 10; i++ { }

// While-style
for condition { }

// Infinite loop
for { }

// Range
for index, value := range slice { }
for key, value := range map { }
for index, char := range "hello" { }
for key := range map { }  // keys only
```

### Switch
```go
switch day {
case "Mon":
    fmt.Println("Monday")
case "Tue", "Wed":
    fmt.Println("Midweek")
default:
    fmt.Println("Other")
}

// Tagless switch (clean if-else chains)
switch {
case score >= 90:
    grade = "A"
case score >= 80:
    grade = "B"
default:
    grade = "F"
}
```

### Defer
```go
func example() {
    defer fmt.Println("world") // runs after function returns
    fmt.Println("hello")
}
// Output: hello world
```

---

## 4. Functions

### Basic Function
```go
func add(a int, b int) int {
    return a + b
}

// Named return values
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return // naked return
}
```

### Multiple Return Values
```go
func div(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}
```

### Variadic Functions
```go
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}
sum(1, 2, 3)
```

### Function Literals (Closures)
```go
func adder() func(int) int {
    sum := 0
    return func(x int) int {
        sum += x
        return sum
    }
}
```

---

## 5. Arrays, Slices & Maps

### Arrays (fixed size)
```go
var arr [3]int = [3]int{1, 2, 3}
arr := [3]int{1, 2, 3}
arr := [...]int{1, 2, 3} // size inferred
```

### Slices (dynamic)
```go
slice := []int{1, 2, 3}
slice = append(slice, 4)
slice = append(slice, 5, 6)

// Make slice
s := make([]int, 5)       // len=5, cap=5
s := make([]int, 3, 10)   // len=3, cap=10

// Slice operations
s[1:3]    // from index 1 to 2
s[:5]     // first 5
s[3:]     // from index 3

// Copy
copy(dest, src)
```

### Maps
```go
m := make(map[string]int)
m["key"] = 42
value, exists := m["key"]   // exists = true
delete(m, "key")

// Map literal
m := map[string]int{
    "a": 1,
    "b": 2,
}
```

---

## 6. Pointers

```go
i := 42
ptr := &i        // pointer to i
*ptr = 21        // dereference (i becomes 21)

func zero(val *int) {
    *val = 0
}
zero(&i)
```

> **No pointer arithmetic** in Go.

---

## 7. Structs & Methods

### Defining Structs
```go
type Person struct {
    Name string
    Age  int
}

p := Person{Name: "Alice", Age: 30}
p := Person{"Bob", 25}
p.Name = "Charlie"
```

### Methods
```go
// Value receiver
func (p Person) Greet() string {
    return "Hello, " + p.Name
}

// Pointer receiver (modifies struct)
func (p *Person) SetAge(age int) {
    p.Age = age
}
```

### Struct Embedding (Composition)
```go
type Employee struct {
    Person     // embedded
    Salary int
}

e := Employee{Person: Person{Name: "Tom"}, Salary: 50000}
e.Greet()  // method promoted from Person
```

---

## 8. Interfaces

### Defining & Implementing
```go
type Greeter interface {
    Greet() string
}

func (p Person) Greet() string {
    return "Hello, " + p.Name
}

// Any type with Greet() method implements Greeter automatically
func sayHello(g Greeter) {
    fmt.Println(g.Greet())
}
```

### Empty Interface (any type)
```go
var anything interface{}
anything = 42
anything = "string"

// Type assertion
value, ok := anything.(int)

// Type switch
switch v := anything.(type) {
case int:
    fmt.Println("int:", v)
case string:
    fmt.Println("string:", v)
default:
    fmt.Println("unknown")
}
```

---

## 9. Error Handling

### Basic Pattern
```go
import "errors"

func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}

result, err := divide(10, 0)
if err != nil {
    log.Fatal(err)
}
```

### Custom Errors
```go
type MyError struct {
    Msg string
    Code int
}

func (e *MyError) Error() string {
    return fmt.Sprintf("error %d: %s", e.Code, e.Msg)
}
```

### Panic & Recover
```go
defer func() {
    if r := recover(); r != nil {
        fmt.Println("Recovered:", r)
    }
}()
panic("something went wrong")
```

---

## 10. Concurrency (Goroutines & Channels)

### Goroutines
```go
go func() {
    fmt.Println("running concurrently")
}()
```

### Channels
```go
ch := make(chan int)

// Send and receive
go func() { ch <- 42 }()
value := <-ch

// Buffered channel
ch := make(chan int, 2)
ch <- 1
ch <- 2

// Close channel
close(ch)
for v := range ch { }  // loop until closed
```

### Select
```go
select {
case msg1 := <-ch1:
    fmt.Println(msg1)
case msg2 := <-ch2:
    fmt.Println(msg2)
case <-time.After(1 * time.Second):
    fmt.Println("timeout")
default:
    fmt.Println("no channel ready")
}
```

### Worker Pool Pattern
```go
jobs := make(chan int, 100)
results := make(chan int, 100)

// Start workers
for w := 1; w <= 3; w++ {
    go worker(jobs, results)
}
```

### Sync Primitives
```go
var mu sync.Mutex
mu.Lock()
// critical section
mu.Unlock()

var wg sync.WaitGroup
wg.Add(1)
go func() {
    defer wg.Done()
    // work
}()
wg.Wait()
```

---

## 11. Packages & Imports

### Package Declaration
```go
package mypkg   // every .go file must start with package <name>
```

### Importing
```go
import "fmt"
import "math/rand"

// Aliased import
import m "math"

// Import for side effects (init function)
import _ "image/png"
```

### Exported Names
- **Capitalized** = exported (public)
- **lowercase** = unexported (private)

---

## 12. Testing

### File: `something_test.go`
```go
package main

import "testing"

func TestAdd(t *testing.T) {
    got := add(2, 3)
    want := 5
    if got != want {
        t.Errorf("got %d, want %d", got, want)
    }
}

// Table-driven test
func TestDivide(t *testing.T) {
    tests := []struct {
        name string
        a, b int
        want int
        err  bool
    }{
        {"positive", 10, 2, 5, false},
        {"by zero", 10, 0, 0, true},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := divide(tt.a, tt.b)
            if (err != nil) != tt.err {
                t.Errorf("error = %v, wantErr %v", err, tt.err)
            }
            if got != tt.want {
                t.Errorf("got %d, want %d", got, tt.want)
            }
        })
    }
}

// Run tests
// go test
// go test -v
// go test -cover
```

---

## 13. Common Packages

| Package | Purpose |
|---------|---------|
| `fmt` | Formatting & printing |
| `io` / `os` | File & I/O operations |
| `net/http` | HTTP client & server |
| `encoding/json` | JSON marshaling/unmarshaling |
| `strings` | String manipulation |
| `strconv` | String conversions |
| `time` | Date/time handling |
| `sync` | Mutex, WaitGroup, etc. |
| `context` | Request-scoped values, cancellation |

### JSON Example
```go
type User struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

u := User{"Alice", 30}
jsonData, _ := json.Marshal(u)

var u2 User
json.Unmarshal(jsonData, &u2)
```

### HTTP Server
```go
http.HandleFunc("/", handler)
http.ListenAndServe(":8080", nil)

func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, %s!", r.URL.Path[1:])
}
```

---

## 14. Best Practices

- **Formatting**: Always run `go fmt`
- **Naming**: Use camelCase, keep names short (but not too short)
- **Error handling**: Always check errors, never ignore them
- **Zero values**: Leverage zero-value initialization
- **Don't panic** in libraries; return errors instead
- **Use `go vet`** to detect suspicious constructs
- **Prefer composition over inheritance**
- **Accept interfaces, return structs**
- **Keep `main()` small** – move logic to packages
- **Use `-race` flag** when testing concurrent code:
  ```bash
  go test -race
  go run -race main.go
  ```

---

> **Pro Tip**: Bookmark [pkg.go.dev](https://pkg.go.dev) and use `go doc <package>` from the command line.
