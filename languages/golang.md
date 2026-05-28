## Golang Interview Quesions
---

**I. Go Basics & Syntax (1-20)**

1.  **Q: What is Go (Golang)?**
    **A:** Go is a statically typed, compiled programming language designed at Google. It is known for its simplicity, efficiency, strong support for concurrency, and garbage collection. It's often used for systems programming, web servers, distributed systems, and command-line tools.

2.  **Q: What are the main features or design goals of Go?**
    **A:**
    *   **Simplicity:** Clean and concise syntax.
    *   **Concurrency:** Built-in support for concurrent programming using goroutines and channels.
    *   **Fast Compilation:** Quick build times.
    *   **Statically Typed:** Type safety with type inference.
    *   **Garbage Collection:** Automatic memory management.
    *   **Standard Library:** Rich and comprehensive standard library.
    *   **Tooling:** Excellent built-in tools (e.g., `go fmt`, `go test`, `go build`).
    *   **Efficiency:** Compiles to native machine code.

3.  **Q: How do you declare a variable in Go? (Mention different ways)**
    **A:**
    *   Using `var` keyword with type: `var age int`
    *   Using `var` with initializer (type inferred): `var name = "Alice"`
    *   Short variable declaration (inside functions only, type inferred): `count := 10`
    *   Multiple variables:
        ```go
        var x, y int = 1, 2
        a, b := "hello", "world"
        var (
            isActive bool
            score    float64
        )
        ```

4.  **Q: What are the basic data types in Go?**
    **A:**
    *   **Boolean:** `bool` (`true`, `false`)
    *   **Numeric:**
        *   Integers: `int`, `int8`, `int16`, `int32`, `int64`, `uint`, `uint8` (alias `byte`), `uint16`, `uint32`, `uint64`, `uintptr`
        *   Floating-point: `float32`, `float64`
        *   Complex: `complex64`, `complex128`
    *   **String:** `string` (immutable sequence of bytes, usually UTF-8 encoded text)
    *   **Rune:** `rune` (alias for `int32`, represents a Unicode code point)

5.  **Q: What is the zero value for different types in Go?**
    **A:** When a variable is declared without an explicit initial value, it's given its zero value:
    *   Numeric types: `0`
    *   Boolean type: `false`
    *   String type: `""` (empty string)
    *   Pointers, functions, interfaces, slices, channels, maps: `nil`

6.  **Q: How do you define a constant in Go?**
    **A:** Using the `const` keyword. Constants must be known at compile time.
    ```go
    const Pi = 3.14159
    const (
        StatusOK    = 200
        StatusError = 500
    )
    const Greeting string = "Hello"
    ```

7.  **Q: What is `iota` in Go?**
    **A:** `iota` is a special predeclared identifier used in constant declarations to create a sequence of related values. It resets to `0` at the start of each `const` block and increments by one for each subsequent constant specification in that block.
    ```go
    const (
        C0 = iota // C0 == 0
        C1 = iota // C1 == 1
        C2 = iota // C2 == 2
    )
    const (
        Read    = 1 << iota // 1 << 0 = 1
        Write   = 1 << iota // 1 << 1 = 2
        Execute = 1 << iota // 1 << 2 = 4
    )
    ```

8.  **Q: Explain the `for` loop in Go. (Mention different forms)**
    **A:** Go has only one looping construct, the `for` loop, which can be used in several ways:
    *   **Complete `for` statement (like C/Java):**
        ```go
        for i := 0; i < 10; i++ { /* ... */ }
        ```
    *   **Condition-only `for` (like `while`):**
        ```go
        sum := 1
        for sum < 1000 { sum += sum }
        ```
    *   **Infinite loop:**
        ```go
        for { /* ... break when needed ... */ }
        ```
    *   **`for...range` loop (for iterating over slices, arrays, strings, maps, channels):**
        ```go
        nums := []int{2, 3, 4}
        for index, value := range nums { /* ... */ }
        for key, value := range myMap { /* ... */ }
        for charIndex, runeValue := range "hello" { /* ... */ }
        ```

9.  **Q: How do you write an `if/else` statement in Go?**
    **A:**
    ```go
    if x > 0 {
        fmt.Println("x is positive")
    } else if x < 0 {
        fmt.Println("x is negative")
    } else {
        fmt.Println("x is zero")
    }
    // With a short statement
    if err := someFunc(); err != nil {
        fmt.Println("Error:", err)
    }
    ```
    Parentheses around the condition are not used, but curly braces `{}` are always required.

10. **Q: How does the `switch` statement work in Go?**
    **A:** The `switch` statement in Go is more flexible than in C or Java.
    *   Cases can be non-integer types (e.g., strings).
    *   Cases evaluate from top to bottom, and the first matching case is executed.
    *   There's an implicit `break` after each case (no fallthrough by default). Use `fallthrough` keyword for explicit fallthrough.
    *   A `switch` without an expression is an alternate way to write if/else if/else logic.
    ```go
    switch os := runtime.GOOS; os {
    case "darwin":
        fmt.Println("macOS")
    case "linux":
        fmt.Println("Linux")
    default:
        fmt.Printf("%s.\n", os)
    }

    num := 2
    switch { // switch true
    case num == 1:
        fmt.Println("One")
    case num == 2:
        fmt.Println("Two")
    default:
        fmt.Println("Other")
    }
    ```

11. **Q: How do you define and call a function in Go?**
    **A:**
    ```go
    // Define a function
    func add(x int, y int) int {
        return x + y
    }
    // Define a function with named return values
    func swap(x, y string) (string, string) {
        return y, x
    }
    // Call a function
    func main() {
        sum := add(5, 3)
        fmt.Println(sum) // 8
        a, b := swap("hello", "world")
        fmt.Println(a, b) // world hello
    }
    ```

12. **Q: Can functions return multiple values in Go?**
    **A:** Yes, Go functions can return multiple values. This is commonly used to return a result and an error value.
    ```go
    func divide(a, b int) (int, error) {
        if b == 0 {
            return 0, errors.New("cannot divide by zero")
        }
        return a / b, nil
    }
    ```

13. **Q: What are named return values in Go?**
    **A:** Named return values are variables declared in the function signature that will be returned. A bare `return` statement in such a function returns the current values of these named variables.
    ```go
    func calculate(a, b int) (sum int, product int) {
        sum = a + b
        product = a * b
        return // Implicitly returns sum and product
    }
    ```
    While they can be convenient, overuse can sometimes make code less clear.

14. **Q: What are variadic functions in Go?**
    **A:** Variadic functions are functions that can be called with a variable number of arguments of a specific type. The last parameter is prefixed with `...`. Inside the function, the variadic parameter is treated as a slice of that type.
    ```go
    func sumAll(numbers ...int) int {
        total := 0
        for _, num := range numbers {
            total += num
        }
        return total
    }
    sum := sumAll(1, 2, 3, 4) // sum is 10
    nums := []int{5, 6, 7}
    sum = sumAll(nums...) // Pass a slice using ...
    ```

15. **Q: What is visibility of identifiers (exported vs. unexported) in Go?**
    **A:** Visibility is determined by the case of the first letter of the identifier (variable, function, type, constant, method name):
    *   **Exported (Public):** If the first letter is uppercase (e.g., `MyVariable`, `DoSomething()`), the identifier is visible and accessible from other packages.
    *   **Unexported (Private):** If the first letter is lowercase (e.g., `myVariable`, `doSomething()`), the identifier is only visible within the package it's defined in.

16. **Q: What is a package in Go? How is `package main` special?**
    **A:** A package is a collection of Go source files in the same directory that are compiled together. Packages provide code organization and reusability. Every Go file must belong to a package, declared with `package <packageName>` at the top.
    `package main` is special: It defines an executable program rather than a library. A `main` package must have a `func main()` which serves as the entry point for the executable.

17. **Q: How do you import packages in Go?**
    **A:** Using the `import` keyword.
    ```go
    import (
        "fmt"
        "math/rand"
        m "math" // aliased import
        _ "image/png" // blank import for side effects (e.g., registering a driver)
    )
    ```

18. **Q: What is the `init()` function in Go?**
    **A:** `init()` is a special function that can be defined in any package. It does not take arguments and does not return values. `init()` functions are executed automatically when a package is initialized, before `main()` (if in `package main`) or before the package is used by another. A package can have multiple `init()` functions (even in the same file), and they are executed in the order they appear to the compiler. They are typically used for setup tasks.

19. **Q: What is type inference in Go?**
    **A:** Type inference is the ability of the Go compiler to automatically deduce the type of a variable based on the value assigned to it, especially when using the short variable declaration `:=` or `var name = value`.
    `count := 10` // `count` is inferred as `int`
    `message := "hello"` // `message` is inferred as `string`

20. **Q: Are strings mutable or immutable in Go?**
    **A:** Strings in Go are immutable. Once a string is created, its contents cannot be changed. Operations that appear to modify a string actually create a new string.

---

**II. Data Structures (Arrays, Slices, Maps, Structs) (21-35)**

21. **Q: What is an array in Go?**
    **A:** An array is a fixed-size, ordered collection of elements of the same type. The size of an array is part of its type.
    ```go
    var numbers [5]int // Array of 5 integers, initialized to zero values
    names := [3]string{"Alice", "Bob", "Charlie"}
    ```
    Arrays are value types; when assigned or passed to a function, a copy is made.

22. **Q: What is a slice in Go? How is it different from an array?**
    **A:** A slice is a dynamically-sized, flexible view into the elements of an underlying array. It's more commonly used than arrays.
    Differences:
    *   **Size:** Arrays have a fixed size defined at compile time. Slices are dynamically sized.
    *   **Type:** The size is part of an array's type (`[5]int` is different from `[10]int`). Slice type only specifies the element type (`[]int`).
    *   **Flexibility:** Slices can grow or shrink using `append`.
    *   **Internals:** A slice is a descriptor containing a pointer to the underlying array, a length, and a capacity.
    *   **Passing to Functions:** Slices are reference types (conceptually, as they hold a pointer). When a slice is passed to a function, changes made to the slice's elements within the function are visible to the caller.

23. **Q: How do you create a slice in Go?**
    **A:**
    *   **From an array (slicing operation):** `myArray[low:high]`
        ```go
        arr := [5]int{1, 2, 3, 4, 5}
        s1 := arr[1:4] // s1 contains {2, 3, 4}
        ```
    *   **Slice literal:**
        ```go
        s2 := []int{10, 20, 30}
        ```
    *   **Using `make()`:** `make([]T, length, capacity)`
        ```go
        s3 := make([]int, 5)    // length 5, capacity 5
        s4 := make([]int, 0, 10) // length 0, capacity 10
        ```

24. **Q: What are length and capacity of a slice?**
    **A:**
    *   **Length (`len(s)`):** The number of elements currently in the slice.
    *   **Capacity (`cap(s)`):** The number of elements in the underlying array, starting from the first element of the slice, that can be held without reallocating memory.
    When you `append` to a slice and its length exceeds its capacity, Go will allocate a new, larger underlying array and copy the existing elements.

25. **Q: How does the `append()` function work with slices?**
    **A:** `append(slice, elements...)` adds elements to the end of a slice.
    *   If the slice's capacity is sufficient, the new elements are added in place.
    *   If the capacity is not sufficient, a new, larger underlying array is allocated, existing elements are copied, new elements are added, and `append` returns a new slice header pointing to this new array.
    It's important to assign the result of `append` back to the slice variable: `s = append(s, newElem)`.

26. **Q: What is a map in Go?**
    **A:** A map is an unordered collection of key-value pairs. Keys must be of a comparable type (e.g., strings, numbers, booleans, pointers, structs with comparable fields). Values can be of any type. Maps are reference types.
    ```go
    ages := make(map[string]int) // Create an empty map
    ages["Alice"] = 30
    ages["Bob"] = 25

    scores := map[string]int{
        "math":    90,
        "science": 85,
    }
    ```

27. **Q: How do you create and initialize a map?**
    **A:**
    *   Using `make()`: `myMap := make(map[KeyType]ValueType)`
    *   Using a map literal: `myMap := map[KeyType]ValueType{"key1": "value1", "key2": "value2"}`
    *   An uninitialized map is `nil` and cannot have keys added to it.

28. **Q: How do you access, add, update, and delete elements in a map?**
    **A:**
    *   **Add/Update:** `myMap[key] = value`
    *   **Access:** `value := myMap[key]`
    *   **Check for Existence (comma ok idiom):**
        ```go
        value, ok := myMap[key]
        if ok {
            // key exists, value is its value
        } else {
            // key does not exist, value is the zero value for ValueType
        }
        ```
    *   **Delete:** `delete(myMap, key)`

29. **Q: Are maps ordered in Go?**
    **A:** No, maps in Go are unordered. The iteration order over a map is not specified and is not guaranteed to be the same from one iteration to the next. If you need a stable iteration order, you must maintain a separate data structure (e.g., a slice of keys) that specifies the order.

30. **Q: What is a struct in Go?**
    **A:** A struct is a composite data type that groups together zero or more named values (fields) of arbitrary types. It's used to create custom data structures.
    ```go
    type Person struct {
        Name string
        Age  int
    }
    ```

31. **Q: How do you create and initialize a struct?**
    **A:**
    *   **Zero-valued struct:** `var p Person`
    *   **Using struct literal (by field name):**
        ```go
        p1 := Person{Name: "Alice", Age: 30}
        ```
    *   **Using struct literal (by field order - discouraged if fields might change):**
        ```go
        p2 := Person{"Bob", 25}
        ```
    *   **Using `new()` (returns a pointer to a zero-valued struct):**
        ```go
        p3 := new(Person) // p3 is *Person
        p3.Name = "Charlie"
        ```

32. **Q: How do you access fields of a struct?**
    **A:** Using the dot (`.`) operator. If you have a pointer to a struct, Go automatically dereferences it, so you can still use `.` notation.
    ```go
    p := Person{Name: "Alice", Age: 30}
    fmt.Println(p.Name) // Alice

    ptrP := &p
    fmt.Println(ptrP.Age) // 30 (equivalent to (*ptrP).Age)
    ```

33. **Q: What is struct embedding (anonymous fields)?**
    **A:** Struct embedding allows one struct type to be included within another struct type as an anonymous field. The fields and methods of the embedded struct are promoted to the embedding struct, as if they were declared directly on it. This provides a form of composition and can be used for code reuse (similar to inheritance in some ways, but it's not true inheritance).
    ```go
    type Contact struct {
        Email string
        Phone string
    }
    type Employee struct {
        Person  // Embedded Person struct (anonymous field)
        Contact // Embedded Contact struct
        Salary  int
    }
    emp := Employee{
        Person: Person{Name: "Eve", Age: 28},
        Contact: Contact{Email: "eve@example.com"},
        Salary: 60000,
    }
    fmt.Println(emp.Name) // Accesses emp.Person.Name
    fmt.Println(emp.Email) // Accesses emp.Contact.Email
    ```

34. **Q: What are struct tags?**
    **A:** Struct tags are string literals that can be attached to the fields of a struct. They provide metadata about the fields, which can be inspected at runtime using reflection. Common uses include specifying field names for JSON/XML marshalling/unmarshalling, ORM field mappings, or validation rules.
    ```go
    type User struct {
        ID   int    `json:"id" db:"user_id"`
        Name string `json:"name" validate:"required"`
    }
    ```

35. **Q: How do slices and maps compare in terms of performance for lookups?**
    **A:**
    *   **Maps:** Provide average O(1) time complexity for lookups, insertions, and deletions (assuming a good hash function and minimal collisions).
    *   **Slices:** Lookups by index are O(1). Lookups by value require iterating through the slice, which is O(n) on average.
    For frequent lookups by a key, maps are generally much more performant than searching through a slice.

---

**III. Pointers (36-40)**

36. **Q: What is a pointer in Go?**
    **A:** A pointer is a variable that stores the memory address of another variable. It "points" to where the data is stored.

37. **Q: How do you get the address of a variable (create a pointer)?**
    **A:** Using the address-of operator `&`.
    ```go
    x := 10
    ptrX := &x // ptrX is a pointer to x, type *int
    ```

38. **Q: How do you access the value that a pointer points to (dereferencing)?**
    **A:** Using the dereference operator `*`.
    ```go
    x := 10
    ptrX := &x
    fmt.Println(*ptrX) // Prints 10
    *ptrX = 20         // Modifies the value of x through the pointer
    fmt.Println(x)     // Prints 20
    ```

39. **Q: When would you use pointers in Go?**
    **A:**
    *   **Modifying a variable in a function:** To allow a function to modify a variable passed as an argument (Go passes arguments by value).
    *   **Efficiency with large structs:** To avoid copying large structs when passing them to functions or assigning them (though Go is efficient, and this is less of a concern than in C/C++ for many cases).
    *   **Indicating an optional value or `nil` state:** A pointer can be `nil`, while a non-pointer struct cannot represent absence in the same way.
    *   **Working with types that inherently use pointers:** Like methods with pointer receivers that modify the struct.

40. **Q: Does Go have pointer arithmetic?**
    **A:** No, Go does not support pointer arithmetic in the same way C/C++ does. This is a safety feature to prevent common memory corruption bugs. You can use the `unsafe` package for low-level pointer operations if absolutely necessary, but it's strongly discouraged for general programming.

---

**IV. Methods & Interfaces (41-50)**

41. **Q: What is a method in Go? How is it defined?**
    **A:** A method is a function that is associated with a specific type (the receiver type). The receiver appears in its own argument list between the `func` keyword and the method name.
    ```go
    type Rectangle struct {
        Width, Height float64
    }
    // Method with a value receiver
    func (r Rectangle) Area() float64 {
        return r.Width * r.Height
    }
    // Method with a pointer receiver (can modify the receiver)
    func (r *Rectangle) Scale(factor float64) {
        r.Width *= factor
        r.Height *= factor
    }
    ```

42. **Q: What is the difference between a value receiver and a pointer receiver for methods?**
    **A:**
    *   **Value Receiver (`func (t MyType) MethodName()`):**
        *   The method operates on a *copy* of the receiver value.
        *   Modifications made to the receiver within the method are not reflected in the original value.
        *   Can be called on both values and pointers of `MyType`.
    *   **Pointer Receiver (`func (t *MyType) MethodName()`):**
        *   The method operates on the *actual* receiver value (via its pointer).
        *   Modifications made to the receiver within the method are reflected in the original value.
        *   Can be called on both values (Go will automatically take the address) and pointers of `MyType`.
    **When to use which:**
    *   Use a pointer receiver if the method needs to modify the receiver.
    *   Use a pointer receiver if the struct is large, to avoid copying (though this is often a micro-optimization; consistency is key).
    *   Otherwise, a value receiver is fine. If in doubt, a pointer receiver is often a safe default for structs. For basic types, slices, maps, channels, value receivers are common.

43. **Q: What is an interface in Go?**
    **A:** An interface is a type that defines a set of method signatures. A concrete type implements an interface if it defines all the methods in that interface set. Interface satisfaction is implicit in Go (no `implements` keyword). Interfaces provide a way to specify behavior rather than concrete types.

44. **Q: How is an interface defined and implemented in Go?**
    **A:**
    ```go
    // Define an interface
    type Shape interface {
        Area() float64
        Perimeter() float64
    }

    // Implement the interface with a concrete type (e.g., Rectangle)
    type Rectangle struct {
        Width, Height float64
    }
    func (r Rectangle) Area() float64 { return r.Width * r.Height }
    func (r Rectangle) Perimeter() float64 { return 2*r.Width + 2*r.Height }

    type Circle struct {
        Radius float64
    }
    func (c Circle) Area() float64 { return math.Pi * c.Radius * c.Radius }
    func (c Circle) Perimeter() float64 { return 2 * math.Pi * c.Radius }

    // Usage:
    func printShapeInfo(s Shape) {
        fmt.Printf("Area: %f, Perimeter: %f\n", s.Area(), s.Perimeter())
    }
    // r := Rectangle{Width: 10, Height: 5}
    // c := Circle{Radius: 7}
    // printShapeInfo(r)
    // printShapeInfo(c)
    ```
    `Rectangle` and `Circle` implicitly implement `Shape` because they have `Area()` and `Perimeter()` methods with matching signatures.

45. **Q: What is the empty interface (`interface{}`) ?**
    **A:** The empty interface (`interface{}`) is an interface that specifies zero methods. Since any type has zero or more methods, *any type satisfies the empty interface*. This means a variable of type `interface{}` can hold a value of any type. It's similar to `Object` in Java or `void*` in C, but type-safe through type assertions or type switches.
    `var i interface{}`
    `i = 42`
    `i = "hello"`

46. **Q: What are type assertions in Go?**
    **A:** A type assertion is an operation applied to an interface value. It checks if the dynamic type of the interface value matches a specific concrete type or another interface type.
    `value, ok := i.(TargetType)`
    *   `i`: An interface value.
    *   `TargetType`: The type you are asserting `i` to be.
    *   `value`: If `ok` is `true`, `value` will hold the value of `i` with type `TargetType`.
    *   `ok`: A boolean, `true` if the assertion is successful, `false` otherwise. If `ok` is `false`, `value` will be the zero value for `TargetType`.
    If the `ok` form is not used (`value := i.(TargetType)`) and the assertion fails, the program will panic.

47. **Q: What is a type switch in Go?**
    **A:** A type switch is like a regular `switch` statement but operates on the dynamic type of an interface value.
    ```go
    func doSomething(i interface{}) {
        switch v := i.(type) { // v gets the value with its concrete type
        case int:
            fmt.Printf("Integer: %d\n", v)
        case string:
            fmt.Printf("String: %s\n", v)
        case bool:
            fmt.Printf("Boolean: %t\n", v)
        default:
            fmt.Printf("Unknown type: %T\n", v) // %T prints the type
        }
    }
    ```

48. **Q: Can you embed an interface within another interface?**
    **A:** Yes, this is a common way to compose interfaces. The embedding interface will include all methods from the embedded interface.
    ```go
    type Reader interface { Read(p []byte) (n int, err error) }
    type Writer interface { Write(p []byte) (n int, err error) }
    type ReadWriter interface {
        Reader // Embeds Reader interface
        Writer // Embeds Writer interface
    }
    ```

49. **Q: When should you use an interface?**
    **A:**
    *   To define contracts or expected behavior for different types.
    *   To write functions that can operate on multiple different types that share common behavior.
    *   To decouple parts of your system (e.g., abstracting away database implementations).
    *   For dependency injection and easier testing (by mocking dependencies).
    *   The Go proverb: "Accept interfaces, return structs."

50. **Q: Explain the Go proverb: "Accept interfaces, return structs."**
    **A:** This proverb suggests a design principle:
    *   **Accept Interfaces:** When writing functions or methods, make parameters interface types if you only need a subset of behavior from the input. This makes your function more flexible and reusable, as it can accept any type that satisfies the interface.
    *   **Return Structs (Concrete Types):** When returning values, return concrete types (like structs or pointers to structs). This gives the caller the full functionality of that concrete type and avoids forcing them to deal with interface values unnecessarily if they need the specific type. The caller can always assign a concrete type to an interface variable if needed.

---

**V. Concurrency (Goroutines, Channels, Mutexes) (51-65)**

51. **Q: What is a goroutine? How do you create one?**
    **A:** A goroutine is a lightweight, concurrently executing function managed by the Go runtime. Goroutines run in the same address space, so access to shared memory must be synchronized.
    You create one by prefixing a function call with the `go` keyword:
    `go myFunction(arg1, arg2)`
    `go func() { /* anonymous function */ }()`

52. **Q: How are goroutines different from OS threads?**
    **A:**
    *   **Lightweight:** Goroutines are much cheaper to create and manage than OS threads (smaller stacks, faster creation/destruction). A single OS thread can run many goroutines.
    *   **Managed by Go Runtime:** The Go runtime scheduler multiplexes goroutines onto a pool of OS threads.
    *   **Communication:** Goroutines often communicate via channels, promoting cleaner concurrent code ("share memory by communicating" rather than "communicate by sharing memory").

53. **Q: What are channels in Go?**
    **A:** Channels are typed conduits through which you can send and receive values between goroutines, providing synchronization and communication. The zero value of a channel is `nil`.

54. **Q: How do you create a channel? Differentiate between buffered and unbuffered channels.**
    **A:**
    *   **Create:** `ch := make(chan Type)` (unbuffered) or `ch := make(chan Type, bufferSize)` (buffered).
    *   **Unbuffered Channel (`make(chan int)`):**
        *   Requires both a sender and a receiver to be ready simultaneously for communication to occur (it's synchronous).
        *   If a sender sends and no receiver is ready, the sender blocks.
        *   If a receiver receives and no sender is ready, the receiver blocks.
    *   **Buffered Channel (`make(chan int, N)`):**
        *   Has a capacity `N`.
        *   Sends to a buffered channel block only if the buffer is full.
        *   Receives from a buffered channel block only if the buffer is empty.
        *   Allows a limited number of values to be sent without a corresponding receiver being immediately ready.

55. **Q: How do you send and receive values on a channel?**
    **A:**
    *   **Send:** `channelName <- value`
    *   **Receive:** `variable := <-channelName`
    *   Receive with check if channel is closed: `value, ok := <-channelName` (`ok` is `false` if channel is closed and empty).

56. **Q: How do you close a channel? Why is it important? Who should close it?**
    **A:**
    *   **Close:** `close(channelName)`
    *   **Importance:** Closing a channel signals that no more values will be sent on it. This is important for receivers using `for...range` on a channel to know when to stop, or for receivers using the `value, ok := <-ch` form to detect closure.
    *   **Who Closes:** Only the *sender* should close a channel. Closing a channel more than once will panic. Sending on a closed channel will panic. Receiving from a closed channel will always return the zero value for the channel's type immediately (with `ok == false`).

57. **Q: What is the `select` statement in Go used for?**
    **A:** The `select` statement lets a goroutine wait on multiple channel operations simultaneously. It blocks until one of its cases can run, then it executes that case. If multiple cases are ready, it chooses one at random.
    It's often used to:
    *   Handle messages from multiple channels.
    *   Implement timeouts or non-blocking operations on channels using a `default` case or a timer channel.
    ```go
    select {
    case msg1 := <-ch1:
        fmt.Println("Received from ch1:", msg1)
    case msg2 := <-ch2:
        fmt.Println("Received from ch2:", msg2)
    case <-time.After(1 * time.Second): // Timeout
        fmt.Println("Timeout")
    default: // Non-blocking operation (if no other case is ready)
        fmt.Println("No communication ready")
    }
    ```

58. **Q: What is `sync.Mutex`? When would you use it?**
    **A:** `sync.Mutex` (Mutual Exclusion lock) is used to protect shared resources from concurrent access by multiple goroutines. It provides `Lock()` and `Unlock()` methods. Only one goroutine can hold the lock at any given time.
    Use it when multiple goroutines need to read and write shared data to prevent race conditions.

59. **Q: What is `sync.RWMutex`? How is it different from `sync.Mutex`?**
    **A:** `sync.RWMutex` (Read-Write Mutex) allows multiple readers to access a shared resource concurrently, or a single writer to access it exclusively.
    *   `RLock()` / `RUnlock()`: For read access. Multiple goroutines can hold a read lock simultaneously.
    *   `Lock()` / `Unlock()`: For write access. Only one goroutine can hold a write lock, and no other readers or writers can acquire locks.
    Different from `sync.Mutex` because it distinguishes between read and write operations. Use it when you have a shared resource that is read much more frequently than it is written, as it can improve concurrency for readers.

60. **Q: What is `sync.WaitGroup`? How is it used?**
    **A:** `sync.WaitGroup` is used to wait for a collection of goroutines to finish executing.
    Methods:
    *   `Add(delta int)`: Increments the WaitGroup counter by `delta`.
    *   `Done()`: Decrements the WaitGroup counter by one (usually called by a goroutine when it finishes, often via `defer wg.Done()`).
    *   `Wait()`: Blocks until the WaitGroup counter becomes zero.
    ```go
    var wg sync.WaitGroup
    for i := 0; i < 5; i++ {
        wg.Add(1) // Increment counter before starting goroutine
        go func(id int) {
            defer wg.Done() // Decrement counter when goroutine finishes
            fmt.Println("Worker", id, "done")
            time.Sleep(100 * time.Millisecond)
        }(i)
    }
    wg.Wait() // Wait for all goroutines to finish
    fmt.Println("All workers completed")
    ```

61. **Q: What is a race condition in concurrent programming? How can you detect it in Go?**
    **A:** A race condition occurs when two or more goroutines access shared data concurrently, and at least one of them modifies the data. The final outcome depends on the unpredictable order in which their operations are interleaved. This can lead to subtle and hard-to-debug bugs.
    Detect in Go: Use the `-race` flag with Go tools:
    `go run -race main.go`
    `go test -race`
    `go build -race`
    The race detector will report data races at runtime.

62. **Q: Explain the Go concurrency philosophy: "Share memory by communicating, don't communicate by sharing memory."**
    **A:** This philosophy encourages using channels for communication and synchronization between goroutines as the primary means of sharing data, rather than relying heavily on traditional shared memory with locks (mutexes).
    *   **Share memory by communicating:** Send data (or ownership of data) over channels. This often leads to cleaner, less error-prone concurrent code because the synchronization is inherent in the channel operations.
    *   **Communicate by sharing memory:** Use mutexes or other synchronization primitives to protect access to shared data structures. This is sometimes necessary but can be more complex to get right.
    Go supports both, but channels are often preferred for their higher-level abstraction.

63. **Q: What happens if you send to a `nil` channel or receive from a `nil` channel?**
    **A:** Sending to or receiving from a `nil` channel will block forever. This can be a source of deadlocks if not handled carefully.

64. **Q: What happens if you try to send to a closed channel?**
    **A:** Sending to a closed channel will cause a panic.

65. **Q: What happens if you try to receive from a closed channel?**
    **A:** Receiving from a closed channel will succeed immediately, returning the zero value for the channel's element type. The second return value from the receive operation (`value, ok := <-ch`) will be `false` to indicate the channel is closed and the value is a zero value because of closure.

---

**VI. Error Handling (66-70)**

66. **Q: How does Go handle errors? What is the `error` type?**
    **A:** Go handles errors by having functions return an additional value of type `error` as their last return value. If there's no error, this value is `nil`. If an error occurs, it contains an error object describing the problem. Callers are expected to explicitly check this error value.
    The `error` type is a built-in interface:
    ```go
    type error interface {
        Error() string
    }
    ```
    Any type that implements the `Error() string` method satisfies the `error` interface.

67. **Q: What is the idiomatic way to check for errors in Go?**
    **A:**
    ```go
    value, err := someFunctionThatReturnsAnError()
    if err != nil {
        // Handle the error (e.g., log it, return it, panic)
        log.Printf("Error: %v", err)
        return err // Or handle appropriately
    }
    // Proceed with using 'value'
    ```

68. **Q: How can you create custom error types in Go?**
    **A:** By defining a struct type and making it implement the `error` interface (i.e., by giving it an `Error() string` method).
    ```go
    type MyCustomError struct {
        Code    int
        Message string
    }

    func (e *MyCustomError) Error() string {
        return fmt.Sprintf("Error %d: %s", e.Code, e.Message)
    }

    func doSomethingRisky() error {
        // ...
        return &MyCustomError{Code: 123, Message: "Something specific went wrong"}
    }
    ```

69. **Q: Explain error wrapping in Go (Go 1.13+). What are `errors.Is` and `errors.As`?**
    **A:** Error wrapping allows you to create a new error that contains (wraps) an original underlying error, providing additional context without losing the original error information. This is done using `fmt.Errorf` with the `%w` verb.
    *   **`errors.Is(err, target error)`:** Checks if `err` (or any error in its chain of wrapped errors) is equivalent to `target`. Useful for checking against sentinel errors.
    *   **`errors.As(err, target interface{})`:** Checks if `err` (or any error in its chain) can be assigned to `target` (which must be a pointer to an error type). If so, it sets `target` to that error value and returns `true`. Useful for inspecting and acting upon specific custom error types.
    ```go
    // Original error
    errDB := errors.New("database connection failed")

    // Wrapping error
    func connect() error {
        // ... try to connect ...
        if failed {
            return fmt.Errorf("failed to connect to service: %w", errDB)
        }
        return nil
    }

    err := connect()
    if errors.Is(err, errDB) {
        fmt.Println("It was a database connection failure.")
    }

    var customErr *MyCustomError
    if errors.As(err, &customErr) {
        fmt.Println("Custom error code:", customErr.Code)
    }
    ```

70. **Q: What is `panic` and `recover`? When should they be used?**
    **A:**
    *   **`panic(value)`:** A built-in function that stops the normal execution flow of the current goroutine. When a function panics, its execution stops, any deferred functions are executed, and then the goroutine exits. If not recovered, the panic propagates up the call stack, and if it reaches the top-level goroutine of the program, the program crashes.
    *   **`recover()`:** A built-in function that regains control of a panicking goroutine. `recover` is only useful inside deferred functions. If the current goroutine is panicking, a call to `recover` captures the value passed to `panic` and resumes normal execution. If the goroutine is not panicking, `recover` returns `nil`.
    **When to use:**
    *   `panic` should generally be reserved for truly exceptional, unrecoverable errors that indicate a bug in the program (e.g., out-of-bounds array access, nil pointer dereference). It's not idiomatic Go to use `panic` for ordinary error handling.
    *   `recover` can be used in a deferred function to gracefully handle a panic within a specific part of a program (e.g., a server handling a request, to prevent one panicking request from crashing the whole server) or in libraries to turn panics into returned errors.

---

**VII. Packages, Modules & Standard Library (71-85)**

71. **Q: What are Go Modules? What problems do they solve?**
    **A:** Go Modules are Go's official dependency management system, introduced in Go 1.11.
    Problems they solve:
    *   **Dependency Versioning:** Allows projects to specify exact versions of their dependencies.
    *   **Reproducible Builds:** Ensures that builds are consistent across different environments and times.
    *   **No more `GOPATH` requirement for project location:** Projects can live outside the traditional `GOPATH/src` directory.
    Key files: `go.mod` (defines the module path, Go version, and dependencies) and `go.sum` (contains checksums of dependencies for security and integrity).

72. **Q: What is the `go.mod` file?**
    **A:** The `go.mod` file is the manifest for a Go module. It's located at the root of the module's directory tree. It defines:
    *   The module's path (its import path).
    *   The version of Go the module was written for.
    *   The `require` directive, listing direct dependencies and their versions.
    *   The `replace` directive (optional, for using local or forked versions of dependencies).
    *   The `exclude` directive (optional, for excluding specific versions of dependencies).

73. **Q: What is the `go.sum` file?**
    **A:** The `go.sum` file contains the expected cryptographic checksums (hashes) of the content of specific module versions. It's used to verify that the downloaded dependencies have not been tampered with, ensuring build integrity and security. It includes checksums for both direct and indirect dependencies.

74. **Q: How do you add a new dependency to your Go module?**
    **A:** Typically, you just `import` the package in your Go code. The next time you run `go build`, `go test`, or `go mod tidy`, the Go toolchain will automatically find the dependency, resolve its version, and add it to your `go.mod` and `go.sum` files.
    You can also use `go get <package-path>@<version>` to explicitly add or update a dependency.

75. **Q: What is the `fmt` package used for? List a few common functions.**
    **A:** The `fmt` package implements formatted I/O, similar to C's `printf` and `scanf`.
    Common functions:
    *   `fmt.Println(...)`: Prints arguments to standard output, followed by a newline.
    *   `fmt.Printf(format string, ...)`: Prints formatted output to standard output.
    *   `fmt.Sprintf(format string, ...)`: Returns a formatted string.
    *   `fmt.Errorf(format string, ...)`: Returns an error formatted according to a format specifier.
    *   `fmt.Scanln(...)`: Reads input from standard input.

76. **Q: What is the `os` package used for? List a few common functions.**
    **A:** The `os` package provides a platform-independent interface to operating system functionality.
    Common functions:
    *   `os.Args`: Command-line arguments.
    *   `os.Getenv(key string)` / `os.Setenv(key, value string)`: Environment variables.
    *   `os.ReadFile(name string)` / `os.WriteFile(name string, data []byte, perm FileMode)`: File reading/writing.
    *   `os.Create(name string)` / `os.Open(name string)` / `os.Stat(name string)`: File operations.
    *   `os.Mkdir(name string, perm FileMode)` / `os.Remove(name string)`: Directory operations.
    *   `os.Exit(code int)`: Exits the program.

77. **Q: What are `io.Reader` and `io.Writer` interfaces? Why are they important?**
    **A:**
    *   `io.Reader`: An interface representing the read end of a stream of data. It has a single method: `Read(p []byte) (n int, err error)`.
    *   `io.Writer`: An interface representing the write end of a stream of data. It has a single method: `Write(p []byte) (n int, err error)`.
    **Importance:** They are fundamental interfaces for I/O operations in Go. Many standard library packages and third-party libraries work with these interfaces, allowing for flexible and composable I/O handling (e.g., reading from a file, a network connection, or an in-memory buffer using the same functions if they accept an `io.Reader`). `io.Copy(dst Writer, src Reader)` is a prime example.

78. **Q: How do you work with JSON data in Go (marshalling and unmarshalling)?**
    **A:** Using the `encoding/json` package:
    *   **Marshalling (Go struct to JSON string/bytes):** `json.Marshal(v interface{}) ([]byte, error)`
        ```go
        type Person struct { Name string `json:"name"`; Age int `json:"age"` }
        p := Person{Name: "Alice", Age: 30}
        jsonData, err := json.Marshal(p)
        // jsonData is []byte
        ```
    *   **Unmarshalling (JSON string/bytes to Go struct):** `json.Unmarshal(data []byte, v interface{}) error`
        ```go
        jsonData := []byte(`{"name":"Bob","age":25}`)
        var p Person
        err := json.Unmarshal(jsonData, &p)
        // p is now {Name:"Bob", Age:25}
        ```
    Struct field tags (like ``json:"name"``) control how fields are encoded/decoded. Only exported struct fields are marshalled/unmarshalled.

79. **Q: How do you make HTTP GET and POST requests in Go using the `net/http` package?**
    **A:**
    *   **GET Request:**
        ```go
        resp, err := http.Get("https://api.example.com/data")
        if err != nil { /* handle error */ }
        defer resp.Body.Close()
        body, err := io.ReadAll(resp.Body)
        // process body
        ```
    *   **POST Request (with JSON body):**
        ```go
        jsonData := []byte(`{"key":"value"}`)
        reqBody := bytes.NewBuffer(jsonData)
        resp, err := http.Post("https://api.example.com/submit", "application/json", reqBody)
        if err != nil { /* handle error */ }
        defer resp.Body.Close()
        // process response
        ```
    For more control (custom headers, different methods), use `http.NewRequest` and `http.DefaultClient.Do(req)`.

80. **Q: How do you create a simple HTTP server in Go using `net/http`?**
    **A:**
    ```go
    func helloHandler(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, World!")
    }

    func main() {
        http.HandleFunc("/", helloHandler) // Register handler for a route
        fmt.Println("Server starting on port 8080...")
        err := http.ListenAndServe(":8080", nil) // Start server, nil uses DefaultServeMux
        if err != nil {
            log.Fatal("ListenAndServe: ", err)
        }
    }
    ```

81. **Q: What is the `context` package used for in Go?**
    **A:** The `context` package provides a way to carry deadlines, cancellation signals, and other request-scoped values across API boundaries and between goroutines.
    Key uses:
    *   **Cancellation:** Signal goroutines to stop work when an operation is cancelled (e.g., user cancels a request, timeout occurs).
    *   **Deadlines/Timeouts:** Set a maximum time for an operation to complete.
    *   **Passing Request-Scoped Values:** Carry values like request IDs or user authentication info (use with caution to avoid making it a general-purpose parameter passing mechanism).
    `context.Background()` is often the starting point for a context. `context.WithCancel()`, `context.WithDeadline()`, `context.WithTimeout()`, `context.WithValue()` create derived contexts.

82. **Q: How does the `defer` statement work in Go? What are its common use cases?**
    **A:** A `defer` statement schedules a function call (the deferred function) to be executed just before the surrounding function returns, either normally or through a panic. Deferred calls are executed in Last-In, First-Out (LIFO) order.
    Common use cases:
    *   **Resource Cleanup:** Closing files, unlocking mutexes, closing network connections.
        ```go
        f, err := os.Open("file.txt")
        if err != nil { /* ... */ }
        defer f.Close() // Guaranteed to run before function returns
        ```
    *   Modifying named return values.
    *   Recovering from panics.
    Arguments to deferred functions are evaluated when the `defer` statement is executed, not when the call is made.

83. **Q: What is the `time` package used for? How do you format time?**
    **A:** The `time` package provides time-related functionalities: getting current time, measuring durations, formatting and parsing times.
    Formatting and Parsing: Go uses a unique reference-based layout string: `Mon Jan 2 15:04:05 MST 2006` (or `01/02 03:04:05PM '06 -0700`).
    ```go
    now := time.Now()
    fmt.Println(now.Format("2006-01-02 15:04:05")) // Format time
    dateStr := "2023-10-26T10:00:00Z"
    parsedTime, err := time.Parse(time.RFC3339, dateStr) // Parse time string
    ```

84. **Q: What is the `strings` package useful for?**
    **A:** The `strings` package provides many utility functions for manipulating UTF-8 encoded strings.
    Examples: `strings.Contains()`, `strings.HasPrefix()`, `strings.HasSuffix()`, `strings.Index()`, `strings.Join()`, `strings.Split()`, `strings.ToLower()`, `strings.ToUpper()`, `strings.TrimSpace()`.

85. **Q: What is the `strconv` package useful for?**
    **A:** The `strconv` package implements conversions to and from string representations of basic data types.
    Examples:
    *   `strconv.Atoi(s string) (int, error)`: String to int.
    *   `strconv.Itoa(i int) string`: Int to string.
    *   `strconv.ParseBool(str string) (bool, error)`
    *   `strconv.ParseFloat(s string, bitSize int) (float64, error)`
    *   `strconv.FormatBool(b bool) string`
    *   `strconv.FormatFloat(f float64, fmt byte, prec, bitSize int) string`

---

**VIII. Testing & Tooling (86-95)**

86. **Q: How do you write a unit test in Go?**
    **A:** Unit tests are written in files ending with `_test.go` in the same package as the code they are testing. Test functions must:
    *   Start with `Test` (e.g., `TestMyFunction`).
    *   Take one argument: `t *testing.T`.
    *   Call `t.Errorf()` or `t.Fatalf()` to report failures.
    ```go
    // mycode.go
    package mypkg
    func Add(a, b int) int { return a + b }

    // mycode_test.go
    package mypkg
    import "testing"
    func TestAdd(t *testing.T) {
        result := Add(2, 3)
        expected := 5
        if result != expected {
            t.Errorf("Add(2, 3) = %d; want %d", result, expected)
        }
    }
    ```
    Run tests with `go test`.

87. **Q: What is table-driven testing in Go?**
    **A:** A common pattern for writing tests where you define a slice of test cases (structs), each containing inputs and expected outputs. You then iterate over this slice and run the test logic for each case. This makes it easy to add more test cases and keep the test logic concise.
    ```go
    func TestAddTable(t *testing.T) {
        tests := []struct {
            name    string
            a, b    int
            want    int
        }{
            {"positive numbers", 2, 3, 5},
            {"negative numbers", -1, -2, -3},
            {"zero", 0, 5, 5},
        }
        for _, tt := range tests {
            t.Run(tt.name, func(t *testing.T) { // Sub-test for better output
                if got := Add(tt.a, tt.b); got != tt.want {
                    t.Errorf("Add() = %v, want %v", got, tt.want)
                }
            })
        }
    }
    ```

88. **Q: How do you write a benchmark test in Go?**
    **A:** Benchmark functions start with `Benchmark` and take `b *testing.B` as an argument. The code to be benchmarked is run inside a loop `for i := 0; i < b.N; i++`. `b.N` is adjusted by the testing framework to achieve statistically significant results.
    ```go
    func BenchmarkAdd(b *testing.B) {
        for i := 0; i < b.N; i++ {
            Add(100, 200)
        }
    }
    ```
    Run with `go test -bench=.` (or `-bench=FunctionName`).

89. **Q: What is an example test in Go (using `Example` functions)?**
    **A:** Example functions (starting with `Example`) serve as documentation and are also compiled and run as part of `go test`. If the function prints to standard output, the output is compared against an `// Output:` comment at the end of the function. They demonstrate how to use a function or package.
    ```go
    func ExampleAdd() {
        sum := Add(1, 2)
        fmt.Println(sum)
        // Output: 3
    }
    ```

90. **Q: What does `go fmt` do?**
    **A:** `go fmt` is a command that automatically formats Go source code according to Go's standard style guidelines. It helps maintain consistent code style across projects and teams. It's common practice to run `go fmt` before committing code.

91. **Q: What does `go vet` do?**
    **A:** `go vet` is a tool that examines Go source code and reports suspicious constructs, such as `Printf` calls whose arguments do not align with the format string, or methods that should have been called but were not. It helps catch common mistakes that might not be caught by the compiler.

92. **Q: What are build tags (or build constraints) in Go?**
    **A:** Build tags are special comments at the top of a Go source file (before the `package` declaration) that control which files are included during a build. They allow conditional compilation based on factors like operating system, architecture, or custom tags.
    Syntax: `//go:build tag1 && (tag2 || tag3)` or older `// +build tag`
    Example:
    `//go:build linux && amd64` (file compiled only for Linux on amd64)
    `//go:build customtag` (file compiled if `customtag` is provided via `-tags` flag to `go build`)

93. **Q: How do you build an executable in Go? How do you cross-compile?**
    **A:**
    *   **Build:** `go build [package-path]` (e.g., `go build .` or `go build main.go`). This creates an executable in the current directory.
    *   **Cross-compile:** Set `GOOS` (target operating system) and `GOARCH` (target architecture) environment variables before running `go build`.
        ```bash
        GOOS=linux GOARCH=amd64 go build -o myapp_linux main.go
        GOOS=windows GOARCH=amd64 go build -o myapp_windows.exe main.go
        ```

94. **Q: What is `go run` used for?**
    **A:** `go run` compiles and runs the specified Go program (main package) directly without producing a separate executable file in the current directory (it builds it in a temporary location). It's convenient for quickly running small programs or scripts during development.

95. **Q: What is the purpose of `GOPATH` and `GOROOT` environment variables? How has their role changed with Go Modules?**
    **A:**
    *   **`GOROOT`:** Specifies the location of your Go installation (where the standard library and Go tools reside). Usually set automatically.
    *   **`GOPATH`:** (Historically) Specified the root of your workspace. It contained `src/` (source code), `pkg/` (compiled package objects), and `bin/` (compiled executables).
    **Change with Go Modules:** With Go Modules (Go 1.11+), `GOPATH` is no longer the primary location for your project source code. Projects can reside anywhere. `GOPATH` is now mainly used for:
        *   Default location for `go install` to place binaries.
        *   Default location for `go get` to download non-module source code (though `go get` outside a module is less common now).
        *   Go module cache is typically at `$GOPATH/pkg/mod`.

---

**IX. Best Practices & Idioms (96-100)**

96. **Q: What are some general best practices for writing idiomatic Go code?**
    **A:**
    *   **Simplicity and Clarity:** Write straightforward, readable code.
    *   **Effective Error Handling:** Explicitly check and handle errors.
    *   **Concurrency with Goroutines and Channels:** Use them appropriately for concurrent tasks.
    *   **Small Interfaces:** Define interfaces with few methods.
    *   **Code Formatting:** Use `go fmt`.
    *   **Package Organization:** Keep packages focused and well-named.
    *   **Avoid Global State:** Minimize global variables.
    *   **Write Tests:** Unit tests, benchmarks, examples.
    *   **Proper Naming Conventions:** (e.g., `camelCase` for local variables, `PascalCase` for exported identifiers).

97. **Q: Why is it generally discouraged to use global variables in Go?**
    **A:**
    *   **Reduced Readability/Maintainability:** Harder to track where global variables are modified.
    *   **Increased Coupling:** Components become implicitly dependent on global state.
    *   **Concurrency Issues:** Unsynchronized access to global variables by multiple goroutines can lead to race conditions.
    *   **Testing Difficulties:** Makes unit testing harder as global state can affect test outcomes.
    It's better to pass dependencies explicitly.

98. **Q: What is meant by "don't just check errors, handle them gracefully"?**
    **A:** Simply checking `if err != nil` is not enough. You need to decide what to do when an error occurs:
    *   **Return the error:** Propagate it up the call stack, possibly wrapping it with more context.
    *   **Retry the operation:** If it's a transient error.
    *   **Log the error and continue (if possible):** If the error is non-critical.
    *   **Log the error and exit:** If the error is fatal to the application.
    *   **Provide a fallback or default behavior.**
    The goal is to make the program robust and provide meaningful feedback or recovery paths.

99. **Q: What are some common pitfalls to avoid in Go concurrency?**
    **A:**
    *   **Race Conditions:** Unsynchronized access to shared memory.
    *   **Deadlocks:** Goroutines waiting for each other indefinitely (e.g., sending to an unbuffered channel with no receiver, or acquiring locks in inconsistent order).
    *   **Goroutine Leaks:** Goroutines that start but never terminate because they are blocked indefinitely (e.g., waiting on a channel that will never receive or be closed).
    *   **Starvation:** A goroutine is perpetually denied access to a shared resource.
    *   **Incorrect Channel Usage:** Closing channels by receivers, sending on closed channels, `nil` channel operations.

100. **Q: What is the Go Playground, and what is it useful for?**
     **A:** The Go Playground (play.golang.org) is an online environment where you can write, compile, and run small Go programs directly in your browser without installing Go locally.
     Useful for:
     *   Quickly testing small code snippets.
     *   Learning Go syntax and features.
     *   Sharing runnable Go code examples easily (it provides a shareable link).
     *   Experimenting with standard library packages.
     It has limitations (e.g., no file system access, network access is restricted, execution time limits).

---

This list covers a wide range of Go topics. I hope it's helpful!