# Complete Guide to Maps in Go and Java: LeetCode Patterns

## Table of Contents
1. Map Basics
2. Creation and Initialization
3. Basic Operations (CRUD)
4. Common Patterns
5. Performance and Memory
6. Thread Safety
7. LeetCode Problem Patterns
8. Best Practices
9. Common Pitfalls

## 1. Map Basics

In both Go and Java, maps (or HashMaps) are key-value data structures that provide fast lookups. The main difference is that Go has maps as a built-in type, while Java implements them as part of its Collections framework.

## 2. Creation and Initialization

### Go Map Creation
```go
// Method 1: Using make
scoreMap := make(map[string]int)

// Method 2: Map literal
scoreMap := map[string]int{
    "alice": 100,
    "bob":   85,
}

// Method 3: Nil map declaration (can't add items until initialized)
var scoreMap map[string]int
```

### Java HashMap Creation
```java
// Method 1: Default constructor
HashMap<String, Integer> scoreMap = new HashMap<>();

// Method 2: With initial capacity
HashMap<String, Integer> scoreMap = new HashMap<>(16);

// Method 3: With initial entries
Map<String, Integer> scoreMap = new HashMap<>() {{
    put("alice", 100);
    put("bob", 85);
}};
```

## 3. Basic Operations (CRUD)

### Create/Update Operations

Go:
```go
// Adding new entry
scoreMap["alice"] = 100

// Update existing entry (same syntax)
scoreMap["alice"] = 95

// Check-then-set pattern
if _, exists := scoreMap["alice"]; !exists {
    scoreMap["alice"] = 100
}
```

Java:
```java
// Adding new entry
scoreMap.put("alice", 100);

// Update existing entry
scoreMap.put("alice", 95);

// Add only if absent
scoreMap.putIfAbsent("alice", 100);

// Update if present
scoreMap.replace("alice", 95);
```

### Read Operations

Go:
```go
// Simple read (returns zero value if key doesn't exist)
score := scoreMap["alice"]

// Safe read with existence check
score, exists := scoreMap["alice"]
if exists {
    fmt.Printf("Score: %d\n", score)
}

// Iteration
for key, value := range scoreMap {
    fmt.Printf("Key: %s, Value: %d\n", key, value)
}
```

Java:
```java
// Simple read (returns null if key doesn't exist)
Integer score = scoreMap.get("alice");

// Read with default value
int score = scoreMap.getOrDefault("alice", 0);

// Check existence
boolean exists = scoreMap.containsKey("alice");

// Iteration
for (Map.Entry<String, Integer> entry : scoreMap.entrySet()) {
    System.out.printf("Key: %s, Value: %d%n", 
        entry.getKey(), entry.getValue());
}
```

### Delete Operations

Go:
```go
// Delete single entry
delete(scoreMap, "alice")

// Clear map (Method 1: Reassign)
scoreMap = make(map[string]int)

// Clear map (Method 2: Delete all keys)
for key := range scoreMap {
    delete(scoreMap, key)
}

// Delete with check
if _, exists := scoreMap["alice"]; exists {
    delete(scoreMap, "alice")
    // Handle deletion
}
```

Java:
```java
// Delete single entry
scoreMap.remove("alice");

// Delete with return value
Integer removed = scoreMap.remove("alice");

// Delete with condition
boolean removed = scoreMap.remove("alice", 100);

// Clear entire map
scoreMap.clear();

// Delete matching condition
scoreMap.entrySet().removeIf(entry -> entry.getValue() < 90);
```

## 4. Common Patterns

### Frequency Counter

Go:
```go
func buildFrequencyMap(nums []int) map[int]int {
    freq := make(map[int]int)
    for _, num := range nums {
        freq[num]++  // Automatic zero-value handling
    }
    return freq
}
```

Java:
```java
public Map<Integer, Integer> buildFrequencyMap(int[] nums) {
    Map<Integer, Integer> freq = new HashMap<>();
    for (int num : nums) {
        freq.put(num, freq.getOrDefault(num, 0) + 1);
    }
    return freq;
}
```

### Two Sum Pattern

Go:
```go
func twoSum(nums []int, target int) []int {
    seen := make(map[int]int)
    for i, num := range nums {
        if j, exists := seen[target-num]; exists {
            return []int{j, i}
        }
        seen[num] = i
    }
    return nil
}
```

Java:
```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> seen = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (seen.containsKey(complement)) {
            return new int[] {seen.get(complement), i};
        }
        seen.put(nums[i], i);
    }
    return new int[] {};
}
```

### Character Mapping (Isomorphic Strings)

Go:
```go
func isIsomorphic(s, t string) bool {
    if len(s) != len(t) {
        return false
    }
    
    sToT := make(map[byte]byte)
    tToS := make(map[byte]byte)
    
    for i := 0; i < len(s); i++ {
        sChar, tChar := s[i], t[i]
        
        if mapped, exists := sToT[sChar]; exists {
            if mapped != tChar {
                return false
            }
        } else {
            if _, exists := tToS[tChar]; exists {
                return false
            }
            sToT[sChar] = tChar
            tToS[tChar] = sChar
        }
    }
    return true
}
```

Java:
```java
public boolean isIsomorphic(String s, String t) {
    if (s.length() != t.length()) return false;
    
    Map<Character, Character> sToT = new HashMap<>();
    Map<Character, Character> tToS = new HashMap<>();
    
    for (int i = 0; i < s.length(); i++) {
        char sChar = s.charAt(i);
        char tChar = t.charAt(i);
        
        if (sToT.containsKey(sChar)) {
            if (sToT.get(sChar) != tChar) return false;
        } else {
            if (tToS.containsKey(tChar)) return false;
            sToT.put(sChar, tChar);
            tToS.put(tChar, sChar);
        }
    }
    return true;
}
```

## 5. Performance and Memory

### Go
- Average O(1) for basic operations
- Automatically grows as needed
- Not thread-safe by default
- Zero-value returns for missing keys
- Memory-efficient for basic types

### Java
- Average O(1) for basic operations
- Manual capacity control possible
- Thread-safe versions available
- Null returns for missing keys
- Boxing overhead for primitive types

## 6. Thread Safety

### Go Thread-Safe Map
```go
import "sync"

var scoreMap sync.Map

// Store
scoreMap.Store("alice", 100)

// Load
value, exists := scoreMap.Load("alice")

// Delete
scoreMap.Delete("alice")

// Load or Store
value, loaded := scoreMap.LoadOrStore("alice", 100)
```

### Java Thread-Safe Map
```java
import java.util.concurrent.ConcurrentHashMap;

ConcurrentHashMap<String, Integer> scoreMap = new ConcurrentHashMap<>();

// Regular operations are atomic
scoreMap.put("alice", 100);
scoreMap.get("alice");
scoreMap.remove("alice");

// Atomic operations
scoreMap.putIfAbsent("alice", 100);
scoreMap.replace("alice", 100, 95);
```

## 7. LeetCode Problem Patterns

### Sliding Window with Map
```go
func longestSubstring(s string) int {
    seen := make(map[rune]int)
    maxLen, start := 0, 0
    
    for end, char := range s {
        if lastSeen, exists := seen[char]; exists && lastSeen >= start {
            start = lastSeen + 1
        }
        seen[char] = end
        if end-start+1 > maxLen {
            maxLen = end - start + 1
        }
    }
    return maxLen
}
```

### Group Anagrams
```java
public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> groups = new HashMap<>();
    
    for (String s : strs) {
        char[] chars = s.toCharArray();
        Arrays.sort(chars);
        String key = new String(chars);
        
        groups.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
    }
    
    return new ArrayList<>(groups.values());
}
```

## 8. Best Practices

### Go Best Practices
1. Always initialize maps before use
2. Use zero value returns to advantage
3. Prefer direct assignment over separate existence check
4. Use `make` with capacity hint for known sizes
5. Consider `sync.Map` for concurrent access

### Java Best Practices
1. Choose initial capacity wisely
2. Use `getOrDefault` over null checks
3. Consider `EnumMap` for enum keys
4. Use `ConcurrentHashMap` for concurrent access
5. Use `computeIfAbsent` for complex value initialization

## 9. Common Pitfalls

### Go Pitfalls
1. Nil map writes cause panic
2. Concurrent access without sync.Map
3. Taking address of map elements
4. Comparing maps directly
5. Forgetting that iteration order is random

### Java Pitfalls
1. Null key/value handling
2. ConcurrentModificationException
3. Missing equals/hashCode implementation
4. Boxing/unboxing overhead
5. Thread safety assumptions

Remember that understanding these patterns and their implementations in both languages will help you solve LeetCode problems more effectively and write better production code.
