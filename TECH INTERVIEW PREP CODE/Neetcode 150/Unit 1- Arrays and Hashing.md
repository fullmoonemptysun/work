# 🟩 Valid anagram

1. Can be solved with two hashmaps but space is O(n)
2. To make space O(1) Problem is limited to 26 characters and then we just have 2 arrays of 26 characters and then just count instance of each letter in each string

  

# 🟨 Group Anagrams

1. Use something that binds a group of anagrams together as key and an array of these anagrams as value in a dictionary.
2. One pass through the array

# 🟨 Top K frequent Elements

> [!Important] Problem Description
> 1. My solution was using hashmap as a counter then doing a loop k times and finding the max each time and add it to the result. This is **O(KN)** K times looking through n items in the dictionary.
> 
> 2. Better solution: Use a heap (Learn)
> 3. Optimal Solution: **O(N)**
> 4. Store lists of all the numbers that have the same frequency at the index = frequency.
> ```undefined
> CREATE A COUNTER AS IN THE ORIGINAL APPROACH
> 
> MAIN TRICK
> CREATE AN ARRAY OF ARRAYS OF SIZE N+1 (because frequency of a number can be = len(nums) and then we'll need to put it at that index).
> 
> AT THE END, go through this new array of frequency buckets in the reverse order, and just put the numbers from the buckets into the result array (use a for loop for each inside array, so will skip if empty).
> 
> 
> ```
> 

# 🟨 Encode and Decode

> [!IMPORTANT] PROBLEM DESCRIPTION
> Two functions, one encodes strings in an array returns a string
> Decode: decodes a string given by this encode function into the array again.
> Optimal Complexities: O(n) and O(n+m) where n is the sum of lengths of the strings and **m** is the number of strings in the array. 
> 
> - **Catch:** Can only use characters from utf 8. In both input and output.
> - **Hint 1:** Think of how we should encode it. (A separator between the strings is the original naive idea)
> - **Hint 2:** Remember that since the separator character is part of the same encoding it can also appear in the strings themselves. And we also need a way to know how long each word was (so maybe use lengths. But remember that length can be multiple characters and its hard to know where it ends if the word starts with numbers too).



# 🟨 Product of elements except current

> [!Main Concept]
> ## 🟡 Product of array except self (Prefix Product, Suffix Product)
> 
> 1. **New concepts: Prefix Product and Suffix Product arrays of an array:**
> 2. _**Prefix Product array:**_ An array where each element at index `i` is the product of all elements of the original array before index `i`.
>     
>     ```Python
>     arr = [1, 2, 3, 4]
>     prefix = [1] * len(arr)
>     for i in range(1, len(arr)):
>         prefix[i] = prefix[i - 1] * arr[i - 1]
>     print(prefix)  # Output: [1, 1, 2, 6]
>     ```
>     
> 3. _**Suffix Product array:**_ An array where each element at index `i` is the product of all elements of the original array to the right (after) index `i`.
>     
>     ```undefined
>     arr = [1, 2, 3, 4]
>     suffix = [1] * len(arr)
>     for i in range(len(ar r)-2, -1, -1):
>         suffix[i] = suffix[i + 1] * arr[i + 1]
>     print(prefix)  # Output: [1, 1, 2, 6]
>     ```
>       

These can be done on one single array that becomes the result since for each element, the products to the left and to the right are multiplied.



# 🟨 Valid Sudoku

>[!IMPORTANT] Important concept PYTHON
>defaultdict behavior in Python:
>- defaultdict(factory) creates default values automatically for missing keys using factory()
>-  factory must be a callable like list, set, dict — not an object like [] or {}
>- accessing a missing key calls factory() and assigns the result to that key
example with set:
>```python
from collections import defaultdict  
rows = defaultdict(set)  
rows['a'].add(1) 
rows['a'].add(2) 
rows['b'].add(3)  
print(rows) # {'a': {1, 2}, 'b': {3}}
>```
rows['a'] and rows['b'] are created as empty sets automatically when first accessed

1. The bruteforce solution that i implemented:
   - Have double loops that check for duplicates in each row and then each column and then each small square.
   - This is not optimal. Could be done with 1 double loop only. Have 3 dictionaries for each unit and then add elements to them. So rowdict[0] (row 0) has these elements and so on.

>[!ERROR] Related problem (🟩LEETCODE 2133)
>Above approach works but is too slow. 
>better approaches out there. 
>all of them use set() to check for duplicates.

# 🟨 Leetcode 2661 first to get painted 
### Approach 3: Reverse Mapping

#### Intuition

In Approach 2, we were checking the count of "painted" elements for each row and column after every marking operation. Now, instead of that, we track the greatest index at which an element of each row and column occurs in `arr`. This will reduce space usage and eliminate the need for redundant checks, as we won’t need the `rowCount` and `colCount` arrays anymore.

Similarly to the previous approaches, we begin by mapping each number to its position (index) in `arr`, using a hashmap, `numToIndex`.

Instead of counting marked numbers, we consider a different question: When will a row or column be fully painted? Intuitively, this happens when all the numbers in that row or column have been processed. Building on this idea, we observe that it suffices to track the latest index in `arr` where each number in a row or column appears. If we know the greatest index for any element in a row or column, that row or column will be fully painted once that index is reached.

For example, consider a row of `mat`, which contains the numbers 3, 5, and 8. If their indices in `arr` are 1, 3, and 2 respectively, the row will be fully painted when index 3 (the largest index for any number in that row) in arr is reached.

After determining the greatest index for each row and column, we identify the row or column with the smallest maximum index, as this represents the first to be fully painted.

The algorithm is visualized below:

![Current](blob:https://leetcode.com/e2a058e4-551b-4ba5-adf0-f79da267e55b)

1 / 7

#### Algorithm

- Initialize a `numToIndex` unordered map to store the index of each element from `arr`.
    
- Populate `numToIndex` by iterating over the `arr` and recording the index of each element.
    
- Initialize `lastElementIndex` to `INT_MAX` and `result` to `INT_MIN` to track the earliest complete row or column.
    
- Initialize `numRows` and `numCols` to the number of rows and columns in the matrix `mat`, respectively.
    
- Check for the earliest row to be completely painted:
    
    - Iterate through each row in the matrix `mat`:
        - Initialize `result` to `INT_MIN` for each row.
        - Iterate through each column in the current row:
            - For each element in the row, find its index in `numToIndex` and update `result` with the maximum of its current value and index of the current element in `arr`.
        - Update `lastElementIndex` with the minimum of `lastElementIndex` and the row's `result`.
- Check for the earliest column to be completely painted:
    
    - Iterate through each column in the matrix `mat`:
        - Initialize `result` to `INT_MIN` for each column.
        - Iterate through each row in the current column:
            - For each element in the column, find its index in `numToIndex` and update `result` with the maximum index value.
        - Update `lastElementIndex` with the minimum of `lastElementIndex` and the column's `result`.
- Return `lastElementIndex`, which represents the earliest index where a row or column has been completely painted.
    

#### Implementation
```cpp
class Solution {
public:
    int firstCompleteIndex(vector<int>& arr, vector<vector<int>>& mat) {
        // Map to store the index of each number in the arr
        unordered_map<int, int> numToIndex;
        for (int i = 0; i < arr.size(); i++) {
            numToIndex[arr[i]] = i;
        }

        int result = INT_MAX;
        int numRows = mat.size();
        int numCols = mat[0].size();

        // Check for the earliest row to be completely painted
        for (int row = 0; row < numRows; row++) {
            // Tracks the greatest index in this column
            int lastElementIndex = INT_MIN;
            for (int col = 0; col < numCols; col++) {
                int indexVal = numToIndex[mat[row][col]];
                lastElementIndex = max(lastElementIndex, indexVal);
            }
            // Update result with the minimum index where this row is fully
            // painted
            result = min(result, lastElementIndex);
        }

        // Check for the earliest column to be completely painted
        for (int col = 0; col < numCols; col++) {
            // Tracks the greatest index in this column
            int lastElementIndex = INT_MIN;
            for (int row = 0; row < numRows; row++) {
                int indexVal = numToIndex[mat[row][col]];
                lastElementIndex = max(lastElementIndex, indexVal);
            }
            // Update result with the minimum index where this column is fully
            // painted
            result = min(result, lastElementIndex);
        }

        return result;
    }
};

```

#### Complexity Analysis

Let k=m⋅n be the size of `arr` (since arr.length==m⋅n), m the number of rows in `mat`, and n the number of columns in `mat`.

- Time complexity: O(m⋅n)
    
    We first build a map to store the index of each element in `arr`, which takes O(k) time. Then, we check for the earliest row and column to be completely painted, which takes O(m⋅n) time. Since k=m⋅n, the total time complexity is O(m⋅n).
    
- Space complexity: O(k)≡O(m⋅n)
    
    We use a map to store the index of each element in `arr`, which requires O(k) space. Other variables use constant space, so the total space complexity is O(k)≡O(m⋅n).




# 🟨 Longest consecutive sequence.
- The thing to realize is that a sequence cannot start at value n if n-1 is already in the list. 

Related problem: longest alphabetical substring. Pretty easy, think of character's unicode values instead of creating a-z string.
