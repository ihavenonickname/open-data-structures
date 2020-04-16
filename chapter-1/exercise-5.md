In this exercise there's no need to implement an efficient bag.

The goal is to use the `Bag` class like:

```python
from decimal import Decimal

bag = Bag()

# These three values are considered equal in Python
# assert 1 == 1.0 == Decimal(1)
bag.add(1)
bag.add(1.0)
bag.add(Decimal(1))

print(bag.size_unique()) # Prints 1 because there's 1 unique item
print(bag.size()) # Prints 3 because there's 3 actual items

print(bag.find(Decimal(1))) # Prints any of: 1 1.0 Decimal('1.0')
print(bag.find_all(1.0)) # Prints all of them

print(bag.find(2)) # Prints None
print(bag.find_all(2)) # Prints empty list
```

---

**Note**

The `==` operator is syntax sugar. It's translated to a call to `__eq__` method.

`a == b` is actually `a.__eq__(b)`

Because numbers are objects in Python, their `__eq__` method is overloaded so that comparing integers and floats and decimals works as one should expect.

---

My implementation starts with the declaration of a `list` which'll be used to hold all values in `Bag`.

```python
class Bag():
    def __init__(self):
        self._data = []
```

`self._data` is going to be a list of lists. Each inner list will hold the values that are equal to a given element.

For example, considering the example above the `self._data` function would be

```python
[
    [1, 1.0, Decimal(1)]
]
```

If you added `2` and `2.0` to the bag, the the internal list would become

```python
[
    [1, 1.0, Decimal(1)],
    [2, 2.0]
]
```

All methods use a helper function that returns the index of a given object in `self._data`

```python
    def _find_index(self, x):
        for i, item in enumerate(self._data):
            if item[0] == x:
                return i

        raise IndexError()
```

Note that comparting `x` to the first element of each inner list is sufficient, because all the others elements in that inner list respond equally to `==`.

And this function is also why this `Bag` implementation is going to be extremelly inefficient -- All operations are `O(n)`, while state-of-the-art sets have `O(1)` time complexity.

Adding `x` appends it to its inner list, or creates its own inner list if not found.

```python
    def add(self, x):
        try:
            self._data[self._find_index(x)].append(x)
        except IndexError:
            self._data.append([x])
```

Removing `x` deletes its inner list. All elements equal to `x` are also removed.

```python
    def remove(self, x):
        try:
            del self._data[self._find_index(x)]
        except IndexError:
            pass
```

Finding all elements equal to `x` yields a copy of its inner list, or an empty list if not found.

```python
    def find_all(self, x):
        try:
            return self._data[self._find_index(x)][:]
        except IndexError:
            return []
```

---

**Note**

This funny syntax `myList[:]` creates a shallow copy of `myList`.

---

Finding a single element equal to `x` yields the first element of its inner list, or `None` if not found.

```python
    def find(self, x):
        try:
            return self.find_all(x)[0]
        except IndexError:
            return None
```

I decided to create two functions for the size. One of them returns the # of unique elements, while the other returns the # of actual elements (i.e. considers duplicates).

```python
    def size_unique(self):
        return len(self._data)

    def size(self):
        return sum(map(len, self._data))
```
