# **SESSION 1**

1. `enumerate()` function takes an iterable and a starting index, then returns the indices with the values of the iterable:

```Bash
string = 'code'
for index, char in enumerate(string):
	print(index, char)
```

  

1. `zip(iterable1, iterable2...)` function takes some iterables as inputs and returns an iterable which needs to be converted to a list, with tuples containing elements of each iterable at each index

  

1. .extend extends a list using another list.

  

1. mergesort, insertionsort, quicksort, selectionsort
2. Always use array methods like append and get to be safe. When trying to do arr[i] = b for an empty array arr, will cause an indexerror.

  

> [!important]
> 
> ## Missing Clues (missing Intervals)
> 
> no need to iterate from lower to upper. can find the intervals by looking at the difference between all the clues (sorted) the lower and final clue and upper.
> 
> The task is to identify missing numbers in a specified range “lower” to “upper” that are not present in a given list of clues. The result should return a list of ranges covering all missing numbers.
> 
> ### Approaches to Solve the Problem
> 
> ### 1. **Sentinel-Based Approach**
> 
> - **Algorithm**:
>     1. Sort the `clues` list.
>     2. Add sentinel values (“lower-1” and “upper + 1”) to handle boundaries seamlessly.
>     3. Iterate through consecutive elements in the extended list to identify gaps.
>     4. For each gap, add the range `[start, end]` to the result.
> - **Time Complexity**:
>     - Sorting `clues`: , where is the size of `clues`.
>     - Iterating through `clues`: .
>     - **Total:** .
> - **Space Complexity**:
>     - Sorting and extending the list requires space.
>     - **Total:** .
> - **Pros**:
>     - Efficient when the size of the `clues` list is small compared to the range size.
>     - Avoids iterating through the entire range, making it suitable for sparse inputs.
> - **Cons**:
>     - Requires sorting the input, which adds overhead if the input is already sorted.
>     - Slightly more complex to understand due to sentinel values.
> 
> ### 2. **Range Iteration Approach**
> 
> - **Algorithm**:
>     1. Iterate through all numbers in the range `[lower, upper]`.
>     2. Check if each number is present in the `clues` (using a set for efficient lookup).
>     3. Start a new range when a missing number is found and end the range when a number from `clues` is encountered.
> - **Time Complexity**:
>     - Iterating through the range: .
>     - Checking membership in the set: per lookup.
>     - **Total:** .
> - **Space Complexity**:
>     - Set storage for `clues`: .
>     - **Total:** .
> - **Pros**:
>     - Simple and easy to implement.
>     - Intuitive and requires no sorting or additional manipulation.
> - **Cons**:
>     - Inefficient for large ranges, as it processes every number regardless of `clues` size.
>     - Better suited for small ranges or dense inputs.
> 
> ### When to Use Each Approach
> 
> - **Sentinel-Based Approach**:
>     - Ideal when the range “lower” to “upper” is large, and the `clues` list is small or sparse.
>     - Preferred for optimizing performance when gaps are far apart.
> - **Range Iteration Approach**:
>     - Suitable for small ranges or when clarity and simplicity are more important than efficiency.
>     - Best for dense inputs where the range is mostly filled.
> 
>   
> 
> one approach deals with the difference in the clues and one deals with the presence of integers between lower and upper in clues.
> 
>   
> **APPROACH 1 (T: O(n) S: O(n)): Fast, complex**
> 
> ```Python
> def find_missing_clues(clues, lower, upper):
>     eclues = [lower - 1] + sorted(clues) + [upper + 1]
> 
>     ranges = []
>     for i in range(len(eclues)-1):
>         curr = eclues[i]
>         next = eclues[i+1]
> 
>         if (next - curr) > 1:
>             start = curr + 1
>             end = next - 1
>             ranges.append([start, end])
>     print(ranges)
>     return ranges
> ```
> 
>   
> **APPROACH 2 (T: O(upper - lower): Easier to understand, more intuitive, slower for big intervals upper - lower (if they are way bigger than the length of clues list)**
> 
> ```Python
> def find_missing_clues(clues, lower, upper):
>     currRange = None
>     ranges = []
>     clues = sorted(clues)
> 
>     for i in range(lower, upper+1):
>         if i not in clues:
>             \#range was not being created
>             if currRange == None:
>                 currRange = [i]
>         
>         else:
>             if currRange != None:
>                 currRange.append(i-1)
>                 ranges.append(currRange)
>                 currRange = None
>     
>     print(ranges)
>     return ranges
> ```

  

> [!important]
> 
> ## Good Samaritan (meals, capacity) Problem (Greedy : min number of boxes)
> 
> 1. Sorting in descending order will help
> 2. If changing the original capacity array is allowed, solution is simpler since each element is updated to the remaining meal capacity every iteration.
> 3. best solution is O(mlogm + n) which comes from sorting, summing and looping
> 4. If we don’t want to change the array elements, we will need two variables currBox and currMeal to keep track of the current meal and box size remaining.
> 5. To take care of the case if capacity cannot accomodate all the meals, we calculate the sums and compare in the beginning O(m+n)
> 6. Possible better solution: sum the meals and subtract capacities from it (is the better solution i think)  
>       
>       
>     
> 
> ### All Solutions
> 
> 1. Sorting the lists and changing elements (Greedy, two pointer) $O(mlogm + nlogn)$
> 
> ```Python
> def minimum_boxes(meals, capacity):
>     i = 0
>     j = 0
>     count = 0
>     meals = sorted(meals, reverse=True)
>     capacity = sorted(capacity, reverse=True)
>     
>     while i < len(meals) and j < len(capacity):
>         c = capacity[j]     
>         m = meals[i]
>         if (c>m):
>            
>             diff = c-m
>             meals[i] = 0
>             capacity[j] = diff
>             i += 1
>             if(i >= len(meals)):
>                 count += 1
>         
>         elif(m>c):
>            
>             diff = m-c
>             meals[i] = diff
>             capacity[j] = 0
>             count += 1
>             j += 1
>         
>     
>         else:
>             
>             capacity[j] = 0
>             count += 1
>             i += 1
>             j +=1 
> 
>     print(count)
>     return count
> ```
> 
> 1. Same as above, but without modifying the list elements $O(mlogm + nlogn)$  
>       
>     
> 
> ```Python
> def minimum_boxes(meals, capacity):
>     i = 0
>     j = 0
>     count = 0
>     meals = sorted(meals,reverse=True)
>     capacity = sorted(capacity, reverse=True)
> 
>     currBox = capacity[j]
>     currMeal = meals[i]
>     while i < len(meals) and j < len(capacity):
>         
>         if(currBox > currMeal):
>             diff = currBox - currMeal
>             i += 1
>             if (i >= len(meals)):
>                 count +=1
>                 break
>             currMeal = meals[i]
>             currBox = diff
> 
> 
>         
>         elif(currBox < currMeal):
>             diff = currMeal - currBox
>             currMeal = diff
>             j += 1
>             currBox = capacity[j]
>             count += 1
> 
>         else:
>             j += 1
>             i += 1
>             count +=1
> 
>             if i < len(meals) and j <len(capacity):
>                 currBox = capacity[j]
>                 currMeal = meals[i]
>             
>     print(count)
>     return count
> ```
> 
> 1. Final (Possibly the best solution) $O(mlogm + m + n)$ $\approx$ $O(mlogm + n)$, Is Greedy too, but shorter and easier to understand.  
>       
>     
> 
> ```Python
> def minimum_boxes(meals, capacity):
>     totalMeals = sum(meals) \#O(n)
>     capacity = sorted(capacity, reverse=True) \#O(mlogm)
>     count = 0
> 
>     if totalMeals > sum(capacity):
>         return  -1
>     
>     for c in capacity:  \#O(m)
>         diff = totalMeals - c
> 
>         if(diff <= 0):
>             count += 1
>             break
> 
>         elif(diff >= 1):
>             totalMeals = diff
>             count += 1
>     
>     print(count)
>     return count
> ```

  

> [!important]
> 
> ## Local Maxes of a Grid
> 
> 1. Best solution is $O(n^2)$ The best space complexity can be $O(1)$ if we change the grid array to make the result array.
> 2. The same solution in C++ runs faster than in python. (about 4 times) Reasons:
>     
>     1. Dynamic typing in python. (Type inference is done) Extra overhead
>     2. Interpretation in python. (slower than compilation)
>     3. **C++ compilers** (like GCC or Clang) apply various optimizations during the compilation process, such as inlining, loop unrolling, and vectorization, to improve runtime performance. **Python** does not benefit from such optimizations, as it’s an interpreted language.
>     
>     **Summary:** The same logic in C++ is faster because C++ code is translated directly to machine code, which is executed quickly by the CPU. Python, being interpreted and dynamically typed, incurs more runtime overhead and lacks the low-level optimizations present in C++ code.
>     
> 
> ### Solutions:
> 
> ```Python
> def local_maximums(grid):
>     centreLimit = len(grid) - 2
>     result = [[0 for _ in range(centreLimit)] for _ in range(centreLimit)]
>     
>     maximum = 0
> 
>     for i in range(1, centreLimit + 1):
>         for j in range(1, centreLimit + 1):
>             maximum = 0
>             
>             for row in range(-1, 2):
>                 for col in range(-1, 2):
>                     if (grid[i + row][j + col]) >= maximum:
>                         maximum = grid[i+row][j+col]
> 	          
> 	          result[i - 1][j - 1] = maximum
>         
>     
>     print(result)
>     return result
> ```
> 
> in C++
> 
> 1. access modifiers are only needed if working inside a class. If not, then these are not required. and should not be used.
> 2. Important vector syntax in c++:
>     
>     ```C++
>     vector<vector<int>> result(n-2, vector<int>(m, 1));  
>     // n-2 rows, each with m columns, initialized to 1
>     ```
>     
> 
>   
> 
> ```C++
> vector<vector<int>> localmax(vector<vector<int>>& grid){
>     int n = grid.size(); //length
>     vector<vector<int>> result(n-2, vector<int>(n-2, 0));
>     
>     for(int i = 1; i < n-1; i++){
>         for(int j = 1; j < n-1; j++){
>             int maximum = 0;
>             for(int k = i-1; k < i + 2; k++){
>                 for(int l = j-1; l < j + 2; l++){
>                     maximum = max(maximum, grid[k][l]);
>                 }
>             }
> 
>             result[i-1][j-1] = maximum;
>         }
>     }
> 
>     for(int row = 0; row < result.size(); row++){
>         for(int col = 0; col < result.size(); col++){
>             cout << result[row][col] << " ";
>         }
>         cout << '\n';
>     }
>     cout << endl;
>     return result;
> }
> ```
> 
> One more possible solution where we modify the original grid to make space complexity O(1).

  

## C++ NOTES:

  

### Why We cannot bind rvalue to a non const lvalue:

The simple answer is that _in most cases_, passing a temporary to a function that expects a mutable lvalue reference indicates a **logic error**, and the c++ language is doing its best to help you avoid making the error.

The function declaration: `void foo(Bar& b)` suggests the following narrative:

> foo takes a reference to a Bar, b, which it will modify. b is therefore both an input and an output

Passing a temporary as the output placeholder is _normally_ a much worse logic error than calling a function which returns an object, only to discard the object unexamined.

For example:

```C++
Bar foo();

void test()
{
  /*auto x =*/ foo();  // probable logic error - discarding return value unexamined
}
```

However, in these two versions, there is no problem:

`void foo(Bar&& b)`

> foo takes ownership of the object referenced by Bar

`void foo(Bar b)`

> foo conceptually takes a copy of a Bar, although in many cases the  
> compiler will decide that creating and copying a Bar is un-necessary.  

So the question is, what are we trying to achieve? If we just need a Bar on which to work we can use the `Bar&& b` or `Bar b` versions.

If we want to _maybe_ use a temporary and _maybe_ use an existing Bar, then it is likely that we would need two overloads of `foo`, because they would be semantically subtly different:

```C++
void foo(Bar& b);    // I will modify the object referenced by b

void foo(Bar&& b);   // I will *steal* the object referenced by b

void foo(Bar b);   // I will copy your Bar and use mine, thanks
```

If we need this optionality, we can create it by wrapping one in the other:

```C++
void foo(Bar& b)
{
  auto x = consult_some_value_in(b);
  auto y = from_some_other_source();
  modify_in_some_way(b, x * y);
}

void foo(Bar&& b)
{
  // at this point, the caller has lost interest in b, because he passed
  // an rvalue-reference. And you can't do that by accident.

  // rvalues always decay into lvalues when named
  // so here we're calling foo(Bar&)

  foo(b);

  // b is about to be 'discarded' or destroyed, depending on what happened at the call site
  // so we should at least use it first
  std::cout << "the result is: " << b.to_string() << std::endl;
}
```

With these definitions, these are now all legal:

```C++
void test()
{
  Bar b;
  foo(b);              // call foo(Bar&)

  foo(Bar());          // call foo(Bar&&)

  foo(std::move(b));   // call foo(Bar&&)
  // at which point we know that since we moved b, we should only assign to it
  // or leave it alone.
}
```

OK, by why all this care? Why would it be a logic error to modify a temporary without meaning to?

Well, imagine this:

```C++
Bar& foo(Bar& b)
{
  modify(b);
  return b;
}
```

And we're expecting to do things like this:

```C++
extern void baz(Bar& b);

Bar b;
baz(foo(b));
```

Now imagine this could compile:

```C++
auto& br = foo(Bar());

baz(br); // BOOM! br is now a dangling reference. The Bar no longer exists
```

Because we are forced to handle the temporary properly in a special overload of `foo`, the author of `foo` can be confident that this mistake will never happen in your code.

  

### Pointers, References and Ampersand &:

**Address-of Operator:**

- **Purpose:** Obtains the memory address of a variable.
- **Example:**
    
    `int a = 5; int* ptr = &a; // 'ptr' now holds the address of 'a'`
    
- **Explanation:** Here, `&a` retrieves the address of the variable `a` and assigns it to the pointer `ptr`.

**Reference Declaration:**

- **Purpose:** Declares a reference to a variable, allowing direct access to the original variable.
- **Example:**
    
    `int a = 5; int& ref = a; // 'ref' is a reference to 'a'`
    
- **Explanation:** In this case, `ref` becomes an alias for `a`. Any changes made to `ref` will directly affect `a`, and vice versa.

### C++ Iterators:

1. **What is an Iterator?**
    - An **iterator** is an object that allows traversal through a container (e.g., `std::vector`, `std::list`).
    - Provides operations like `++` (increment), `-` (decrement), and (dereference).
2. **How Iterators Work**:
    - Iterators **point to elements** in a container but don't store the container's size or structure.
    - They support operations like `++` and , which are implemented according to the container type.
    - **Container-specific iterators**: Each container type (e.g., `std::vector`, `std::list`) has its own specialized iterator.
3. **Iterator Movement**:
    - **Increment (**`**++**`**)**: Moves to the next element in the container (specific to the container’s structure).
    - **Decrement (**`**-**`**)**: Moves to the previous element.
    - **Dereferencing (****)**: Accesses the element the iterator points to.
4. **Container Types**:
    - `**std::vector**`: Iterator behaves like a pointer (contiguous memory), allows random access.
    - `**std::list**`: Iterator is a pointer to nodes (non-contiguous memory), supports bidirectional access.
5. **Iterators as Nested Classes**:
    - **Iterators are often nested classes** inside the container class.
    - These nested iterator classes implement the logic needed to traverse the container.
6. **Iterator Interface**:
    
    - Containers define an iterator interface that supports operations like `++`, `-`, , `!=`, etc.
    - The iterator doesn't need to know the container’s internal structure; the container defines how the iterator operates.
    - **Iterator Validity**: An iterator is valid only for the container it was created from. Each iterator knows how to traverse its specific container (e.g., a `std::vector` iterator knows how to move through a vector, while a `std::list` iterator knows how to move through a list). If you try to use an iterator from one container to access elements from a different container, the behavior is **undefined** and will likely result in a runtime error or crashes.
    
      
    
      
    
      
    

# Session 2

> [!important]
> 
> ## Removing duplicates from an array
> 
> 1. Original solution that i thought was **two pointer** which works and has a time complexity of O(n) and a space complexity of O(n):
> 
> ```Python
> def remove_dupes(items):
>     result = []
>     first = 0
>     second = 1
> 
>     result.append(items[first])
> 
>     while (second < len(items)):
>         if(items[first] == items[second]):
>             second += 1
>         
>         elif(items[first] != items[second]):
>             first = second
>             result.append(items[first])
>             second+=1
>     
> 
>     print(result)
>     return result
> ```
> 
> 1. Optimal solution: time is still O(n) but space is O(1) and also reduces the time overhead for accessing a different array. Have a uniqueIndex index variable that keeps track of each unique index and move it everytime when a new element is encountered and assign the new element discovered. must resize the array at the end.
> 
> ```Python
> def remove_dupes(items):
> 
>     uniqueIndex = 0
> 
>     for i in range(len(items)):
>         if(items[uniqueIndex] != items[i]):
>             uniqueIndex += 1
>             items[uniqueIndex] = items[i]
> 
> 
>     items = items[:uniqueIndex+1]
>     
>     print(items)
>     return items
> ```
> 
>   

  

1. for mergesort:
    - **Use** `**(left + right) / 2**` if you are working in a language that handles overflow gracefully (e.g., Python).
    - **Use** `**left + (right - left) / 2**` for general-purpose algorithms or in languages prone to integer overflow (e.g., C++ or Java).

  

> [!important]
> 
> ## 🟩 Sort By Parity
> 
> 1. Using any sorting algorithm (merge, selection, insertion, quick, etc.) and modifying it for odd and even integers should do the trick however is unneccessary and is not optimal.
> 2. Reason for above is that we don’t have to sort each item absolutely (numerically) and don’t need to compare all of them to each other. We only need to put the even numbers to the front and the odd integers to the back (Seperate them).
> 
> Optimal Solution: $O(n), O(1)$
> 
> ```Python
> def sort_by_parity(nums):
>     i = 0
>     j = len(nums) - 1
> 
>     while i < j:
>         if (nums[i] % 2 == 0):
>             i+=1
>         
>         if (nums[j] % 2 == 1):
>             j -= 1
>         
>         if (nums[j] % 2 == 0 and nums[i] % 2 == 1):
>             print('Entered')
>             temp = nums[j] 
>             nums[j] = nums[i]
>             nums[i] = temp
>             print(nums)
> 
> 
>     return nums
> ```

  

> [!important]
> 
> ## 🟨 Biggest Honey Container
> 
> 1. Two pointers, one at the beginning one at the end.
> 2. The catch is, the area is not being calculated by multiplying both the heights, but instead, by squaring the smaller height and hence, the smaller height is what limits an area and so whichever pointer points to the lower height must be moved. Every iteration the maximum is to be calculated of course.
> 3. Related image
>     
>     ![[container_with_most_water_ex1.jpg]]
>     
> 4. Optimal: $O(n), O(1)$:
> 
> ```Python
> def most_honey(height):
>     i = 0
>     j = len(height) - 1
> 
>     maximum = 0
> 
>     while i < j:
>         maximum = max(maximum, min(height[i], height[j])*(j-i))
>         if height[i] < height[j]:
>             i += 1
>         
>         elif height[j] < height[i]:
>             j -= 1
>         
>         else:
>             i += 1
>             
>     
>     print(maximum)
>     return maximum
> ```
> 
> ## 🟩 Best time to Buy and Sell a Stock I (Sliding Window)
> 
> 1. ==Similar to the above problem at first look== but **sliding window** approach is better than two pointer for this one. We have a buy variable set to the first day price and then a for loop that starts after that and goes until the end.
> 2. Every time a smaller price is encountered, buy is set to that
> 3. Otherwise, if the difference between price and buy is more than the maxprofit variable, it is set to that.
> 
> Approach 1: Sliding Window , Optimal: O(n), O(1):
> 
> ```C++
> int maxProfit(vector<int>& prices) {
>         int buy = prices[0];
>         int maxProfit = 0;
> 
>         for(int i = 1; i < prices.size(); i++){
>             if(prices[i] < buy){
>                 buy = prices[i];
>             }
> 
>             else if(prices[i] - buy > maxProfit){
>                 maxProfit = prices[i] - buy;
>             }
>         }
>         return maxProfit;
>     }
> ```
> 
> ==Approach 2: Two pointer, O(n), O(1) slower because of more operations, more variables, and a while loop instead of a for loop:==
> 
> ```C++
> int maxProfit(vector<int>& prices) {
>         int i = 0;
>         int j = 1;//memory overhead
>         int maxProfit = 0;
>         if (prices.size() <= 1){
>             return 0;
>         }//slows things 
>         
>         while (i < prices.size() && j < prices.size()){ //slows things
>             if(prices.at(j) <= prices.at(i)){
>                 i = j; 
>                 j++; //two operations instead of just one
>             }
>             else{
>                 maxProfit = max(maxProfit, prices.at(j) - prices.at(i));
>                 j++; // again two operations instead of just one
>             }
> 
>         }
>         return maxProfit;
>     }
> ```

  

> [!important]
> 
> ## 🟨 Merge Intervals
> 
> 1. There are a couple approaches that all follow the same basic logic of finding an overlap and not finding one (starting a new interval).
> 2. Sorting is important according to the interval start values for each interval
> 3. Then we just start at 1 and iterate until the end and check for overlaps
> 4. How? if the closing interval of the current big interval is bigger or equal to the starting value of a different interval, there is an overlap, in this case, the new closing value for big interval becomes the max of the two closing values.
> 5. If there is no overlap, the current interval we were working with gets added to the result arrray, and this can be in place or by creating a resultant array
> 6. after the loop, we have to add the last interval we are working with to the resultant array.
> 
>   
> 
> Approach 1 O(nlogn), O(1) but is slower because has more variables and an interval array:
> 
> ```Python
> def merge_intervals(intervals):
>     intervals = sorted(intervals)
>     currInt = intervals[0][:] \#must copy and create new array
>     # and not access the actual array 
>     k = 0
> 
>     for i in range(1, len(intervals)):
>        
>         if(currInt[1] >= intervals[i][0]):
>             currInt[1] = max(intervals[i][1], currInt[
>             
>         
>         else:
>             
>             intervals[k] = currInt
>             currInt = intervals[i]
>             k += 1
>             
> 
>     intervals[k] = currInt
>     
>     intervals = intervals[:k+1]
>     
>     
>     print(intervals)
>     return intervals
> ```
> 
> The above can be done in a different way also, where we dont have a currInt array but instead have two variables small index, big index to keep track of the opening and closing value for currInt (almost the same)  
>   
> ==Approach 2: Optimal: O(nlogn), O(1)==
> 
> The approach is the same, however everything happens in place
> 
> ```C++
> vector<vector<int>> merge(vector<vector<int>>& intervals) {
>         sort(intervals.begin(), intervals.end()); // Sort intervals by start time
>         int k = 0; // Index for merged intervals
>         
>         for (int i = 1; i < intervals.size(); i++) {
>             if (intervals[k][1] >= intervals[i][0]) { // Overlap detected
>                 intervals[k][1] = max(intervals[k][1], intervals[i][1]); // Merge
>             } else {
>                 k++; // Move to the next position
>                 intervals[k] = intervals[i]; // Bring the current interval to kth pos
>             }
>         }
>         
>         intervals.resize(k + 1); // Resize to include only merged intervals
>         return intervals;
> 
>     }
> ```

  

> [!important]
> 
> ## 🟨 Three sum (Given an array, return all triplets of nums (a, b, c) where a + b + c = 0 and $a,b,c \in array$
> 
> 1. Use two pointers. Hashmaps uneccessarily complicate things.
> 2. Sort the list. Very important to remember how duplicates behave when sorted. We have to skip these duplicates for all k, i and j values.
> 3. Think about how the combinations work. Do we have to check all the elements from 0 to end for all nums[k] ? :
>     
>     - No, we dont. why? Because if a comes before b and c, we dont need to check a when nums[k] = b. since this case would have been covered when nums[k] = a. (CRITICAL POINT)
>     - This also means that we only check up to the third-last element. Because after that, if there exist any combinations, they would have been explored by that time.
>     
>       
>     
>     ```Plain
>     PSEUDOCODE:
>     given nums
>     
>     instantiate answer array
>     n = len(nums)
>     
>     for k from 0 to n-2
>     	if k is not the first element:
>     		skip if duplicate (same as the one before), continue
>     		
>     		i = k + 1 (avoids duplication and considers CRITICAL POINT above. 
>     		j = len(nums)-1
>     		
>     		while i is smaller than j:
>     			if sum is larger than 0:
>     				decrease j (to a smaller number)
>     			
>     			elif sum is smaller than 0:
>     				increase i (to a larger number)
>     			
>     			else (if sum is 0):
>     				add to the answer array
>     				increase i
>     				decrease j
>     				
>     				then make sure if there are any duplicates at i and j skip over 
>     				all of them.
>     				
>     				(don't break here because there might be other combinations 
>     				ahead that lead to the 0 sum for this k)
>     				
>     				
>     				
>     return the result
>     
>     ```
>     
>       
>     
>     Time Complexity is O(n^2): for loop and then the while loop inside. The two smaller while loops basically shorten the main while loop so they’re not extra nested loops.
>     
>     Space Complexity is O(n) the return array
>     
>       
>     
>     DON’t SKIP OVER DUPLICATES FIRST THING. DO THE OPERATIONS WITH THE FIRST INSTANCES OF THE DUPLICATES. THEN SKIP OVER ANY THAT COME AFTER.
>     
>       
>     
>     There are more optimal ways to write the code than this to make it faster in python. But the core algorithms remains same.
>     

  

> [!important]
> 
> ## 🟨 Insert Interval
> 
> 1. Think faster. Eliminate ideas fast.
> 2. Think about how you have to keep track of an index going to keep track of which elements to insert into the result array. (while loop instead of for loop to continue where left off)
> 3. Think how sorted intervals behave: Think of the possibilities (how would an overlap be detected? what if interval is smaller(no overlap), what if it is bigger (no overlap))
> 4. How to detect the right position? which one of the intervals to just skip
> 
>   
> 
> ```Plain
> PSEUDOCODE:
> while (i < len(intervals) and closing values of intervals are smaller 
> than opening of newInterval):
> 	add these smaller intervals to the result array
> 	keep increasing i (move forward)
> 	
> Now, we know that remaining intervals are bigger or overlap.
> while (i < len(intervals) and opening values of intervals are smaller or equal to 
> the closing of newInterval, which means an overlap, since these intervals bigger):
> 	give new values to newInterval (merge)
> 	change the newInterval to be [min(currInterval[0] and newInterval[0], ....]
> 	keep doing the above by increasing for each bigger interval that might overlap
> 	
> no more intervals left, or no more overlaps left: add present newInterval to result
> 
> add remaining intervals if any.
> 
> return result.	
> ```