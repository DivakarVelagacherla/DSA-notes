Core pattern for working with arrays, especially sorted ones.

---

## 📅 Day 1 (Feb 2, 2026)

### 🎯 Concepts

**Arrays - Memory Model**

- Arrays are stored in **contiguous memory blocks**
- Each element sits right next to the other in memory
- Access formula: `address = base_address + (index × size_of_element)`
- This is why `arr[i]` is **O(1)** - one calculation, direct memory jump
- Searching unsorted array: **O(n)** - must check every element

**Two Pointer Pattern**

A technique where you use **two index variables** to track different positions in an array simultaneously.

**When to Use (Signals):**

- Need to consider **TWO elements/positions** at the same time
- Array is **sorted** (or you can sort it)
- Finding **pairs** that satisfy a condition (sum, difference, product)
- **In-place modification** required (no extra space allowed)
- Need to **partition** or **compare** two sections of the array

**Two Types:**

**Type 1: Opposite Ends (Left ← → Right)**

```
[1, 2, 3, 4, 5]
 ↑           ↑
left       right
```

- Start: `left = 0`, `right = n-1`
- Move based on comparison logic
- Use for: finding pairs, two sum, container problems

**Type 2: Same Direction (Slow/Fast)**

```
[1, 1, 2, 3, 3]
 ↑  ↑
slow fast
```

- Both start at beginning (or slow at 0, fast at 1)
- Fast pointer scans ahead
- Slow pointer marks "clean" section
- Use for: removing duplicates, partitioning, in-place filtering

---

### 🧠 How to Think

**Pattern Recognition:**

1. Read the problem
2. Ask: "Do I need to look at TWO positions at once?"
3. Ask: "Is the array sorted?"
4. Ask: "Can I solve this without extra space?"

If yes to any 2+ → likely Two Pointer

**Decision Making:**

*For Opposite Ends:*

- Compare values at both pointers
- Decide which pointer to move based on what you need
- Example: sum too small? Move left right (increase sum). Sum too big? Move right left (decrease sum).

*For Same Direction:*

- Fast pointer explores
- When fast finds something useful, slow pointer acts
- Slow marks the "boundary" of the result

---

### ⏱️ Time & Space Complexity

**Time Complexity:** O(n)

- Each pointer visits each element at most once
- Linear scan through the array

**Space Complexity:** O(1)

- Only using two integer variables (pointers)
- No extra arrays or data structures
- In-place operations

---

### 💻 Problems Solved

#### Problem 1: Two Sum II (Sorted Array)

**LeetCode:** #167 (Medium)

**Problem:** Given a **sorted** array and a target sum, return true if any two numbers add up to the target.

**Example:**

```
Input: arr = [1, 2, 5, 8, 9], target = 10
Output: true (because 1 + 9 = 10)
```

**Brute Force Approach:**

```java
public boolean twoSumBruteForce(int[] arr, int target) {
    // Check every possible pair
    for (int i = 0; i < arr.length; i++) {
        for (int j = i + 1; j < arr.length; j++) {
            if (arr[i] + arr[j] == target) {
                return true;
            }
        }
    }
    return false;
}
```

- **Time:** O(n²) - nested loops
- **Space:** O(1)

**Optimized (Two Pointer):**

```java
public boolean twoSum(int[] arr, int target) {
    int left = 0;
    int right = arr.length - 1;
    
    while (left < right) {
        int sum = arr[left] + arr[right];
        
        if (sum == target) {
            return true;
        } else if (sum < target) {
            left++;  // Need bigger sum
        } else {
            right--; // Need smaller sum
        }
    }
    
    return false;
}
```

- **Time:** O(n) - single pass
- **Space:** O(1)
- **Why it works:** Sorted array lets us eliminate possibilities. If sum is too small, only moving left pointer can help (right pointer already at max). If sum is too big, only moving right pointer can help.

---

#### Problem 2: Remove Duplicates from Sorted Array

**LeetCode:** #26 (Easy)

**Problem:** Remove duplicates from a sorted array **in-place**. Return the new length.

**Example:**

```
Input: arr = [1, 1, 2, 3, 3, 3, 4]
Output: 4
Array becomes: [1, 2, 3, 4, _, _, _] (underscores = don't care)
```

**Brute Force Approach:**

```java
public int removeDuplicatesBruteForce(int[] arr) {
    // Use extra space (HashSet or new array)
    Set<Integer> seen = new HashSet<>();
    int writeIndex = 0;
    
    for (int num : arr) {
        if (!seen.contains(num)) {
            seen.add(num);
            arr[writeIndex++] = num;
        }
    }
    
    return writeIndex;
}
```

- **Time:** O(n)
- **Space:** O(n) - HashSet takes extra space

**Optimized (Two Pointer):**

```java
public int removeDuplicates(int[] arr) {
    int left = 0;      // End of "clean" section
    int right = 1;     // Scanner
    
    while (right < arr.length) {
        if (arr[right] > arr[left]) {
            // Found a new unique value
            left++;
            arr[left] = arr[right];
        }
        right++;
    }
    
    return left + 1;  // Length of clean section
}
```

- **Time:** O(n)
- **Space:** O(1) - true in-place
- **Why it works:** Left pointer marks the end of the "clean" (no duplicates) section. Right pointer scans ahead. When right finds a new unique value (greater than left because array is sorted), we copy it to the next clean position.

---

#### Problem 3: Container With Most Water

**LeetCode:** #11 (Medium)

**Problem:** Given heights of vertical lines, find two lines that form a container with maximum water.

**Formula:** water = width × min(height1, height2)

**Example:**

```
Input: heights = [1, 8, 6, 2, 5, 4, 8, 3, 7]
Output: 49
Explanation: Lines at index 1 (height 8) and index 8 (height 7)
             width = 7, height = min(8,7) = 7
             water = 7 × 7 = 49
```

**Brute Force Approach:**

```java
public int maxWaterBruteForce(int[] heights) {
    int maxWater = 0;
    
    // Check every possible pair of lines
    for (int i = 0; i < heights.length; i++) {
        for (int j = i + 1; j < heights.length; j++) {
            int width = j - i;
            int height = Math.min(heights[i], heights[j]);
            int water = width * height;
            maxWater = Math.max(maxWater, water);
        }
    }
    
    return maxWater;
}
```

- **Time:** O(n²) - nested loops
- **Space:** O(1)

**Optimized (Two Pointer):**

```java
public int maxWater(int[] heights) {
    int left = 0;
    int right = heights.length - 1;
    int maxWater = 0;
    
    while (left < right) {
        int width = right - left;
        int height = Math.min(heights[left], heights[right]);
        int currentWater = width * height;
        maxWater = Math.max(currentWater, maxWater);
        
        // Move the shorter line
        if (heights[left] < heights[right]) {
            left++;
        } else {
            right--;
        }
    }
    
    return maxWater;
}
```

- **Time:** O(n) - single pass
- **Space:** O(1)
- **Why it works:** Start at extremes (maximum width). The shorter line limits the water. Moving the taller line guarantees worse or equal result (width decreases, height stays limited by short line). Moving the shorter line gives a CHANCE to improve (might find a taller line). Even if we hit shorter values, we explore all possible improvements.

---

### 🎓 Key Takeaways

1. **Two Pointer is about tracking TWO positions** - not just using two variables
2. **Sorted arrays unlock Two Pointer optimizations** - they give you information to eliminate possibilities
3. **Move pointers based on LOGIC** - each problem has its own movement rules
4. **Opposite ends:** typically for finding pairs
5. **Same direction:** typically for partitioning/filtering
6. **Always O(n) time, O(1) space** when done right

---

### 📌 LeetCode Problems

**Easy:**

- #26 - Remove Duplicates from Sorted Array

**Medium:**

- #167 - Two Sum II
- #11 - Container With Most Water

## 📅 Day 2 (Feb 3, 2026)

### 💻 Problems Solved

#### Problem 4: Move Zeroes

**LeetCode:** #283 (Easy)

**Problem:** Move all zeroes to the end while maintaining relative order of non-zero elements. Do it **in-place**.

**Example:**

```
Input: [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
```

**Key Insight:** Similar to Remove Duplicates, but:

- Remove Duplicates: array is **sorted**, problem says "don't care" about values beyond new length
- Move Zeroes: array is **unsorted**, must **preserve relative order**, so use **swap** instead of overwrite

**Solution (Swap Approach):**

```java
public void moveZeroes(int[] nums) {
    if (nums.length <= 1) return;
    
    int left = 0;  // Position where next non-zero should go
    
    for (int right = 0; right < nums.length; right++) {
        if (nums[right] != 0) {
            // Swap non-zero with position at left
            int temp = nums[right];
            nums[right] = nums[left];
            nums[left] = temp;
            left++;
        }
    }
}
```

- **Time:** O(n)
- **Space:** O(1)
- **Why swap?** Preserves all elements in their relative positions. No need for second pass to fill zeros.

---

#### Problem 5: Valid Palindrome II

**LeetCode:** #680 (Easy)

**Problem:** Return true if string can become a palindrome after deleting **at most one** character.

**Example:**

```
Input: "aba"
Output: true (already palindrome)

Input: "abca"
Output: true (delete 'c' → "aba")

Input: "abc"
Output: false
```

**Key Insight:** When checking palindrome with two pointers:

- Characters already matched are validated ✓
- On mismatch, only need to check **remaining middle section** after skipping one character
- Example: `"aaaaza"` → match outer a's, hit mismatch at middle `a != z`, check if `"aaa"` or `"az"` is palindrome

**Solution:**

```java
public boolean validPalindrome(String s) {
    int left = 0;
    int right = s.length() - 1;
    
    while (left < right) {
        if (s.charAt(left) == s.charAt(right)) {
            left++;
            right--;
        } else {
            // Hit mismatch - try skipping one character
            return isPalindrome(s, left+1, right) || 
                   isPalindrome(s, left, right-1);
        }
    }
    
    return true;
}

private boolean isPalindrome(String s, int left, int right) {
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

- **Time:** O(n) - at most 2 passes over substring
- **Space:** O(1)
- **Why it works:** Already validated outer characters, only check middle after deletion

---

### 🎓 Key Takeaway

**Swap vs Overwrite:**

- **Sorted array + "don't care" about rest** → overwrite is fine
- **Unsorted array + must preserve order** → use swap

**Problem Reduction:**

- Valid Palindrome II → regular palindrome check + handle one deletion case
- Breaking complex problems into simpler ones you already solved

---

### 📌 LeetCode Problems (Day 2)

**Easy:**

- #283 - Move Zeroes
- #680 - Valid Palindrome II (harder than typical easy)

## 📅 Day 3 (Feb 4, 2026)

### 💻 Problems Solved

#### Problem 6: 3Sum

**LeetCode:** #15 (Medium)

**Problem:** Find all unique triplets in an array that sum to zero.

**Example:**

```
Input: nums = [-1, 0, 1, 2, -1, -4]
Output: [[-1, -1, 2], [-1, 0, 1]]
```

**Key Insight - Problem Reduction:**

- Fix one element → reduces to Two Sum II on remaining array
- For each fixed element, find two numbers that sum to `0 - fixed`
- Example: Fix `-1`, find two numbers that sum to `1`

**Challenge: Handling Duplicates**

Sorted array: `[-4, -1, -1, 0, 1, 2]`

**Approach 1 (Initial):** Use HashSet to remove duplicates after generation

- Works but slow (552ms)
- HashSet adds overhead for hashing and checking

**Approach 2 (Optimized):** Skip duplicates during generation

- Since array is sorted, duplicates are adjacent
- Skip duplicate fixed elements in outer loop
- Skip duplicate values in two-pointer
- No HashSet needed (30ms)

**Solution:**

```java
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> triplets = new ArrayList<>();
    Arrays.sort(nums);
    
    for (int i = 0; i < nums.length; i++) {
        // Skip duplicate fixed elements
        if (i > 0 && nums[i] == nums[i-1]) continue;
        
        int firstElement = nums[i];
        int target = 0 - firstElement;
        twoSum(nums, i+1, nums.length-1, target, firstElement, triplets);
    }
    
    return triplets;
}

private void twoSum(int[] arr, int start, int end, int target, 
                    int firstElement, List<List<Integer>> triplets) {
    while (start < end) {
        int sum = arr[start] + arr[end];
        
        if (sum == target) {
            triplets.add(List.of(firstElement, arr[start], arr[end]));
            start++;
            end--;
            
            // Skip duplicates in two-pointer
            while (start < end && arr[start] == arr[start-1]) start++;
            while (start < end && arr[end] == arr[end+1]) end--;
        } else if (sum > target) {
            end--;
        } else {
            start++;
        }
    }
}
```

**Complexity Analysis:**

- **Time:** O(n²)
    - Sorting: O(n log n)
    - Outer loop: O(n)
    - Inner two-pointer: O(n) for each fixed element
    - Total: O(n log n) + O(n²) = O(n²)
- **Space:** O(log n)
    - Variables: O(1) - fixed number of variables regardless of input size
    - Sorting: O(log n) - quicksort recursion stack
    - Output not counted in space complexity
- **Bottleneck:** O(n²) nested iteration
    - Cannot be improved further
    - This is optimal for 3Sum

**Why Skip Duplicates This Way?**

Outer loop:

```java
if (i > 0 && nums[i] == nums[i-1]) continue;
```

- Compare with PREVIOUS element
- If same, skip (already processed)

Inner loop:

```java
start++;
end--;
// Then skip duplicates
while (start < end && arr[start] == arr[start-1]) start++;
while (start < end && arr[end] == arr[end+1]) end--;
```

- Move pointers FIRST
- Then skip duplicates by comparing with PREVIOUS positions
- Prevents infinite loop

---

### 🎓 Key Takeaways

**Problem Reduction:**

- 3Sum → Fix one element + Two Sum II
- Breaking complex problems into simpler subproblems

**Optimization Pattern:**

1. Get it working (use HashSet for duplicates)
2. Identify bottleneck (HashSet overhead)
3. Optimize (skip duplicates during generation)
4. Result: 552ms → 30ms

**Java Pass-by-Reference vs Pass-by-Value:**

- Primitives (`int`, `double`, `boolean`) → copied
- Objects (`List`, arrays, classes) → reference passed (efficient)

**Duplicate Skipping in Sorted Arrays:**

- Pattern appears in many problems (3Sum, 4Sum, Combination Sum)
- Duplicates are adjacent when sorted
- Skip by comparing with previous/next element

---

### 📌 LeetCode Problems (Day 3)

**Medium:**

- #15 - 3Sum

## 📅 Day 4-7 (Feb 8, 2026 - Saturday Catchup)

### 💻 Problems Solved

#### Problem 7: Trapping Rain Water

**LeetCode:** #42 (Hard)

**Problem:** Calculate how much rain water can be trapped between bars.

**Example:**

```
heights = [0,1,0,2,1,0,1,3,2,1,2,1]

Visualization:
       3              █
       2        █     █     █
       1  █     █  █  █  █  █  █
       0  0  1  0  2  1  0  1  3  2  1  2  1

Water trapped: 6 units
```

**Key Formula:** Water at position i = `min(maxLeft, maxRight) - height[i]`

**Challenge:** How to track maxLeft and maxRight without O(n) scan for each position?

**Brute Force Approach:**

```java
for each position i:
    scan left to find maxLeft (O(n))
    scan right to find maxRight (O(n))
    water += max(0, min(maxLeft, maxRight) - height[i])
```

- **Time:** O(n²) - too slow

**Optimized (Two Pointer):**

**Key Insight:** You don't need to know BOTH maxLeft and maxRight exactly. You just need to know which one is SMALLER (the limiting factor).

**Logic:**

- If `height[left] < height[right]`:
    - Left side is the bottleneck
    - Calculate water at left position using maxLeft
    - Move left++
- If `height[left] >= height[right]`:
    - Right side is the bottleneck
    - Calculate water at right position using maxRight
    - Move right--

**Solution:**

```java
public int trap(int[] height) {
    int left = 0, right = height.length - 1;
    int leftMax = 0, rightMax = 0;
    int water = 0;

    while (left < right) {
        if (height[left] <= height[right]) {
            leftMax = Math.max(leftMax, height[left]);
            water += leftMax - height[left];
            left++;
        } else {
            rightMax = Math.max(rightMax, height[right]);
            water += rightMax - height[right];
            right--;
        }
    }
    return water;
}
```

**Complexity Analysis:**

- **Time:** O(n) - single pass
- **Space:** O(1) - only a few variables
- **Bottleneck:** O(n) is optimal - must examine every element

**Why It Works:**

- When left is shorter, you know maxRight is AT LEAST height[right]
- So `min(maxLeft, maxRight) = maxLeft` (left is limiting)
- You can safely calculate water at left position
- Even though you don't know exact maxRight, you have enough info

**Common Confusion:** "Am I calculating water left to right?"

- No! You're calculating at whichever pointer has the shorter height
- Order of calculation doesn't follow array order
- You calculate where you have enough information

---

#### Problem 8: Sort Colors (Dutch National Flag)

**LeetCode:** #75 (Medium)

**Problem:** Sort an array containing only 0s, 1s, and 2s in-place, in one pass.

**Example:**

```
Input: [2, 0, 2, 1, 1, 0]
Output: [0, 0, 1, 1, 2, 2]
```

**Pattern: Three Pointers**

```
[0, 0, 1, 1, 2, 2]
      ↑  ↑     ↑
    low mid  high
    
Everything left of low = 0s
Between low and mid = 1s (sorted)
Between mid and high = unsorted (processing)
Everything right of high = 2s
```

**Solution:**

```java
public void sortColors(int[] nums) {
    int low = 0, mid = 0;
    int high = nums.length - 1;
    
    while (mid <= high) {
        if (nums[mid] == 0) {
            // Swap with low
            int temp = nums[mid];
            nums[mid] = nums[low];
            nums[low] = temp;
            low++;
            mid++;
        } else if (nums[mid] == 1) {
            mid++;
        } else {
            // Swap with high
            int temp = nums[mid];
            nums[mid] = nums[high];
            nums[high] = temp;
            high--;
            // Don't move mid - need to check swapped element
        }
    }
}
```

**Complexity:**

- **Time:** O(n) - single pass
- **Space:** O(1) - in-place
- **Bottleneck:** O(n) is optimal

**Key Insight:** When swapping with high, don't increment mid because the swapped element needs to be examined.

---

#### Problem 9: Remove Element

**LeetCode:** #27 (Easy)

**Problem:** Remove all instances of a value in-place. Return new length.

**Example:**

```
Input: nums = [3, 2, 2, 3], val = 3
Output: 2
Array becomes: [2, 2, _, _]
```

**Pattern:** Same direction (slow/fast) - identical to Remove Duplicates

**Solution:**

```java
public int removeElement(int[] nums, int val) {
    int left = 0;
    
    for (int right = 0; right < nums.length; right++) {
        if (nums[right] != val) {
            nums[left] = nums[right];
            left++;
        }
    }
    
    return left;
}
```

**Complexity:**

- **Time:** O(n)
- **Space:** O(1)
- **Bottleneck:** O(n) is optimal

---

#### Problem 10: 3Sum Closest

**LeetCode:** #16 (Medium)

**Problem:** Find three numbers whose sum is CLOSEST to the target. Return the sum.

**Example:**

```
Input: nums = [-1, 2, 1, -4], target = 1
Output: 2
Explanation: -1 + 2 + 1 = 2 (closest to 1)
```

**Key Difference from 3Sum:**

- Track the sum with minimum distance from target
- Distance = `Math.abs(sum - target)`
- Return the SUM, not the distance

**Solution:**

```java
public int threeSumClosest(int[] arr, int target) {
    Arrays.sort(arr);
    int closestSum = arr[0] + arr[1] + arr[2];
    
    for (int i = 0; i < arr.length - 2; i++) {
        int left = i + 1;
        int right = arr.length - 1;
        
        while (left < right) {
            int sum = arr[i] + arr[left] + arr[right];
            
            if (Math.abs(sum - target) < Math.abs(closestSum - target)) {
                closestSum = sum;
            }
            
            if (sum < target) {
                left++;
            } else if (sum > target) {
                right--;
            } else {
                return target;  // Can't get closer!
            }
        }
    }
    
    return closestSum;
}
```

**Complexity:**

- **Time:** O(n²)
- **Space:** O(1)
- **Bottleneck:** O(n²) is optimal

**Common Mistake:** Using `Math.abs(sum)` instead of `Math.abs(sum - target)`

**When to use long:** If values up to 10^9 and adding 3+ numbers, check if sum exceeds 2×10^9

---

#### Problem 11: 4Sum

**LeetCode:** #18 (Medium/Hard)

**Problem:** Find all unique quadruplets that sum to target.

**Example:**

```
Input: nums = [1, 0, -1, 0, -2, 2], target = 0
Output: [[-2, -1, 1, 2], [-2, 0, 0, 2], [-1, 0, 0, 1]]
```

**Pattern:** Fix TWO elements, then two pointers

**Structure:**

```
Outer loop (i): fix first element
  Inner loop (j): fix second element
    Two pointers: find pairs that sum to (target - arr[i] - arr[j])
```

**Solution:**

```java
public List<List<Integer>> fourSum(int[] arr, int target) {
    Arrays.sort(arr);
    List<List<Integer>> result = new ArrayList<>();
    
    for (int i = 0; i < arr.length - 3; i++) {
        if (i > 0 && arr[i] == arr[i-1]) continue;  // Skip duplicate
        
        for (int j = i+1; j < arr.length - 2; j++) {
            if (j > i+1 && arr[j] == arr[j-1]) continue;  // Skip duplicate
            
            int left = j + 1;
            int right = arr.length - 1;
            
            while (left < right) {
                long sum = (long)arr[i] + arr[j] + arr[left] + arr[right];
                
                if (sum < target) {
                    left++;
                } else if (sum > target) {
                    right--;
                } else {
                    result.add(List.of(arr[i], arr[j], arr[left], arr[right]));
                    left++;
                    right--;
                    
                    // Skip duplicates
                    while (left < right && arr[left] == arr[left-1]) left++;
                    while (left < right && arr[right] == arr[right+1]) right--;
                }
            }
        }
    }
    
    return result;
}
```

**Complexity:**

- **Time:** O(n³) - two nested loops + two pointers
- **Space:** O(1) - excluding output
- **Bottleneck:** O(n³) is optimal for 4Sum

**Integer Overflow:** Use `long` for sum when values up to 10^9 (4 × 10^9 > 2 × 10^9)

---

### 🎓 Week 1 Final Takeaways

**Three Main Patterns Mastered:**

1. **Opposite Ends (Convergent)**
    - Start at both ends, move inward
    - Use when: sorted array, finding pairs, need to explore from extremes
    - Problems: Two Sum II, 3Sum, Container With Most Water, Trapping Rain Water
2. **Same Direction (Slow/Fast)**
    - Both pointers move in same direction
    - Slow marks boundary, fast scans ahead
    - Use when: in-place modification, building clean section
    - Problems: Remove Duplicates, Move Zeroes, Remove Element
3. **Three Pointers (Partitioning)**
    - Partition into 3 sections
    - Use when: multiple distinct values to separate
    - Problems: Sort Colors (Dutch National Flag)

**Problem Reduction Pattern:**

- 2Sum → sorted + two pointers
- 3Sum → fix one + 2Sum
- 3Sum Closest → fix one + 2Sum (track closest)
- 4Sum → fix two + 2Sum

**Complexity Patterns:**

- 2Sum: O(n)
- 3Sum: O(n²)
- 4Sum: O(n³)
- Pattern: K-Sum = O(n^(K-1))

**Integer Overflow Rule:**

- If adding N numbers, each up to M
- Max sum = N × M
- If N × M > 2 × 10^9, use `long`

**Duplicate Handling:**

- Only needed when finding unique combinations
- Skip adjacent duplicates in sorted array
- Compare with previous element: `if (i > start && arr[i] == arr[i-1]) continue`

---

### 📌 All LeetCode Problems (Week 1)

**Easy:**

- #26 - Remove Duplicates from Sorted Array
- #27 - Remove Element
- #283 - Move Zeroes
- #680 - Valid Palindrome II

**Medium:**

- #11 - Container With Most Water
- #15 - 3Sum
- #16 - 3Sum Closest
- #75 - Sort Colors
- #167 - Two Sum II

**Hard:**

- #18 - 4Sum
- #42 - Trapping Rain Water

**Total: 11 problems, 3 patterns mastered**
