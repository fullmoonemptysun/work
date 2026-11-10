## 1. Selection sort O($n^2$)

## 2. Bubble sort O($n^2$), O($n$) in best case (early stoppage).
- set r to the last index
- start at the beginning, if element at i < element at i+1 swap elements at i and i+1 up until r. The largest element gets sent to the rightmost index in the current range (r).
- then decrease r and repeat until r = 0.
- Early stoppage: to optimize further, if in any pass the total number of swaps is 0. Exit the loops. 

## 3. Insertion Sort $O(n^2)$, $O(n)$ in best case (early stoppage)
- Insertion of a card into a pre sorted left side. 
- Early Stoppage: If array is sorted earlier than all the elements have been explored, then there will be at most 1 comparison for each elements remaining making the complexity O(n). 

## 4. Merge Sort O(nlogn)
- Understand that all the left side is taken care of first before the function is even called on the right side. 

