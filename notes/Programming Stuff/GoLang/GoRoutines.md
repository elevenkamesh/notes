## Steps for goroutines
- Create a function with `go` keyword
- Create a `sync.waitgroup` and add your goroutine using `waitgroup.Add(1|number of goroutines)`
- In your routine, do a `defer waitgroup.Done()`
- Finally in the function calling all these routines, do a `waitgroup.Wait()`