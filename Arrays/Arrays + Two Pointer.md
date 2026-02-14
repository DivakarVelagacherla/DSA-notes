# Arrays and Two Pointer

Core pattern for working with arrays, especially sorted ones.

---

## 📚 Arrays - Memory Model

**Contiguous Memory Storage:**

- Arrays are stored in **contiguous memory blocks**
- Each element sits right next to the other in memory
- Access formula: `address = base_address + (index × size_of_element)`
- This is why `arr[i]` is **O(1)** - one calculation, direct memory jump

**Search Complexity:**

- Unsorted array: **O(n)** - must check every element
- Sorted array: **O(log n)** - can use binary search

---

## 🎯 Two Pointer Pattern

A technique where you use **two index variables** to track different positions in an array simultaneously.

### When to Use (Signals):

- Need to consider **TWO elements/positions** at the same time
- Array is **sorted** (or you can sort it)
- Finding **pairs** that satisfy a condition (sum, difference, product)
- **In-place modification** required (no extra space allowed)
- Need to **partition** or **compare** two sections of the array

### Two Types:

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

## 🧠 How to Recognize Pattern

**Ask yourself:**

1. Do I need to look at TWO positions at once?
2. Is the array sorted?
3. Can I solve this without extra space?

**If yes to 2+ → likely Two Pointer**

**Decision Making:**

*For Opposite Ends:*

- Compare values at both pointers
- Decide which pointer to move based on what you need
- Example: sum too small? Move left right (increase sum). Sum too big? Move right left (decrease sum)

*For Same Direction:*

- Fast pointer explores
- When fast finds something useful, slow pointer acts
- Slow marks the "boundary" of the result

---

## ⏱️ Complexity

**Time:** O(n)

- Each pointer visits each element at most once
- Linear scan through the array

**Space:** O(1)

- Only using two integer variables (pointers)
- No extra arrays or data structures
- In-place operations

---

## 📚 Pattern 1: Two Sum (Sorted Array)

**Problem:** Given a **sorted** array and a target sum, find two numbers that add up to target.

**Example:**

```
Input: arr = [1, 2, 5, 8, 9], target = 10
Output: [1, 9] (indices or values)
```

**Brute Force:** O(n²) - check every pair

**Two Pointer Solution:**

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

**Time:** O(n), **Space:** O(1)

**Why it works:** Sorted array lets us eliminate possibilities. If sum is too small, only moving left pointer can help. If sum is too big, only moving right pointer can help.

---

## 📚 Pattern 2: Remove Duplicates (In-Place)

**Problem:** Remove duplicates from a sorted array **in-place**. Return the new length.

**Example:**

```
Input: arr = [1, 1, 2, 3, 3, 3, 4]
Output: 4
Array becomes: [1, 2, 3, 4, _, _, _]
```

**Two Pointer Solution:**

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

**Time:** O(n), **Space:** O(1)

**Why it works:** Left pointer marks the end of the "clean" (no duplicates) section. Right pointer scans ahead. When right finds a new unique value, copy it to the next clean position.

---

## 📚 Pattern 3: Container With Most Water

**Problem:** Given heights of vertical lines, find two lines that form a container with maximum water.

**Formula:** water = width × min(height1, height2)

**Example:**

```
heights = [1, 8, 6, 2, 5, 4, 8, 3, 7]
Output: 49

Lines at index 1 (height 8) and index 8 (height 7)
width = 7, height = min(8,7) = 7
water = 7 × 7 = 49
```

**Two Pointer Solution:**

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

**Time:** O(n), **Space:** O(1)

**Why it works:** Start at extremes (maximum width). The shorter line limits the water. Moving the taller line guarantees worse or equal result. Moving the shorter line gives a CHANCE to improve.

---

## 📚 Pattern 4: Move Zeroes

**Problem:** Move all zeroes to the end while maintaining relative order of non-zero elements. Do it **in-place**.

**Example:**

```
Input: [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
```

**Swap Approach:**

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

**Time:** O(n), **Space:** O(1)

**Swap vs Overwrite:**

- **Sorted array + "don't care" about rest** → overwrite is fine
- **Unsorted array + must preserve order** → use swap

---

## 📚 Pattern 5: 3Sum

**Problem:** Find all unique triplets in an array that sum to zero.

**Example:**

```
Input: nums = [-1, 0, 1, 2, -1, -4]
Output: [[-1, -1, 2], [-1, 0, 1]]
```

**Key Insight:** Fix one element → reduces to Two Sum on remaining array

**Solution:**

```java
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> triplets = new ArrayList<>();
    Arrays.sort(nums);
    
    for (int i = 0; i < nums.length - 2; i++) {
        // Skip duplicate fixed elements
        if (i > 0 && nums[i] == nums[i-1]) continue;
        
        int left = i + 1;
        int right = nums.length - 1;
        int target = 0 - nums[i];
        
        while (left < right) {
            int sum = nums[left] + nums[right];
            
            if (sum == target) {
                triplets.add(List.of(nums[i], nums[left], nums[right]));
                left++;
                right--;
                
                // Skip duplicates
                while (left < right && nums[left] == nums[left-1]) left++;
                while (left < right && nums[right] == nums[right+1]) right--;
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
    }
    
    return triplets;
}
```

**Time:** O(n²), **Space:** O(log n) for sorting

**Duplicate Handling:**

- Skip adjacent duplicates in sorted array
- Compare with previous element: `if (i > 0 && nums[i] == nums[i-1]) continue`

---

## 📚 Pattern 6: Trapping Rain Water

**Problem:** Calculate how much rain water can be trapped between bars.

**Example:**

```
heights = [0,1,0,2,1,0,1,3,2,1,2,1]
Water trapped: 6 units
```

**Key Formula:** Water at position i = `min(maxLeft, maxRight) - height[i]`

**Two Pointer Solution:**

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

**Time:** O(n), **Space:** O(1)

**Why it works:** When left is shorter, you know maxRight is at least height[right]. So `min(maxLeft, maxRight) = maxLeft`. You can safely calculate water at left position.

---

## 📚 Pattern 7: Dutch National Flag (3 Pointers)

**Problem:** Sort an array containing only 0s, 1s, and 2s in-place, in one pass.

**Example:**

```
Input: [2, 0, 2, 1, 1, 0]
Output: [0, 0, 1, 1, 2, 2]
```

**Three Pointer Solution:**

```java
public void sortColors(int[] nums) {
    int low = 0, mid = 0;
    int high = nums.length - 1;
    
    while (mid <= high) {
        if (nums[mid] == 0) {
            swap(nums, mid, low);
            low++;
            mid++;
        } else if (nums[mid] == 1) {
            mid++;
        } else {
            swap(nums, mid, high);
            high--;
            // Don't move mid - need to check swapped element
        }
    }
}
```

**Time:** O(n), **Space:** O(1)

**Partition Logic:**

```
Everything left of low = 0s
Between low and mid = 1s
Between mid and high = unsorted
Everything right of high = 2s
```

---

## 🎯 Problem Reduction Pattern

**K-Sum Pattern:**

- 2Sum → O(n) with two pointers
- 3Sum → Fix one + 2Sum = O(n²)
- 4Sum → Fix two + 2Sum = O(n³)
- Pattern: **K-Sum = O(n^(K-1))**

---

## ⚠️ Common Mistakes

1. **Wrong loop condition**
    - Use `left < right` not `left <= right`
    - Prevents checking same element twice
2. **Not handling duplicates**
    - Skip duplicates: `if (i > 0 && arr[i] == arr[i-1]) continue`
    - Only needed for finding unique combinations
3. **Integer overflow**
    - If adding N numbers up to M each
    - Max sum = N × M
    - If N × M > 2×10⁹, use `long`
4. **Moving mid after swap with high**
    - When swapping with high in 3-pointer, DON'T increment mid
    - Need to check the swapped element

---

## 📝 Example Problems

**Easy:**

- Remove Duplicates (#26)
- Remove Element (#27)
- Move Zeroes (#283)
- Valid Palindrome II (#680)

**Medium:**

- Two Sum II (#167)
- Container With Most Water (#11)
- 3Sum (#15)
- 3Sum Closest (#16)
- Sort Colors (#75)

**Hard:**

- Trapping Rain Water (#42)
- 4Sum (#18)

---

## ✨ Key Takeaways

**Three Main Patterns:**

1. **Opposite Ends** - Start at both ends, move inward
2. **Same Direction** - Slow marks boundary, fast scans
3. **Three Pointers** - Partition into 3 sections

**When to Use:**

- Sorted array + finding pairs → Opposite ends
- In-place modification → Same direction
- Multiple distinct values → Three pointers

**Complexity:**

- Always O(n) time for single pass
- Always O(1) space for in-place
- Optimal for most two-pointer problems