**Week:** 2

**Pattern:** Sliding Window

**Sessions:** 2 (Day 1 complete)

**Problems Solved:** 3

---

## 🎯 What is Sliding Window?

Sliding Window is a technique where you maintain a "window" (subarray/substring) and slide it across the array/string to find something (max, min, count, etc.).

**Think of it like:** A physical window sliding across a wall - you see different parts as it moves.

**Two types:**

1. **Fixed-Size Window** - Window size stays constant (k elements)
2. **Variable-Size Window** - Window expands/shrinks based on condition

---

## 📊 Progress

**Day 1 (Feb 8, 2026):** ✅ Complete

- Fixed-size sliding window
- Variable-size sliding window
- Window with frequency tracking

**Day 2 (Feb 15, 2026):** ⏳ Not Started

- More practice problems
- Harder variations

---

## 📖 Pattern 1: Fixed-Size Window

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

---

### Problem 1: Maximum Average Subarray I

**LeetCode:** #643 (Easy)

**Problem:** Find maximum average of any subarray of length k.

**Example:**

```
Input: arr = [1, 12, -5, -6, 50, 3], k = 4
Output: 12.75
Explanation: (12 + (-5) + (-6) + 50) / 4 = 12.75
```

**Solution:**

```java
public double findMaxAverage(int[] arr, int k) {
    int left = 1;
    int right = k;
    double sum = 0;
    double maxAvg = Double.NEGATIVE_INFINITY;
    
    // Build first window
    for(int i = 0; i < k; i++){
        sum = sum + arr[i];
    }
    maxAvg = Math.max(maxAvg, sum/k);
    
    // Slide window
    while(right < arr.length){
        sum = sum - arr[left-1] + arr[right];
        maxAvg = Math.max(maxAvg, sum/k);
        left++;
        right++;
    }
    
    return maxAvg;
}
```

**Complexity:**

- Time: O(n) - single pass through array
- Space: O(1) - only using variables
- Bottleneck: O(n) is optimal (must check every element)

**Common Mistakes:**

1. **Using `float` instead of `double`**
    - `float` has ~7 decimal precision
    - `double` has ~15 decimal precision
    - Large sums cause precision errors with float
    - Always use `double` for decimal calculations
2. **Using `Float.MIN_VALUE` for initialization**
    - `Float.MIN_VALUE` = 1.4E-45 (smallest positive number!)
    - Use `Float.NEGATIVE_INFINITY` or `-Float.MAX_VALUE`
    - For doubles: `Double.NEGATIVE_INFINITY`
3. **Integer division trap**
    
    ```java
    int sum = 51;
    float avg = sum / k;  // Wrong! Integer division happens first
    
    // Correct:
    double sum = 51;      // Make sum a double
    double avg = sum / k; // Now it's double division
    ```
    

**When to use MIN_VALUE vs 0:**

- Finding **maximum** value that could be negative → `Integer.MIN_VALUE` or `NEGATIVE_INFINITY`
- Finding **minimum** value that could be large → `Integer.MAX_VALUE` or `MAX_VALUE`
- Finding **length/count** (never negative) → Start at `0`

---

## 📖 Pattern 2: Variable-Size Window

**When to use:**

- Window size is NOT fixed
- Need to find longest/shortest subarray satisfying a condition
- Condition involves checking window contents (no duplicates, specific sum, etc.)

**Template:**

```java
int left = 0;
Set<Character> window = new HashSet<>();  // Or HashMap for counting

for (int right = 0; right < s.length(); right++) {
    // Shrink window while condition is violated
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
- Use HashSet/HashMap to track window contents
- Shrink with `while` loop (might need multiple removals)
- Update result after every valid window

---

### Problem 2: Longest Substring Without Repeating Characters

**LeetCode:** #3 (Medium)

**Problem:** Find length of longest substring without repeating characters.

**Example:**

```
Input: s = "abcabcbb"
Output: 3
Explanation: "abc" is the longest without repeats

Input: s = "pwwkew"
Output: 3
Explanation: "wke" is the longest
```

**Key Insight:**

When you hit a duplicate, don't restart the window - **shrink from left** until no duplicate.

```
s = "abcabcbb"
     ^^^^
     Duplicate 'a' found
     
Don't restart! Shrink:
s = "abcabcbb"
      ^^^       
      Remove left 'a', now "bca" is valid
```

**Solution:**

```java
public int lengthOfLongestSubstring(String s) {
    if(s.length() == 0) return 0;
    
    int left = 0;
    int right = 0;
    int longest = 0;
    Set<Character> seen = new HashSet<>();
    
    while(right < s.length()){
        // Shrink while duplicate exists
        while(seen.contains(s.charAt(right))){
            seen.remove(s.charAt(left));
            left++;
        }
        
        // Add current character
        seen.add(s.charAt(right));
        longest = Math.max(longest, right - left + 1);
        right++;
    }
    
    return longest;
}
```

**Complexity:**

- Time: O(n) - each character enters and leaves set at most once
- Space: O(min(n, m)) where m = character set size
    - For ASCII: O(128) = O(1)
    - Worst case: O(n) if all characters unique
- Bottleneck: O(n) is optimal

**Why not O(n²)?**

Nested loops don't always mean O(n²)!

- Outer loop: `right` goes 0 → n
- Inner loop: `left` goes 0 → n **total** (not per iteration)
- Each character processed at most twice (once by each pointer)
- Total: O(2n) = O(n)

**Common Mistake:**

Using `if` instead of `while` for shrinking:

```java
if(seen.contains(s.charAt(right))){  // Wrong! Only removes once
    seen.remove(s.charAt(left));
    left++;
}
```

Must use `while` - might need to remove multiple characters:

```java
while(seen.contains(s.charAt(right))){  // Correct!
    seen.remove(s.charAt(left));
    left++;
}
```

---

## 📖 Pattern 3: Window with Frequency Counting

**When to use:**

- Need to track **how many** of each element in window
- Problems involving "replace k characters" or "at most k distinct"
- Requires counting, not just presence

**Data Structures:**

**Option 1: HashMap**

```java
HashMap<Character, Integer> freq = new HashMap<>();
freq.put(c, freq.getOrDefault(c, 0) + 1);

// Find max frequency
int maxFreq = 0;
for (int count : freq.values()) {
    maxFreq = Math.max(maxFreq, count);
}
```

**Option 2: Array (Faster!)**

```java
// For uppercase A-Z only
int[] freq = new int[26];

// Add character
freq[c - 'A']++;

// Character 'A' → 'A' - 'A' = 0 → freq[0]
// Character 'B' → 'B' - 'A' = 1 → freq[1]
// Character 'Z' → 'Z' - 'A' = 25 → freq[25]

// Find max frequency
int maxFreq = 0;
for (int count : freq) {
    maxFreq = Math.max(maxFreq, count);
}
```

**When to use array vs HashMap:**

- **Use array:** Only uppercase (A-Z) or lowercase (a-z) → `int[26]`
- **Use HashMap:** Mixed characters, unicode, unknown character set

**Why array is faster:**

- Direct index access: O(1)
- No hashing overhead
- Better cache locality
- Less memory

---

### Problem 3: Longest Repeating Character Replacement

**LeetCode:** #424 (Medium)

**Problem:** You can replace up to k characters. Find longest substring of same letter after replacements.

**Example:**

```
Input: s = "AABABBA", k = 1
Output: 4
Explanation: Replace 1 B with A → "AAAA"

Input: s = "ABAB", k = 2
Output: 4
Explanation: Replace 2 A's with B → "BBBB"
```

**Key Insight:**

You don't need to decide which character to keep. Just check:

```
Window length = 5
Most frequent character = 3 occurrences
Replacements needed = 5 - 3 = 2

If replacements needed ≤ k → Valid window!
If replacements needed > k → Shrink!
```

**Algorithm:**

```
1. Expand right (add character)
2. Update frequency of that character
3. Track max frequency seen so far
4. Check: (windowLength - maxFreq) ≤ k?
5. If not, shrink from left
6. Update max window length
```

**Solution:**

```java
public int characterReplacement(String s, int k) {
    int left = 0, right = 0;
    int[] freqMap = new int[26];
    int maxLength = 0;
    int maxFreq = 0;
    
    while(right < s.length()){
        // Add character to window
        freqMap[s.charAt(right) - 'A']++;
        maxFreq = Math.max(maxFreq, freqMap[s.charAt(right) - 'A']);
        
        int windowLength = right - left + 1;
        int changesNeeded = windowLength - maxFreq;
        
        // Shrink while invalid
        while(changesNeeded > k){
            freqMap[s.charAt(left) - 'A']--;
            left++;
            windowLength = right - left + 1;
            changesNeeded = windowLength - maxFreq;
        }
        
        // Update max (window is now valid)
        maxLength = Math.max(windowLength, maxLength);
        right++;
    }
    
    return maxLength;
}
```

**Complexity:**

- Time: O(n) - each character processed at most twice
- Space: O(1) - array size is constant (26)
- Bottleneck: O(n) is optimal

**Common Mistakes:**

1. **Missing `- 'A'` in array indexing**
    
    ```java
    freqMap[s.charAt(left)]--;  // Wrong! Uses ASCII value (65 for 'A')
    freqMap[s.charAt(left) - 'A']--;  // Correct! Uses 0 for 'A'
    ```
    
2. **Not always expanding right**
    
    ```java
    if(changesNeeded <= k){
        right++;  // Wrong! What if condition is false?
    }
    
    // Correct: Always expand right at end of loop
    right++;
    ```
    
3. **Trying to recalculate maxFreq when shrinking**
    - You don't need to!
    - maxFreq can be "stale" and algorithm still works
    - If maxFreq is too high, window keeps shrinking until valid

---

## 🤔 How to Choose Data Structure

**Tracking presence (yes/no):**

- Use `HashSet<Character>`
- Example: "Check if character exists in window"

**Tracking frequency (count):**

- Characters A-Z or a-z → `int[26]`
- Mixed/unknown characters → `HashMap<Character, Integer>`
- Example: "Count how many times each character appears"

**Quick decision:**

```
Problem says "only lowercase letters" → int[26]
Problem says "only digits 0-9" → int[10]
Problem says "ASCII characters" → int[128] or HashMap
Problem says "any characters" → HashMap
```

---

## 📝 When to Use Sliding Window

**Triggers:**

- "Subarray" or "substring"
- "Consecutive elements"
- "Window of size k"
- "Longest/shortest satisfying condition"
- "Maximum/minimum in subarray"

**Not sliding window:**

- "Subsequence" (elements don't need to be consecutive)
- "Pick any k elements" (not consecutive)
- Problems requiring sorting or global view

---

## 🎯 Key Takeaways

**Fixed-Size Window:**

- ✅ Window size is constant (given as k)
- ✅ Slide by: subtract left + add right
- ✅ O(n) time, O(1) space
- ✅ No need for Set/HashMap usually

**Variable-Size Window:**

- ✅ Window expands/shrinks based on condition
- ✅ Use Set to track presence, HashMap/Array to track counts
- ✅ Shrink with `while` loop (not `if`)
- ✅ Update result after window becomes valid
- ✅ Still O(n) time (each element processed at most twice)

**Common Patterns:**

- Array for counting when character set is known and small
- `c - 'A'` converts character to index (0-25)
- maxFreq doesn't need recalculation when shrinking
- Always expand right, conditionally shrink left

---

## 📌 LeetCode Problems (Day 1)

**Easy:**

- #643 - Maximum Average Subarray I

**Medium:**

- #3 - Longest Substring Without Repeating Characters
- #424 - Longest Repeating Character Replacement

