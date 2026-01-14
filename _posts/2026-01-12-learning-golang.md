---
layout: post 
title: Learning GoLang
category: technicalArticles
---

> From my experience working at [Punch](https://www.punch.trade/). Read docs, youtube, GPT explanations. 

Learning Golang!. I may take occasional detours as a part of understanding things 'properly'. 

### Fundamentals

Nuances in Go: 
- Strict compile time checks, i.e: If you have declared a variable or imported a package in code, you MUST use it. Unused entities in code will lead to compile time errors thrown. 
  - Compiled vs Interpreted language: 
    - Your CPU runs instructions in 0s and 1s. So every instruction defined via a language must eventually become machine code. The difference is WHEN and HOW that translation happens.
    - Compiled languages Flow: Source code → Compiler → Machine code executable → Run the executable. Compiler: Reads the entire program; Does static analysis; Optimizes globally. Interpreted languages Flow: Source code → Interpreter → Execute line by line. Interpreter: Reads one statement, Executes it immediately, Moves to the next. Compiled languages came first. Note that today almost all modern “interpreted” languages compile internally.
    - What did interpreted languages solve?: Early computers were painful; Compilation took minutes to hours hence debugging meant: Write code, Compile, Run, Crash, Repeat. Interpreters solved this: Immediate feedback, Interactive programming (REPL), Dynamic behavior. For example:
    - ```
      Eg1: — Calculator 
      In Python (Interpreted, can do interactive exploration): 
      >>> 10 * 3
      30
      >>> 10 * 3 + 5
      35
      >>> (10 * 3 + 5) / 7
      5.0
      In C (Compiled): 
      - Write the code first 
      #include <stdio.h>
      int main() {
          printf("%d\n", (10 * 3 + 5) / 7);
      }
      - Then run gcc calc.c -> ./a.out. 
      > Interpreter lets you think with the computer, compiler forces you to prepare a program first. Interpreters were not invented to replace compilation rather solve human feedback speed, not program execution speed.
      > Interpreted languages shine when you don’t yet know what the program should be, if you know, then Compiled language/Interpreted language would anyways work in similar way. 
      
      Eg2: Parsing unknown / messy data
      In Python: 
      import json
      with open("data.json") as f:
          data = json.load(f)
      type(data)
      len(data)
      data[0].keys()
      You inspect:
      >>> data[0]["user_id"]
      >>> data[0].get("timestamp")
      >>> [x for x in data if "error" in x]
      You discover the data while writing code.
      In C you must: 
      Decide struct layout upfront, Handle parsing manually, Recompile every structural change, Print + inspect. C forces decisions early. Python lets decisions happen late.
      ```
- Strong + static typing (but with inference). 
  - It is statically typed language, i.e: The type of every variable/expression is known before the program runs. Dynamic typic means the type of variable is know during runtime of program. 
  - Go gives capability to infer the type of variable even if you don't mention the type in code, during compile time, using it's type-inference. Eg: `Usual code eg: var x int = 10. But if you don't mention 'int', it will still be able to infer the type during compile time, i.e: var x = 10`.  
- If you don't assign a value to variable, Go assumes default values, it never leaves variables uninitialized. Eg: 
  ```
  | Type    | Zero value |
  | ------- | ---------- |
  | int     | 0          |
  | string  | ""         |
  | bool    | false      |
  | pointer | nil        |
  | slice   | nil        |
  | map     | nil        |
  ```
- No semicolons (mostly) to end code in line. Go takes care of it. Eg: `var x int = 10 is fine, no need for var x int = 10;`. 
- Explicit conversions must be done if required in values. Eg: `var y float64 = float64(x)`.
- if / for / switch need no parentheses
- Only ONE loop keyword: for. No while, no do-while.
- Functions can return multiple values. 
  ```
  func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("divide by zero")
    }
    return a / b, nil
  }
  ```
- Error handling is explicit. Eg: 
  ```
  result, err := divide(10, 2)
  if err != nil {
      return err
  }
  ```
- No classes, no inheritance. Go uses: structs, interfaces, composition. 
- Interfaces are implicit. Eg: 
  ```
  type Reader interface {
      Read() string
  }
  If a struct has Read(), it automatically implements Reader. No need to use a keyword like implements which we do like in Java. 
  ```
- Pointers but no pointer arithmetic. Like: `p := &x, is fine, but can't do things like: p++ // illegal`
- Arrays vs slices: Arrays are fixed, Slices in Go are dynamic sized (mostly used). 
  ```
  var a [3]int // fixed size of 3 - arr
  var s []int // dynamic - slice

  Note:
  1. nil slice ≠ empty slice
  var s []int     // nil
  s := []int{}    // empty
  2. To add elements in slice, eg: s = append(s, "hello")
  ```
- Maps must be initialized
  ```
  var m map[string]int
  Doing: m["a"] = 1 // Wrong
  Rather: m := make(map[string]int)
  Note: 
  | new              vs        make            |
  | ---------------- | ----------------------- |
  | allocates memory | allocates + initializes |
  | returns pointer  | returns value           |
  | rarely used      | commonly used           |
  ```
- func init() in Go is a special function that runs automatically during a package's initialization, before any other functions in the package are called, including main(). It's used for setup and configuration tasks. 
- No function overloading. 
- Other things like built-in concurrency, defer, etc., will add about this in later parts. 
- ```
  A random example: 
  Java style OOP:
    class User {
        private String name;
        User(String name) {
            this.name = name;
        }
        public String greet() {
            return "Hi " + name;
        }
    }
  Go style OOP:
    type User struct {
        name string
    }
    func NewUser(name string) *User {
        return &User{name: name}
    }
    func (u *User) Greet() string {
        return "Hi " + u.name
    }
    ```

### Phase 1

- Keywords used: 
  - Declarations: package, import, var, const, type, func  
  - Control flow: if, for, switch, select
  - Concurrency: go, chan
  - Memory/lifecycle: new, make, defer
  - Error/exit: panic, recover, return
- Variable declarations:
```
For Single variable:
1. var x string = "" -> Used and declared inside/outside (global variable) functions; Declares + Assigns value

2. var x = "" -> Used inside/outside functions; Go infers 'x' is a string during compile time (Go is statically typed language); Declares + Assigns value

3. x := "" -> Used ONLY inside functions; Declares + Assigns value; It's var only, not const (we learn about this later)

4. x = "hello" -> Used inside/outside functions; Assigns ONLY, hence 'x' must exist already

For Multiple variables:
1. 
var name1, name2 string
var age int
var isAdmin bool
name = "foo"
name2 = "bar"
age = 25
isAdmin = false

2. 
var (
    name string = "suraj"
    age int = 25
    active bool = true
)

3. (type-interface taking care)
var name, age, active = "suraj", 25, true

4. 
func main() {
    name, age, active := "suraj", 25, true
    _, _, _ = name, age, active
}
_ -> It is a blank identifier. Since Go requires every declared variable MUST be used, blank identifier flags to compiler that the variable exists, not deliberately using it, but may use in future. 
```
- DS in Go:
  - Keywords:
    - var: mutable, package-level declaration
    - const: immutable 
    - type: define new types, used for: Structs, Interfaces, etc. 
    - func: functions
    - import: dependency management and package: namespace
      - A namespace is a named logical container that groups identifiers (functions, variables, types) so they don’t clash with others. In Go, packages are namespaces.
      - If a function/variable/struct/interface/const, etc., variable is capitalized - Means access is public, if smallcase then access is private (accessible only inside same package)
      ```
      func CreateUser() User {   // public function
          return User{Name: "Suraj", Age: 10}
      }
      func createUser() User {   // private function
          return User{Name: "Suraj", Age: 10}
      }
      ```
  - DS: 
  ```
  The type is always on the RIGHT side, if not inferring. Eg: var x int, var s []string, var m map[string]int

  Normal variables ~
  > x := 10
  > var x string = ""
  > var name string
    name = "Suraj"

  Arrays (Fixed size) ~ 
  > var a [3]string
    a[0] = "a" 
  > x := [3]int{1, 2, 3}

  Slices (Dynamic size) ~
  > s := []int{1, 2, 3} // or 
  > s2 := []string{}
    s2 = append(s, "hello") // append is only in slice, not arrays
  > arr := [5]int{1, 2, 3, 4, 5}
    s := arr[1:4] // [2 3 4] // slice from an array 

  Maps (key → value) ~
  > m := map[string]int{
          "apple":  10,
          "banana": 20,
        }
    price := m["apple"]

  Structs ~ 
  > type User struct {
        Name string
        Age  int
    }
    u := User{
        Name: "suraj",
        Age:  25,
    }
    fmt.Println(u.Name) // Capitalized, hence public access from all packages, if lowercase letters named in struct then private access. 
  
  Pointers ~ 
  > x := 10
    p := &x   // pointer to x

  Interface (behavior, not data) ~
  > type Speaker interface {
      Speak() string
    }
    // Any type that has Speak() string automatically implements Speaker interface, no need to use stuff like override, etc. Use eg: 
    func (p Person) Speak() string {
        return "Hello, I am " + p.Name
    }
  ```
  - DataTypes:
  ```
  10      // int, we do have int8, int64, uint/uint8... - unsigned int which has positive values, etc... 
  3.14    // float64
  true    // bool
  "hi"    // string
  'A'     // byte
  '世'     // rune
  ```
- Loops/If-Else/Switches
  ```
  Normal loop:
  for i := 0; i < 5; i++ {
    fmt.Println(i)
  }

  Infinite loop:
  for {
    fmt.Println("running")
  }

  Loop over slice/map:
  for i, v := range nums {
    fmt.Println(i, v)
  }

  If-Else:
  if x > 10 {
    fmt.Println("big")
  } else {
      fmt.Println("small")
  }

  Switch:
  switch day {
    case "Mon":
        fmt.Println("Start")
    case "Sun":
        fmt.Println("Rest")
    default:
        fmt.Println("Other")
  }
  ```
- Functions
  ```
  Normal:
  func add(a int, b int) int {
    return a + b
  }

  Multiple returns: 
  func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("divide by zero")
    }
    return a / b, nil
  }

  // Structs, Interface, covered in past. 
  ```

### Phase 2

File structure in Go: 
Assume:
```
hello-go/
├── go.mod
├── main.go
└── mathutils/
    └── add.go
```
Create go.mod using: `go mod init hello-go`
go.mod can be imagined as: requirements.txt + project identity + version lock
Keeping module name same as folder name is best practice, else when you import other packages, you'll have to do an explicit handling. 

Files: 
```
mathutils/add.go:
package mathutils
// Add is a public function
func Add(a int, b int) int {
    return a + b
}
// Note: Package name mathutils would be same across all files in that folder. Note that main.go, is a special package, since it's entrypoint file.  

main.go:
package main
import (
    "fmt"               // standard library
    "hello-go/mathutils" // local package
)
func main() {
    sum := mathutils.Add(3, 4)
    fmt.Println("Sum:", sum)
}
// Note: Import path = module + folder, and not exactly as folder path. 
```

Run program using: go run .
To install third party packages: go get github.com/google/uuid
When you do so, a go.sum file is created (you don’t edit this). It stores checksums, ensures integrity; Can be imagined as pip-lock/poetry.lock file in Python. 


------------------------------------------------
