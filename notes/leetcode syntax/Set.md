
# JAVA
```java
import java.util.HashSet; // Import the HashSet class

HashSet<String> cars = new HashSet<String>();

cars.add("bmw")
//  Check If an Item Exists

cars.contains("Mazda");

// remove an item 
cars.remove("bmw")

//remove all item 
cars.clear()

// get size 
cars.size()
```


# GoLang  
```go
package main

import "fmt"

func main() {

    // Create a set using a map (Go doesn't have built-in sets)
    cars := make(map[string]struct{})

    // Add an item
    cars["BMW"] = struct{}{}

    // Check if an item exists
    _, exists := cars["Mazda"]

    // Remove an item
    delete(cars, "BMW")

    // Remove all items
    cars = make(map[string]struct{})

    // Get size
    size := len(cars)

    fmt.Println("Size:", size)
}
```

