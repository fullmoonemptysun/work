# `unordered_set`

- A container structure that stores elements in unordered manner.
- Any operation takes O(1) time.
- Important functions:
	- `insert(element)`: insert an element (***Insertion*)
	- `begin()`: returns an iterator pointing to the beginning of the set. (***Iterator to the start*)
	- `end()`: returns iterator pointing to the end of the set. (***Iterator to the End + 1*)
	- `count(element)` : returns 1 if element present in the set or 0 if not. (***Presence***)
	- `size()`: returns the ***Size*** of the set.
	- `find(element)`: find an element in the set.
### Other functions:

- **cbegin()** – it refers to the first element of the unordered set.
- **cend()** – it refers to the theoretical element after the last element of the unordered set.
- **bucket_size()** - gives the total number of elements present in a specific bucket in an unordered set.
- **emplace()** - to insert an element in the unordered set.
- **max_size()** - the maximum elements an unordered_set can hold.
- **max_bucket_count()** - to check the maximum number of buckets an unordered set can hold.



# `vector`
- Dynamic arrays storing elements in contiguous memory. 
- functions:
	- `begin()`: Return an iterator to the starting element
	- `end()`: Return an iterator to the last element + 1
	- `push_back()` : Insert an element at the end of the vector
	- `insert(iterator, element)` : inserts element at iterator position.
	- `erase(iterator)`: erase element at iterator position.
	- `pop_back()` : pop the last element. 
	- `first()` : returns a reference to the first element of the vector.
	- `back()`: returns a ref to the last element.
	- `size()`: returns the size of the vector.
	- `empty()` : checks if vector is empty or not.
	- `clear()`: clears a vector.


**Other Functions:**
- **cbegin()** - it refers to the first element of the vector.
- **cend()** - it refers to the theoretical element after the last element of the vector.
- **rbegin()** - it points to the last element of the vector.
- **rend()** - it points to the theoretical element before the first element of the vector.
- **crbegin()** - it refers to the last element of the vector.
- **crend()** - it refers to the theoretical element before the first element of the vector.
- **max_size()** - returns the maximum size the vector can hold.
- **capacity()** - it returns the current capacity of the vector.


# `set`

- basically an ordered set. (in a particular order).
- can't have duplicates (no sets can)
- Operations take O(1) solution in best case and O(n) in the worst case. 

- functions: 
	- `insert()` 
	- `s.begin()` 
	- `s.end()`
	- `s.clear()`
	- `s.find()`
	- `s.erase()`
	- `s.size()`
	- `s.empty()`


# `unordered_multiset`

- Same as `unordered_set` but it can have duplicate elements in it. 

>[!important] Associative containers
> An **associative container** in C++ is a standard library class template that stores elements for **fast lookup based on keys** rather than their position, guaranteeing **logarithmic time complexity** ($O(\log n)$) for insertion, deletion, and search operations.
# `multiset` 
- Same as a `set` but can have duplicate elements.


# `unordered_map`
- key and value pairs
- can't have duplicate keys. 
- orderless

- functions:
	- `insert(key, value)`
	- `.begin()` `.end()` : iterators to go over the elements
	- `.find(key)`
	- `.erase(key)`
	- `.size()`
	- `.empty()`
	- `.clear()`


# `map`

- ordered (alphabetical, numerical, et cetera.)

# `unordered_multimap`

- `unordered_map` but duplicate keys allowed. 
- rarely needed.
- often replaced by easier:
`unordered_map<type1, vector<type2>>`

# `multimap`

- `map` but duplicate keys allowed
- sorted like `map`

# `queue`

- Linear list of elements in which deletions can take place only at one end (front) and insertion can take place only at the other end (rear). **FIFO** 

- functions:
	- `.push(element)`
	- `.pop()`
	- `.front()`: reference to the first element 
	- `.back()` : reference to the last element
	- `.emplace()`: insert an element in the queue.
	- `.size()`
	- `.empty()`


# `stack`
- Linear data structure. 
- Ordered.
- Addition and deletion only happens from one end. **LIFO/FILO**
- function list:
	- `.pop()`
	- `.push()`
	- `.top()`: element on top.
	- `.emplace()` : insert in the stack.
	- `.empty()`
	- `.size()`


# `deque`

- Double ended queue.
- Insertion/Deletion from either side
- functions:
	- `push_back()`
	- `push_front()`
	- `pop_back()`
	- `pop_front()`
	- `.front()`
	- `.back()`
	- `.size()`
	- `.empty()`

# `list`

- linked list.
- functions:
	- `.push_back()`
	- `.push_front()`
	- `.pop_back()`
	- `.pop_front()`
	- `.front()`
	- `.back()`
	- `.reverse()`
	- `.sort()` : sort in ascending order
	-  `.size()`
	- `.empty()`


# `sort(iterator1, iterator2)`

- Different implementations under the hood for different containers. (Mostly quicksort/mergesort). 
- **O(nlogn)** 
- sorts between the range of iterators. 


`min_element(it1, it2)`: return the min element in the range of iterators
`max_element(it1, it2)` return the max.
These functions are valid for any container that defines an iterator. 