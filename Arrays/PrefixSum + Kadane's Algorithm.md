Core patterns for efficiently working with subarrays and finding optimal subarray solutions.

---

## 🎯 What is Prefix Sum?

**The Problem:** You need to find the sum of elements between index `i` and `j` many times.

**Brute Force:** Recalculate each time → O(n) per query

**With Prefix Sum:** Precompute once → O(1) per query

---

## 💡 The Concept: Running Total

**Think of it like tracking your bank balance:**

```
Day 1: +$100 → Balance: $100
Day 2: +$50  → Balance: $150 (remember previous + add current)
Day 3: -$30  → Balance: $120 (remember previous + add current)
Day 4: +$200 → Balance: $320 (remember previous + add current)
```

**Prefix Sum = Running Total**

Each position stores: "What's the sum of everything I've seen so far?"

---

## 📝 Building Prefix Sum Array

**Formula:** `prefix[i] = prefix[i-1] + arr[i]`

**Example:**

```
arr =    [3,  1,  4,  2,  5]
prefix = [3,  4,  8, 10, 15]
          ↑   ↑   ↑   ↑   ↑
          3  3+1 4+4 8+2 10+5
```

**Code:**

```java
public int[] buildPrefixSum(int[] arr) {
    int[] prefix = new int[arr.length];
    prefix[0] = arr[0];
    
    for (int i = 1; i < arr.length; i++) {
        prefix[i] = prefix[i-1] + arr[i];
    }
    
    return prefix;
}
```

**Complexity:**

- Time: O(n) to build
- Space: O(n) for prefix array

---

## 🎯 Using Prefix Sum: Range Queries

**Goal:** Find sum from index `left` to `right` in O(1)

**Formula:** `sum(left, right) = prefix[right] - prefix[left-1]`

**Why it works:**

```
prefix[right] = sum of everything from 0 to right
prefix[left-1] = sum of everything from 0 to left-1

Difference = sum from left to right ✅
```

**Edge Case:** When `left = 0`, just return `prefix[right]` (no subtraction needed)

**Code:**

```java
public int rangeSum(int left, int right) {
    if (left == 0) {
        return prefix[right];
    }
    return prefix[right] - prefix[left-1];
}
```

---

## 🔑 Pattern: Prefix Sum + HashMap

**When to use:**

- Array has negative numbers (sum unpredictable)
- Looking for subarrays with **exactly K** sum
- Need to count subarrays

**Key Insight:**

```
If we want: prefix[j] - prefix[i] = k
Rearrange: prefix[i] = prefix[j] - k
```

**Translation:** "At position j, if I've seen prefix sum of `(currentSum - k)` before, there's a subarray ending at j with sum k!"

**Algorithm:**

1. Use HashMap to store `{prefixSum: frequency}`
2. Initialize with `{0: 1}` (handles subarrays from index 0)
3. For each element:
    - Update current sum
    - Check if `(currentSum - k)` exists in map
    - If yes: add its frequency to count
    - Store/update current sum in map

**Code:**

```java
public int subarraySum(int[] nums, int k) {
    int count = 0;
    int sum = 0;
    Map<Integer, Integer> freqMap = new HashMap<>();
    freqMap.put(0, 1);  // Initialize
    
    for (int num : nums) {
        sum += num;
        
        int required = sum - k;
        if (freqMap.containsKey(required)) {
            count += freqMap.get(required);  // Add frequency!
        }
        
        freqMap.put(sum, freqMap.getOrDefault(sum, 0) + 1);
    }
    
    return count;
}
```

**Critical:** Check BEFORE updating map (avoid counting same element twice)

---

## 📊 Complexity Analysis

**Building prefix sum:**

- Time: O(n)
- Space: O(n)

**Range query:**

- Time: O(1) per query
- Space: O(1)

**Prefix sum + HashMap:**

- Time: O(n)
- Space: O(n) for HashMap

---

## 🎯 When to Use Prefix Sum

**Use Prefix Sum when:**

- Multiple range sum queries on same array
- Need to find subarrays with specific sum
- Looking for "exactly K" sum (use with HashMap)
- Array has negative numbers (can't use sliding window)

**Don't use when:**

- Single query (just iterate)
- Looking for "at most K" with all positives (use sliding window)
- Need to modify array frequently (prefix becomes stale)

---

## 📝 Problems Solved

**Easy:**

- Range Sum Query - Immutable (#303)
- Running Sum of 1d Array (#1480)
- Find Pivot Index (#724)

**Medium:**

- Contiguous Array (#525)
- Subarray Sum Equals K (#560)
- Binary Subarrays With Sum (#930)

---

## 💡 Key Takeaways

1. ✅ Prefix sum = running total (each position stores sum so far)
2. ✅ Range sum = `prefix[right] - prefix[left-1]`
3. ✅ Edge case: when `left = 0`, return `prefix[right]`
4. ✅ For "exactly K" sum → use HashMap pattern
5. ✅ Initialize HashMap with `{0: 1}` before loop
6. ✅ Check map BEFORE updating (avoid self-matching)
7. ✅ Add frequency, not just count++ (handles duplicates)

---

## 🧠 Pattern Recognition

**Sliding Window vs Prefix Sum:**

**Use Sliding Window:**

- Tracking counts/frequencies (odd count, distinct chars)
- All positive numbers + "at most K"
- Predictable when add/remove

**Use Prefix Sum + HashMap:**

- Tracking sums (especially with negatives)
- "Exactly K" sum
- Unpredictable with negatives

---

## 🔜 Coming Next

**Day 2-3:** More prefix sum problems

**Day 4-6:** Kadane's Algorithm (Maximum Subarray)

**Day 7:** Week recap and pattern reinforcement

---

## 📅 Day 2: Prefix Sum + HashMap Pattern

### 🎯 Core Concept Learned

**Prefix Sum + HashMap for Subarray Counting**

When you need to count subarrays (not find max length), use HashMap to store frequencies:

- **Key:** Prefix sum value
- **Value:** How many times we've seen that prefix sum

### 💡 The Key Insight

For finding subarrays with sum = K:

```
If prefix[j] - prefix[i] = k
Then: prefix[i] = prefix[j] - k
```

**Translation:** At position j, if we've seen prefix sum of `(currentSum - k)` before, there's a subarray ending at j that sums to k!

### 🔑 Why Initialize HashMap with {0: 1}?

**The `{0: 1}` represents the empty prefix** - the state before we've seen any elements.

This handles subarrays that start from index 0.

**Example:** `nums = [1, 2, 3], k = 3`

- At index 0: sum = 1, check for (1-3) = -2 (not found)
- At index 1: sum = 3, check for (3-3) = **0** (✅ found! count++)

Without `{0: 1}`, we'd miss subarrays starting from index 0.

### 🛠️ Problems Solved Today

#### 1. LeetCode #525 - Contiguous Array

**Problem:** Find max length subarray with equal 0s and 1s.

**Key Trick:** Convert 0s to -1, 1s to +1

- Equal 0s and 1s → sum = 0
- Use HashMap to store {prefix_sum: first_index_seen}
- When same prefix_sum appears again → subarray between has sum = 0

**Code Pattern:**

```java
Map<Integer, Integer> map = new HashMap<>();
map.put(0, -1);  // Initialize for full array case
int sum = 0, maxLen = 0;

for (int i = 0; i < nums.length; i++) {
    sum += (nums[i] == 0 ? -1 : 1);  // Convert on the fly
    
    if (map.containsKey(sum)) {
        maxLen = Math.max(maxLen, i - map.get(sum));
    } else {
        map.put(sum, i);  // Store FIRST occurrence
    }
}
```

**Complexity:**

- Time: O(n)
- Space: O(n)

---

#### 2. LeetCode #560 - Subarray Sum Equals K

**Problem:** Count subarrays with sum = k.

**Key Difference from #525:**

- Not looking for max length → store **frequencies**, not indices
- Check BEFORE updating map (avoid counting same element twice)

**Code Pattern:**

```java
Map<Integer, Integer> freqMap = new HashMap<>();
freqMap.put(0, 1);  // Initialize
int sum = 0, count = 0;

for (int num : nums) {
    sum += num;
    
    int required = sum - k;
    if (freqMap.containsKey(required)) {
        count += freqMap.get(required);  // Add frequency!
    }
    
    freqMap.put(sum, freqMap.getOrDefault(sum, 0) + 1);
}
```

**Why add frequency?** Multiple prefix sums could equal `required`, each represents a valid subarray.

**Complexity:**

- Time: O(n)
- Space: O(n)

---

#### 3. LeetCode #930 - Binary Subarrays With Sum

**Problem:** Count subarrays with sum = goal (binary array).

**Pattern Recognition:** Exactly the same as #560!

- Same HashMap approach
- Same initialization with {0: 1}
- Same logic: check for (sum - goal)

**Code:** Identical structure to #560, just different variable names.

**Complexity:**

- Time: O(n)
- Space: O(n)

---

### 🧠 Pattern Recognition

**When to use Prefix Sum + HashMap:**

✅ **Use when:**

- Array has negative numbers (sum unpredictable)
- Need to count subarrays with **exact** sum
- "How many subarrays with sum = k?"
- Can't use sliding window (negatives break monotonicity)

❌ **Don't use when:**

- All positive numbers + "at most k" → use sliding window
- Looking for max/min length with predictable sums → two pointer
- Single query → just iterate

---

### 📊 Two HashMap Patterns Compared

| **Aspect** | **Max Length Pattern (#525)** | **Count Pattern (#560, #930)** |
| --- | --- | --- |
| **Goal** | Find longest subarray | Count all subarrays |
| **HashMap Value** | Index (first occurrence) | Frequency (count) |
| **Initialization** | `{0: -1}` | `{0: 1}` |
| **When found** | Calculate length | Add frequency to count |
| **Update map** | Only if not present | Always (increment frequency) |

---

### ✅ Key Takeaways from Day 2

1. **Prefix Sum + HashMap has TWO variants:**
    - Max length → store indices
    - Count subarrays → store frequencies
2. **Always initialize with {0: ...}:**
    - {0: -1} for max length (handles full array)
    - {0: 1} for counting (handles subarrays from index 0)
3. **The formula is always: `required = currentSum - target`**
4. **For counting problems:**
    - Check map BEFORE updating (avoid self-matching)
    - Add frequency, not just count++ (handles duplicates)
5. **Pattern recognition trigger:**
    - "Count subarrays with sum = k" → Prefix Sum + HashMap (frequency)
    - "Max length with sum = 0/equal" → Prefix Sum + HashMap (index)

---

### 🔥 Common Mistake Avoided

**Mistake:** Trying to use sliding window for problems with negative numbers.

**Why it fails:** With negatives, adding elements doesn't guarantee sum increases, and removing doesn't guarantee sum decreases. Window behavior becomes unpredictable.

**Solution:** Use Prefix Sum + HashMap instead - handles negatives perfectly.

---

### 🚀 Progress Check

**Week 3 Progress:**

- ✅ Day 1: Prefix sum basics (range queries)
- ✅ Day 2: Prefix sum + HashMap (subarray counting)
- 📅 Day 3: Kadane's Algorithm (coming next)

**Total Problems Solved This Week:** 6