## List

Every operation on a singly linked list starts at the head and performs a linear search until it finds the desired element. For this reason, operating at the front of the list is fast, while operating at the back is slow.

Performance can be improved with a doubly linked list, as it can traverse elements from the front or from the back. However, accessing elements in the middle of the list still requires a linear search.

For fast random access it's best to use a memory buffer (array) under the hood. The problem now is that adding to the front requires a shift of all elements, which is slow.

Both approaches have trade-offs:

* Doubly linked lists have fast appends and removals on both ends, but slow access at the middle.
* Array-based lists have fast access anywhere, but slow appends and removals at the front.

In Python, the built-in type `list` is an array-based list, whereas `collenctions.deque` is a doubly linked list. It's up for the programmer to choose the best fit for the use case at hand.

## USet

My implementation of `USet` is quite inneficient because the whole point of a set is having fast membership test, and my implementation uses an O(n) algorithm.

An obvious performance improvement is to use a hash function to generate an integer for every element and use this integer as the index in an array-like container under the hood. In other words, if `container[hash(x)]` is `None` then `x` is not in the set.

Coming up with an efficient hash function however is a non-trivial task.

## SSet

I can't think of a concrete implementation of efficient sorted set right now. Perhaps it's possible to use a balanced tree under the hood, so membership tests are O(log n) ? I'm not sure.
