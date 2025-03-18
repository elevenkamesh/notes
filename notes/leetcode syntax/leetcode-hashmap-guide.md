# HashMap Patterns in LeetCode

## Basic HashMap Operations

### Initialization
```java
// Standard initialization
HashMap<Integer, Integer> map = new HashMap<>();

// Initialize with initial capacity
HashMap<String, Integer> map = new HashMap<>(16);

// Initialize from another map
HashMap<String, Integer> map = new HashMap<>(existingMap);
```

### Common Operations
```java
// Adding elements
map.put(key, value);              // O(1) average
map.putIfAbsent(key, value);      // Only puts if key doesn't exist

// Retrieving elements
map.get(key);                     // O(1) average, returns null if not found
map.getOrDefault(key, defaultValue); // Returns defaultValue if key not found

// Checking existence
map.containsKey(key);             // O(1) average
map.containsValue(value);         // O(n) - avoid if possible

// Removing elements
map.remove(key);                  // O(1) average
map.remove(key, value);           // Only removes if key maps to value

// Size operations
map.size();                       // Get number of entries
map.isEmpty();                    // Check if map is empty
map.clear();                      // Remove all entries
```

## Common LeetCode Patterns

### 1. Frequency Counter Pattern
```java
// Count frequency of elements
public Map<Integer, Integer> countFrequency(int[] nums) {
    Map<Integer, Integer> freqMap = new HashMap<>();
    for (int num : nums) {
        freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
    }
    return freqMap;
}
```

### 2. Two Sum Pattern
```java
// Find two numbers that add up to target
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] {map.get(complement), i};
        }
        map.put(nums[i], i);
    }
    return new int[] {};
}
```

### 3. Character/String Mapping Pattern
```java
// Check if strings are isomorphic
public boolean isIsomorphic(String s, String t) {
    Map<Character, Character> sToT = new HashMap<>();
    Map<Character, Character> tToS = new HashMap<>();
    
    for (int i = 0; i < s.length(); i++) {
        char sChar = s.charAt(i);
        char tChar = t.charAt(i);
        
        if (sToT.containsKey(sChar) && sToT.get(sChar) != tChar) return false;
        if (tToS.containsKey(tChar) && tToS.get(tChar) != sChar) return false;
        
        sToT.put(sChar, tChar);
        tToS.put(tChar, sChar);
    }
    return true;
}
```

### 4. Sliding Window with HashMap
```java
// Find longest substring without repeating characters
public int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> map = new HashMap<>();
    int maxLength = 0;
    int start = 0;
    
    for (int end = 0; end < s.length(); end++) {
        char currentChar = s.charAt(end);
        if (map.containsKey(currentChar)) {
            start = Math.max(map.get(currentChar) + 1, start);
        }
        map.put(currentChar, end);
        maxLength = Math.max(maxLength, end - start + 1);
    }
    return maxLength;
}
```

## Advanced Tips

1. Use `getOrDefault()` instead of checking `containsKey()` followed by `get()`
2. Consider using `Map.Entry<K,V>` for iterating over entries:
```java
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    String key = entry.getKey();
    Integer value = entry.getValue();
}
```

3. For counting problems, consider using Java's built-in frequency utilities:
```java
Map<String, Long> frequency = Arrays.stream(words)
    .collect(Collectors.groupingBy(w -> w, Collectors.counting()));
```

## Common Pitfalls to Avoid

1. Don't use `map.get()` in conditions without null checking
2. Avoid `containsValue()` for performance-critical code
3. Remember that `put()` returns the previous value
4. Be careful with mutable objects as keys
