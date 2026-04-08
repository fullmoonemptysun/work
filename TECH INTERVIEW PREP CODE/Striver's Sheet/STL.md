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
- Operations take O(1) solution in best case and O(n) in the worst case. 

