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

**Problems for Day 2:**

- More practice with these patterns
- Harder variations
- Combining with other techniques

## 📅 Day 2 Progress (Feb 9, 2026)

**Duration:** 2 hours

**Problems Solved:** 5

**Focus:** Frequency HashMap mastery

### Problems Completed:

**Warm-up:**

- Container With Most Water (LeetCode #11) - Two pointer review ✅

**New Problems:**

1. **Minimum Size Subarray Sum** (LeetCode #209) ✅
    - Variable-size window
    - Find shortest valid window
    - Learned: `right - left` when right increments first
2. **Permutation in String** (LeetCode #567) ✅
    - Fixed-size window
    - Frequency matching with `int[26]`
    - Edge case: Check if s2 shorter than s1
3. **Find All Anagrams** (Custom) ✅
    - Fixed-size window
    - Return all matching indices
    - Learned: Window start = `left + 1` after sliding
4. **Longest K Distinct Characters** (Custom) ✅
    - Variable-size window
    - Track distinct character count
    - Learned: Check `freq[c] == 0` before/after increment/decrement

**In Progress:**

1. **Minimum Window Substring** - Saved for Day 3 (hardest sliding window problem)

---

### Key Learnings from Day 2:

**Frequency Tracking Pattern:**

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

**Fixed-Size Window Template:**

```java
// Build first window
for (int i = 0; i < windowSize; i++) {
    windowFreq[s.charAt(i) - 'a']++;
}

// Slide window
for (int right = windowSize; right < s.length(); right++) {
    windowFreq[s.charAt(right) - 'a']++;     // Add new
    int left = right - windowSize;
    windowFreq[s.charAt(left) - 'a']--;      // Remove old
    
    // Check window validity
    if (isValid()) {
        // Process
    }
}
```

**Common Mistakes Fixed:**

1. **Sequential loops don't increase complexity**
    - O(n) + O(m) + O(k) = O(n) if proportional
    - Only nested loops increase: O(n × m) = O(n²)
2. **When to update result**
    - Update AFTER shrinking (when window is valid)
    - Not before checking validity
3. **Index calculations**
    - After `right++`: window length = `right - left`
    - Before `right++`: window length = `right - left + 1`
4. **Edge cases**
    - Check if target string longer than source
    - Check array bounds before accessing

---

### Pattern Recognition Progress:

**Fixed-Size Window:**

- ✅ Know when to use (window size given/fixed)
- ✅ Can implement sliding efficiently
- ✅ Understand frequency matching

**Variable-Size Window:**

- ✅ Know when to use (find longest/shortest)
- ✅ Can expand and shrink correctly
- ✅ Track distinct counts properly

**Frequency HashMap:**

- ✅ Using `int[26]` for lowercase letters
- ✅ Check frequency == 0 for new/removed characters
- ✅ Comparing two frequency arrays

## 📅 Day 3 Progress (Feb 10, 2026)

**Duration:** 1.5 hours (shortened session - late start, sleepy)

**Problems Solved:** 2

**Focus:** HashMap + Sliding Window

### Problems Completed:

**Warm-up:**

- **3Sum** (LeetCode #15) ✅
    - Two pointer pattern review
    - Fixed `i`, use two pointers for remaining
    - Skip duplicates to avoid duplicate triplets
    - Key: Sort first, then iterate

**New Problem:**

1. **Fruit Into Baskets** (LeetCode #904) ✅
    - Variable-size window with at most K=2 distinct elements
    - HashMap frequency tracking
    - Learned: Use HashMap when need both presence AND count
    - Learned: Remove key from HashMap when count becomes 0

---

### Key Learnings from Day 3:

**HashMap vs Array Decision:**

```
Use Array (int[26]):
- Known character set (a-z)
- Faster (no hashing overhead)
- Less memory

Use HashMap:
- Unknown character set
- Need to track ANY values (not just characters)
- When you need both presence AND count
```

**HashMap Frequency Pattern:**

```java
// Expand window
map.put(key, map.getOrDefault(key, 0) + 1);

// Shrink window
map.put(key, map.get(key) - 1);
if (map.get(key) == 0) {
    map.remove(key);  // Remove when count reaches 0!
}
```

**Common Mistakes Fixed:**

1. **Initial pointer position**
    
    ```java
    int right = fruits.length - 1;  // ❌
    int right = 0;                   // ✓
    ```
    
2. **Shrinking once vs while loop**
    
    ```java
    if (map.size() > target) {  // ❌ Only shrinks once
    while (map.size() > target) {  // ✓ Keeps shrinking
    ```
    
3. **HashMap.remove() confusion**
    
    ```java
    // Wrong - doesn't decrement!
    map.remove(key, map.get(key) - 1);
    
    // Correct
    map.put(key, map.get(key) - 1);
    if (map.get(key) == 0) {
        map.remove(key);
    }
    ```
    
4. **Updating result before validation**
    
    ```java
    // Wrong - updates before checking validity
    maxLen = Math.max(maxLen, right - left + 1);
    while (map.size() > target) { shrink... }
    
    // Correct - updates after ensuring window is valid
    while (map.size() > target) { shrink... }
    maxLen = Math.max(maxLen, right - left + 1);
    ```
    

---

### Alternative Solution Learned:

**Storing last index instead of frequency:**

```java
// Instead of: map.put(fruit, frequency)
// Use: map.put(fruit, lastIndex)

if (map.size() > 2) {
    int smallestIdx = Collections.min(map.values());
    map.remove(fruits[smallestIdx]);
    left = smallestIdx + 1;  // Jump left pointer
}
```

**Trade-offs:**

- ✅ Shorter code, no while loop for shrinking
- ⚠️ O(k) overhead for Collections.min() where k = map size
- Both approaches are O(n) overall

---

### Session Notes:

**Challenge Faced:**

- Attempted **Minimum Window Substring** (LeetCode #76 - Hard)
- Decided to postpone until end of week
- Smart decision: build confidence with medium problems first
- Will revisit during Sunday recap with more preparation

**Learning Mindset:**

- Every pattern feels hard initially - this is normal!
- Struggling is part of the learning process
- Week 1 (Two Pointers) felt hard, now easy
- Week 2 (Sliding Window) feels hard now, will be easy soon

## 📅 Day 4 Progress (Feb 11, 2026)

**Duration:** 2 hours

**Problems Solved:** 4

**Focus:** Counting subarrays pattern + "Exactly K" trick

### Problems Completed:

**Warm-up:**

- **Remove Duplicates from Sorted Array** (LeetCode #26) ✅
    - Two pointer pattern review
    - Slow/fast pointer technique
    - In-place modification

**New Problems:**

1. **Max Consecutive Ones III** (LeetCode #1004) ✅
    - Variable-size window
    - Track count of zeros flipped
    - Valid when: `zeroCount <= k`
    - Learned: Standard sliding window template
2. **Subarray Product Less Than K** (LeetCode #713) ✅
    - **NEW PATTERN:** Counting subarrays
    - Key insight: `count += (right - left)`
    - Each window contributes multiple subarrays
    - Subarrays are counted by ending position
3. **Count Number of Nice Subarrays** (LeetCode #1248) ✅
    - **NEW TRICK:** "Exactly K" = "At Most K" - "At Most K-1"
    - Track odd number count
    - Reusable helper function pattern

---

### Key Learnings from Day 4:

**Standard Sliding Window Template (MEMORIZE THIS!):**

```java
int left = 0, right = 0;
int maxLength = 0;

while (right < nums.length) {
    // Step 1: Add nums[right] to window
    // (update tracking variables)
    right++;
    
    // Step 2: Shrink WHILE window is invalid
    while (window_is_invalid) {
        // Remove nums[left] from window
        left++;
    }
    
    // Step 3: Update result (window is now valid)
    maxLength = Math.max(maxLength, right - left);
}
```

**Why `right - left` and not `right - left + 1`?**

```java
// After right++:
// - right points ONE PAST the last element
// - Window is [left, right-1] inclusive
// - Length = right - left

Example:
  [1, 2, 3]
   l     r (after right++)
   0     3
   
  Window contains indices [0, 1, 2]
  Length = 3 - 0 = 3 ✓
```

---

### NEW PATTERN: Counting Subarrays

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

**Each window of size N contributes N subarrays!**

**Counting Strategy:**

- Count subarrays by "ending position"
- At each step, count subarrays ending at current `right`
- Previous positions already counted in previous iterations
- No double counting!

**Template for Counting:**

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

**Use Cases:**

- Count subarrays with product < k
- Count subarrays with sum < k
- Count subarrays with at most k distinct elements

---

### NEW TRICK: "Exactly K" Pattern

**Problem:** How to count subarrays with **exactly k** of something?

**Solution:** 

```
Exactly K = (At Most K) - (At Most K-1)
```

**Why It Works:**

```
At Most K     = {0} + {1} + {2} + ... + {K}
At Most K-1   = {0} + {1} + {2} + ... + {K-1}

Subtract:
(At Most K) - (At Most K-1) = {K} ✓
```

**Example:** Exactly 2 odds

```
At Most 2 odds = {0 odds, 1 odd, 2 odds}
At Most 1 odd  = {0 odds, 1 odd}
Difference     = {2 odds} ✓
```

**Implementation Pattern:**

```java
public int exactlyK(int[] nums, int k) {
    return atMostK(nums, k) - atMostK(nums, k - 1);
}

private int atMostK(int[] nums, int k) {
    if (k < 0) return 0;  // Edge case!
    
    // Standard counting pattern
    int left = 0, count = 0;
    for (int right = 0; right < nums.length; right++) {
        // Add to window
        // Shrink while > k
        count += (right - left);
    }
    return count;
}
```

**Use Cases:**

- Exactly k odd numbers
- Exactly k distinct characters
- Exactly k zeros

---

### Common Mistakes Fixed:

1. **Loop condition**
    
    ```java
    while(right < nums.length-1)  // ❌ Misses last element
    while(right < nums.length)     // ✓
    ```
    
2. **Nested loop complexity**
    - Nested while loops ≠ O(n²)
    - Each element added once, removed once
    - Total = O(2n) = O(n) ✓
3. **Edge cases for counting**
    
    ```java
    if (k < 0) return 0;  // Prevent negative k in atMost
    ```
    
4. **When k <= 1 causes issues**
    
    ```java
    // For product problems:
    if (k <= 1) return 0;  // Product always >= 1
    
    // OR use defensive condition:
    while(product >= k && left < right)
    ```
    

---

### Interview Explanation Tips:

**For Counting Subarrays:**

> "At each position, I count all valid subarrays ending there. A window of size N has N subarrays ending at the last element. This ensures each subarray is counted exactly once."
> 

**For Exactly K Trick:**

> "'Exactly K' is tricky with sliding window. But I can use: Exactly K = At Most K - At Most K-1. This works because subtracting removes all subarrays with fewer than K, leaving only exactly K."
> 

**Verify with Example:**

Always trace through a small example to build confidence!

---

### Patterns Mastered:

**Week 2 Pattern Checklist:**

- ✅ Fixed-size sliding window
- ✅ Variable-size sliding window (longest/shortest)
- ✅ Frequency tracking (array and HashMap)
- ✅ Distinct element counting
- ✅ **NEW: Counting subarrays**
- ✅ **NEW: "Exactly K" trick**

---

### Performance Notes:

**Solution Speed:**

- Your solution: 13ms (clear, maintainable)
- Optimized solutions: 6ms (complex, harder to explain)
- **Verdict:** Clarity > micro-optimization!
- In interviews, explainability matters more than 7ms difference

**When to optimize:**

- Master patterns first
- Then learn optimizations
- Focus on O(n) → O(log n) improvements, not constants

---

### Week 2 Progress Summary:

**Days completed:** 4/5

**Total problems:** 16

- Day 1: 3 problems (basics)
- Day 2: 5 problems (frequency mastery)
- Day 3: 2 problems (HashMap)
- Day 4: 4 problems (counting patterns)

**Remaining:**

- Day 5 (Friday): More practice + variations
- Sunday: 30-min recap + tackle Minimum Window Substring (hard)

---

---

### Week 2 Progress Summary:

**Days completed:** 3/5

**Total problems:** 12

- Day 1: 3 problems (fixed-size windows, frequency basics)
- Day 2: 5 problems (frequency mastery, custom problems)
- Day 3: 2 problems (HashMap pattern)

**Patterns Mastered:**

- ✅ Fixed-size sliding window
- ✅ Variable-size sliding window (find longest/shortest)
- ✅ Frequency tracking with arrays
- ✅ Frequency tracking with HashMap
- ✅ Distinct element counting
- 🔄 Advanced patterns (in progress)

---