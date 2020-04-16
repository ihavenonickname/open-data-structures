At first, both structures are empty.

```none
s q
```

Then, some elements are appended into `s`.

```none
(append 1 to s)
s q
1

(append 2 to s)
s q
1
2

(append 3 to s)
s q
1
2
3
```

Every element in `s` is popped and then appended into `q`.

```none
(pop from s and append into q)
s q
1 3
2

(pop from s and append into q)
s q
1 3
  2

(pop from s and append into q)
s q
  3
  2
  1
```

Now, every element in `q` is popped and then appended back to `s` again.

```none
(pop from d and append into s)
s q
3 2
  1

(pop from d and append into s)
s q
3 1
2

(pop from d and append into s)
s q
3
2
1
```

Finally `s` ends up with all its elements again, but in reverse order.

It happens because `q` preserves the order of the elements while `s` pops them in reverse order. So when `q` returns the elements back to `s` they end up reversed.

In other words, the elements are reversed when `s` passes them to `q`.

A python implementation:

```python
from collections import deque

s = []

# Put something into the stack

s.append(1)
s.append(2)
s.append(3)

# Now it's [1, 2, 3]

d = deque()

# Reversing happens here!
d.append(s.pop())
d.append(s.pop())
d.append(s.pop())

# Now the stack is empty
# Put elements back to it

s.append(d.popleft())
s.append(d.popleft())
s.append(d.popleft())

# Stack is [3, 2, 1]
```
