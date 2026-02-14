# Sliding Window

## 🎯 What is Sliding Window?

Sliding Window is a technique where you maintain a "window" (subarray/substring) and slide it across the array/string to find something (max, min, count, etc.).

**Think of it like:** A physical window sliding across a wall - you see different parts as it moves.

**Two types:**

1. **Fixed-Size Window** - Window size stays constant (k elements)
2. **Variable-Size Window** - Window expands/shrinks based on condition

---

## 📚 Pattern 1: Fixed-Size Window

**When to use:**

- Problem mentions "k consecutive elements"
- Window size is given/fixed
- Find max/min/average of subarrays of size k

**Template:**

```java
// Step 1: Calculate sum of first window
for (int i = 0; i < k; i++) {
    sum += arr[i];
}
maxSum = sum;

// Step 2: Slide the window
for (int right = k; right < arr.length; right++) {
    sum = sum - arr[right - k] + arr[right];  // Remove left, add right
    maxSum = Math.max(maxSum, sum);
}
```

**Key insight:** Instead of recalculating the entire sum, subtract what leaves and add what enters.

**Complexity:**

- Time: O(n)
- Space: O(1)

**Common Mistakes:**

1. Using `float` instead of `double` (precision errors)
2. Using `Float.MIN_VALUE` for initialization (use `NEGATIVE_INFINITY`)
3. Integer division trap: `int sum / k` gives integer result

---

## 📚 Pattern 2: Variable-Size Window (Find Longest)

**When to use:**

- Window size is NOT fixed
- Need to find longest subarray satisfying a condition
- Shrink when condition is INVALID

**Template:**

```java
int left = 0;
Set<Character> window = new HashSet<>();

for (int right = 0; right < s.length(); right++) {
    // Shrink window WHILE condition is violated
    while (conditionViolated) {
        window.remove(s.charAt(left));
        left++;
    }
    
    // Add current element
    window.add(s.charAt(right));
    
    // Update result
    maxLength = Math.max(maxLength, right - left + 1);
}
```

**Key differences from fixed-size:**

- Window expands and shrinks dynamically
- Use `while` for shrinking (not `if` - might need multiple removals)
- Update result after every valid window

**Complexity:**

- Time: O(n) - each element enters/leaves at most once
- Space: O(min(n, charset size))

**Why not O(n²)?**

- Nested loops ≠ O(n²) automatically
- Each element processed at most twice (once by each pointer)
- Total: O(2n) = O(n)

---

## 📚 Pattern 3: Variable-Size Window (Find Shortest)

**When to use:**

- Need to find shortest/minimum valid window
- Shrink WHILE condition is VALID (trying to make it smaller)

**Template:**

```java
int left = 0;
int minLength = Integer.MAX_VALUE;

for (int right = 0; right < arr.length; right++) {
    // Add to window
    
    // Shrink WHILE valid (try to make it smaller!)
    while (valid) {
        minLength = Math.min(minLength, right - left + 1);
        // Remove from left
        left++;
    }
}
```

**Key difference:** Shrink while VALID (not invalid)

---

## 📚 Pattern 4: Frequency Counting

**When to use:**

- Need to track **how many** of each element
- Problems involving "replace k characters" or "at most k distinct"

**Data Structures:**

**Option 1: Array (Faster)**

```java
// For uppercase A-Z only
int[] freq = new int[26];

// Add character
freq[c - 'A']++;

// Character 'A' → index 0
// Character 'B' → index 1
// Character 'Z' → index 25
```

**Option 2: HashMap**

```java
HashMap<Character, Integer> freq = new HashMap<>();
freq.put(c, freq.getOrDefault(c, 0) + 1);
```

**When to use array vs HashMap:**

- **Array:** Known charset (A-Z, a-z) → Faster, less memory
- **HashMap:** Unknown charset, mixed characters, or any values

**Distinct Count Tracking:**

```java
// Adding character
if (freq[c - 'a'] == 0) {
    distinctCount++;  // New character!
}
freq[c - 'a']++;

// Removing character
freq[c - 'a']--;
if (freq[c - 'a'] == 0) {
    distinctCount--;  // Character gone!
}
```

**HashMap Remove Pattern:**

```java
// Expand
map.put(key, map.getOrDefault(key, 0) + 1);

// Shrink
map.put(key, map.get(key) - 1);
if (map.get(key) == 0) {
    map.remove(key);  // Remove when count reaches 0!
}
```

---

## 📚 Pattern 5: Counting Subarrays

**When to use:**

- Need to COUNT number of valid subarrays
- Not finding longest/shortest, but COUNTING

**Key Insight:**

When counting subarrays, add `(right - left)` not just `1`!

**Why?**

```
Window: [5, 2, 6]
         l     r (after right++)

Subarrays ENDING at last element (6):
1. [6]       - length 1
2. [2, 6]    - length 2  
3. [5, 2, 6] - length 3

Count = 3 = window size = right - left
```

**Template:**

```java
int count = 0;
int left = 0;

for (int right = 0; right < nums.length; right++) {
    // Add nums[right] to window
    
    // Shrink while invalid
    while (window_invalid) {
        // Remove nums[left]
        left++;
    }
    
    // Count subarrays ending at right
    count += (right - left);
}
```

**Counting Strategy:**

- Count subarrays by "ending position"
- At each step, count all subarrays ending at current `right`
- Previous positions already counted → No double counting!

---

## 📚 Pattern 6: "Exactly K" Trick

**Problem:** How to count subarrays with **exactly k** of something?

**Solution:**

```
Exactly K = (At Most K) - (At Most K-1)
```

**Why It Works:**

```
At Most K     = {0} + {1} + {2} + ... + {K}
At Most K-1   = {0} + {1} + {2} + ... + {K-1}

Subtract: (At Most K) - (At Most K-1) = {K} ✓
```

**Implementation:**

```java
public int exactlyK(int[] nums, int k) {
    return atMostK(nums, k) - atMostK(nums, k - 1);
}

private int atMostK(int[] nums, int k) {
    if (k < 0) return 0;  // Edge case!
    
    int left = 0, count = 0;
    for (int right = 0; right < nums.length; right++) {
        // Add to window
        // Shrink while > k
        count += (right - left);
    }
    return count;
}
```

---

## 📚 Pattern 7: Complex Tracking (Formed vs Required)

**When to use:**

- Need ALL characters/elements to meet a requirement
- Track: how many needed vs how many satisfied

**Template:**

```java
HashMap<Character, Integer> need = new HashMap<>();
HashMap<Character, Integer> window = new HashMap<>();

int required = need.size();
int formed = 0;

for (int right = 0; right < s.length(); right++) {
    char c = s.charAt(right);
    window.put(c, window.getOrDefault(c, 0) + 1);
    
    if (need.containsKey(c) && 
        window.get(c).intValue() == need.get(c).intValue()) {
        formed++;  // This character requirement met!
    }
    
    while (formed == required) {
        // Valid window - all requirements met
        // Save result if needed
        // Shrink and check if still valid
    }
}
```

**Key Points:**

- `required` = how many unique characters need to be satisfied
- `formed` = how many are currently satisfied
- Valid when `formed == required`

---

## 🧩 Master Template (Standard Pattern)

```java
int left = 0, right = 0;
int result = 0;

while (right < arr.length) {
    // Step 1: Add arr[right] to window
    // (update tracking variables)
    right++;
    
    // Step 2: Shrink WHILE window is invalid
    while (window_is_invalid) {
        // Remove arr[left] from window
        left++;
    }
    
    // Step 3: Update result (window is now valid)
    result = Math.max(result, right - left);
}
```

**Key Points:**

1. Always expand `right`
2. Shrink with `while` (not `if`)
3. Calculate length AFTER `right++`: use `right - left`
4. Update result after ensuring window is valid

---

## 📈 When to Use Sliding Window

**Triggers:**

- "Subarray" or "substring"
- "Consecutive elements"
- "Window of size k"
- "Longest/shortest satisfying condition"
- "Maximum/minimum in subarray"
- "Count subarrays"

**NOT sliding window:**

- "Subsequence" (elements don't need to be consecutive)
- "Pick any k elements" (not consecutive)
- Problems requiring sorting or global view

---

## 🛠️ Common Mistakes

1. **Using `if` instead of `while` for shrinking**
    - Might need to remove multiple elements
    - Always use `while`
2. **Wrong length calculation**
    - After `right++`: length = `right - left`
    - Before `right++`: length = `right - left + 1`
3. **Updating result at wrong time**
    - Update AFTER shrinking (when window is valid)
    - Not before checking validity
4. **HashMap.remove() confusion**
    - Must decrement first: `map.put(key, count - 1)`
    - Then remove if zero: `if (count == 0) map.remove(key)`
5. **Frequency tracking**
    - Check `freq == 0` BEFORE incrementing (new character)
    - Check `freq == 0` AFTER decrementing (character gone)
6. **Not checking containsKey() before HashMap access**
    - Can cause NullPointerException
    - Always check: `if (map.containsKey(key))`

---

## 📊 Complexity Analysis

**Time Complexity:**

- All sliding window patterns: O(n)
- Each element enters window once, leaves once
- Even nested while loops: O(2n) = O(n)

**Space Complexity:**

- HashSet/HashMap: O(min(n, charset size))
- Array for frequencies: O(1) (constant size)
- No extra space (just pointers): O(1)

---

## 🎯 Quick Decision Guide

**What am I finding?**

- Maximum/Longest → Shrink when INVALID
- Minimum/Shortest → Shrink while VALID
- Counting → Add `(right - left)` not `1`
- Exactly K → atMost(k) - atMost(k-1)

**What am I tracking?**

- Presence only → HashSet
- Frequencies (a-z) → int[26]
- Frequencies (mixed) → HashMap
- All requirements met → formed vs required

**When do I shrink?**

- Fixed-size → Automatic (always k elements)
- Find longest → When invalid
- Find shortest → While valid
- Counting → When invalid

---

## 📝 Example Problems by Pattern

**Pattern 1 (Fixed-Size):**

- Max Average Subarray (#643)
- Permutation in String (#567)

**Pattern 2 (Variable - Longest):**

- Longest Substring Without Repeating (#3)
- Longest Repeating Character Replacement (#424)
- Max Consecutive Ones III (#1004)
- Fruit Into Baskets (#904)

**Pattern 3 (Variable - Shortest):**

- Minimum Size Subarray Sum (#209)
- Minimum Window Substring (#76)

**Pattern 4 (Counting):**

- Subarray Product Less Than K (#713)

**Pattern 5 (Exactly K):**

- Count Nice Subarrays (#1248)

---

## ✨ Key Takeaways

1. **Standard template works for 90% of problems**
2. **Always expand, conditionally shrink**
3. **Use `while` for shrinking, not `if`**
4. **Length calculation depends on when `right` increments**
5. **Choose data structure based on what you're tracking**
6. **Counting subarrays: add window size, not 1**
7. **"Exactly K" = atMost(k) - atMost(k-1)**
8. **All patterns are O(n) time**