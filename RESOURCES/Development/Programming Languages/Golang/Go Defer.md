In Go (Golang), the `defer` keyword is used to schedule a function call to be executed _after_ the surrounding function completes. This is incredibly useful for tasks like releasing resources (closing files, unlocking mutexes, etc.) or ensuring that cleanup actions are performed, regardless of how the function exits (normally or due to a panic).

Here's a breakdown of how `defer` works and some examples:

**1. Basic Usage:**

The `defer` keyword is placed before a function call. The deferred function will be executed when the surrounding function returns, either through a `return` statement or by reaching the end of its body.

Go

```
package main

import "fmt"

func myFunction() {
    defer fmt.Println("This is deferred") // This will print last
    fmt.Println("This is executed first")
}

func main() {
    myFunction()
}

// Output:
// This is executed first
// This is deferred
```

**2. Multiple `defer` Statements:**

If you have multiple `defer` statements in a function, they are executed in **Last-In-First-Out (LIFO)** order. This means the last `defer` statement you write will be the first one executed when the function returns.

Go

```
package main

import "fmt"

func myFunction() {
    defer fmt.Println("Deferred 1") // Executed last
    defer fmt.Println("Deferred 2") // Executed second
    defer fmt.Println("Deferred 3") // Executed first
    fmt.Println("Function body")
}

func main() {
    myFunction()
}

// Output:
// Function body
// Deferred 3
// Deferred 2
// Deferred 1
```

**3. `defer` with Function Arguments:**

The arguments to a deferred function are evaluated _when the `defer` statement is executed_, not when the deferred function is eventually called. This is important to understand, especially when dealing with variables that might change.

Go

```
package main

import "fmt"

func myFunction() {
    i := 10
    defer fmt.Println("Deferred value:", i) // i is evaluated here (10)
    i++
    fmt.Println("Current value:", i) // i is now 11
}

func main() {
    myFunction()
}

// Output:
// Current value: 11
// Deferred value: 10
```

**4. Common Use Cases:**

- **Closing Files:**
    
    Go
    
    ```
    f, err := os.Open("myfile.txt")
    if err != nil {
        // Handle error
    }
    defer f.Close() // Ensure the file is always closed
    
    // ... use the file ...
    ```
    
- **Unlocking Mutexes:**
    
    Go
    
    ```
    mutex.Lock()
    defer mutex.Unlock() // Ensure the mutex is always unlocked
    
    // ... access shared resource ...
    ```
    
- **Recovering from Panics:**
    
    `defer` can be used in conjunction with the `recover()` function to handle panics.
    
    Go
    
    ```
    package main
    
    import "fmt"
    
    func mightPanic() {
        panic("Something went wrong!")
    }
    
    func myFunction() {
        defer func() {
            if r := recover(); r != nil {
                fmt.Println("Recovered from panic:", r)
            }
        }()
    
        mightPanic()
        fmt.Println("This will not be printed if panic occurs.")
    }
    
    func main() {
        myFunction()
    }
    // Output:
    // Recovered from panic: Something went wrong!
    ```
    

**5. `defer` in Loops:**

Be cautious when using `defer` inside loops. Because deferred functions are executed at the end of the _surrounding function_, using `defer` in a loop can lead to resource exhaustion if you're, for example, opening files in each iteration. The files would only be closed after the entire loop and the surrounding function finished. In such cases, it's better to handle resource management within the loop itself.

Go

```
package main

import (
    "fmt"
    "os"
)

func main() {
    filenames := []string{"file1.txt", "file2.txt", "file3.txt"}

    for _, filename := range filenames {
        f, err := os.Open(filename)
        if err != nil {
            fmt.Println("Error opening file:", err)
            continue // Important: continue to the next iteration
        }
        //Do not defer f.Close() here.
        //Instead close it right after using it.
        //Deferring in a loop means that all files will be closed only
        //after the for loop is finished.
        fmt.Println("Processing", filename)
        f.Close()
    }
}
```

`defer` is a powerful tool in Go for ensuring cleanup and handling panics. Using it correctly leads to more robust and maintainable code.