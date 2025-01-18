# Java
```java
Import HashMap import java.util.HashMap; HashMap<KeyType, ValueType> map = new HashMap<>(); 
// Example:
HashMap<String, Integer> map = new HashMap<>(); 
map.put("key", 1);
int value = map.get("key"); 
map.remove("key");
```
# Golang

```go
map := make(map[KeyType]ValueType)
// Example: 
map := make(map[string]int)

 a := map[int]bool{}

map["key"] = 1
value, exists := map["key"]
delete(map, "key")
```

# python

```python
map = {}

# Example:
map["key"] = 1
value = map.get("key") 
del map["key"]
```

```js
// JavaScript uses an object or Map
const map = new Map();

// Example:
map.set("key", 1);
const value = map.get("key");
map.delete("key");
```