In Go, the `context` package is used to manage and carry deadlines, cancellation signals, and request-scoped values across API boundaries and between processes. It is especially useful in concurrent programming where multiple goroutines are involved and need a way to communicate or be controlled.
[YouTube Video on Contexts in GoLang](https://www.youtube.com/watch?v=h2RdcrMLQAo)
### Why We Use Context in Go

1. **Cancellation Propagation**
	- When you have a hierarchy of goroutines, you may want to cancel all related operations if the parent operation is canceled or if a timeout is reached.
	- `context` helps propagate cancellation signals to all goroutines derived from the same `context.Context`.
	- Example:
		```go
		package main
		
		import (
		    "context"
		    "fmt"
		    "time"
		)
		
		func main() {
		    ctx, cancel := context.WithCancel(context.Background())
		
		    go func(ctx context.Context) {
		        for {
		            select {
		            case <-ctx.Done():
		                fmt.Println("Goroutine stopped")
		                return
		            default:
		                fmt.Println("Working...")
		                time.Sleep(500 * time.Millisecond)
		            }
		        }
		    }(ctx)
		
		    time.Sleep(2 * time.Second)
		    cancel() // Send cancellation signal
		    time.Sleep(1 * time.Second)
		}
		```
		Output:
		```bash
		Working...
		Working...
		Working...
		Working...
		Goroutine stopped
		```

1. **Timeouts and Deadlines**
	- Context can enforce time limits on operations, preventing them from running indefinitely.
	- `context.WithTimeout` or `context.WithDeadline` creates a context that automatically cancels itself after a specified duration or at a specified time.
	- Example:
		```go
		package main
		
		import (
		    "context"
		    "fmt"
		    "time"
		)
		
		func main() {
		    ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
		    defer cancel() // Ensure resources are released
		
		    select {
		    case <-time.After(5 * time.Second):
		        fmt.Println("Operation completed")
		    case <-ctx.Done():
		        fmt.Println("Timeout:", ctx.Err())
		    }
		}
		```
		Output:
		```bash
		Timeout: context deadline exceeded
		```

3. **Passing Request-Scoped Values**
	- `context` allows you to pass values through an application in a request-scoped manner, making it easier to share metadata (like authentication tokens, user IDs, etc.) across function calls.
	- Example:
		```go
		package main
		
		import (
		    "context"
		    "fmt"
		)
		
		func main() {
		    ctx := context.WithValue(context.Background(), "userID", 42)
		
		    processRequest(ctx)
		}
		
		func processRequest(ctx context.Context) {
		    userID := ctx.Value("userID").(int)
		    fmt.Println("Processing request for user:", userID)
		}
		```
		Output:
		```bash
		Processing request for user: 42
		```