The `List` interface is supposed to have 5 methods: `size`, `get`, `set`, `add`, and `remove`


```python
l = List()

assert l.size() == 0

l.add(0, 'e')
l.add(0, 'c')
l.add(0, 'a')
l.add(1, 'b')
l.add(3, 'd')
l.add(5, 'f')

assert l.get(0) == 'a'
assert l.get(1) == 'b'
assert l.get(2) == 'c'
assert l.get(3) == 'd'
assert l.get(4) == 'e'
assert l.get(5) == 'f'

l.remove(5)
l.remove(0)
l.remove(2)

assert l.get(0) == 'b'
assert l.get(1) == 'c'
assert l.get(2) == 'e'

l.setval(0, 'B')
l.setval(1, 'C')
l.setval(2, 'E')

assert l.get(0) == 'B'
assert l.get(1) == 'C'
assert l.get(2) == 'E'

assert l.size() == 3
```

I could use the regular `list` class to build my own `List`, but that would go against the intents of the exercise. For this reason I decided to implement my class as a singly linked list.

First, a tiny container class for list nodes.

```python
class ListNode():
    def __init__(self, value, nextnode=None):
        self.value = value
        self.next = nextnode
```

The actual list starts with the declaration of the first node (the head of the list) and a counter for its size.

```python
class List():
    def __init__(self):
        self._head = None
        self._count = 0
```

The `size` method is the easiest one.

```python
    def size(self):
        return self._count
```

Now, a more challenging implementation is the `get` method.

```python
    def get(self, i):
        if i < 0 or i >= self.size():
            raise IndexError()

        current = self._head
        j = 0

        while j != i:
            current = current.next
            j += 1

        return current.value
```

This algorithm calls the next node `i` times and returns its value.

The `setval` method is pretty similar, but it overrides the value of the node instead of returning it.

```python
    def setval(self, i, x):
        if i < 0 or i >= self.size():
            raise IndexError()

        current = self._head
        j = 0

        while j != i:
            current = current.next
            j += 1

        current.value = x
```

The `add` method is arguably the most challenging one because it has a special case: adding to the front of the list.

```python
    def add(self, i, x):
        if i < 0 or i > self.size():
            raise IndexError()

        if i == 0:
            self._head = ListNode(x, self._head)
        else:
            prev = self._head
            j = 1

            while j != i:
                prev = prev.next
                j += 1

            prev.next = ListNode(x, prev.next)

        self._count += 1
```

Note that the first `if` condition uses a `>` instead of `>=` because the user should be able to add to the back of the list.

A gotcha of this algorithm is that it must find the node that resides immediately before the node being added, that's why I called the variable `prev`.

Once the previous node is found, the `next` of `prev` becomes the node being added, and the old `next` becomes the `next` of the node being added. Sounds confusing, but it isn't. See:

Given a singly linked list with 2 elements

```
a — c — Ø
```

Adding a new value to index 1 (i.e. between `a` and `c`) is visualized as

```
a   c -> Ø
 \ /
  b
```

Now, the `remove` method is easy if you understood `add`.

```python
    def remove(self, i):
        if i < 0 or i >= self.size():
            raise IndexError()

        if i == 0:
            self._head = self._head.next
        else:
            prev = self._head
            j = 1

            while j != i:
                prev = prev.next
                j += 1

            prev.next = prev.next.next

        self._count -= 1
```

It also has a special case for the first node and finds the previous node of the one being added. The `next` of the previous node becomes the `next` of the node being removed.

---

**Note**

Python has automatic garbage collectection. Objects without any reference are automatically removed from memory. That's why the `remove`  method don't need to do anything with `prev.next`.

---

This implementation of `List` passes in all tests.

Now it's time to test and implement `USet` class.

```python
s = USet()

assert s.size() == 0

assert s.add(1)
assert s.add(2)
assert s.add(3)

assert not s.add(1)
assert not s.add(2)
assert not s.add(3)

assert s.size() == 3

assert s.remove(3) == 3
assert s.remove(0) == None

assert s.size() == 2

assert s.find(1.0) == 1
assert s.find(2.0) == 2
```

Following the spirit of not using Python built-in structures in this exercise, the `USet` class is built on top of the previous `List` class.

```python
class USet():
    def __init__(self):
        self._data = List()
```

The simplest method is `size`, which just calls the `size` method of the underlying list.

```python
    def size(self):
        return self._data.size()
```

Now it's time to implement the `add` method. Considering the semantics of the set structure, a given element may only be added to the set if it doesn't belong to it yet. This is going to require a linear search in `List`.

I'm gonna use this opportunity to implement the [iterator pattern](https://en.wikipedia.org/wiki/Iterator_pattern) in `List`.

First an iterator is created.

```python
class ListIterator():
    def __init__(self, head):
        self._current = head

    def has_current(self):
        return self._current != None

    def current(self):
        return self._current.value

    def advance(self):
        self._current = self._current.next
```

Then the `List` is made iterable with a method that returns the iterator.

```python
    def iterator(self):
        return ListIterator(self._head)
```

Now, in `USet` a helper method is created -- similar to the one used in exercise 5.

```python
    def _find_index(self, x):
        it = self._data.iterator()
        i = 0

        while it.has_current():
            if it.current() == x:
                return i
            it.advance()
            i += 1

        raise IndexError()
```

And everything is set up to the `add` method.

```python
    def add(self, x):
        try:
            self._find_index(x)
            return False
        except IndexError:
            self._data.add(0, x)
            return True
```

`x` can only be added if `IndexError` is thrown, because it means `x` wasn't found.

`find` is pretty similar to `add` and should be easy to grasp.

```python
    def find(self, x):
        try:
            return self._data.get(self._find_index(x))
        except ValueError:
            return None
```

It turns out that `remove` is the most challenging one because the element being removed must be returned.

```python
    def remove(self, x):
        try:
            i = self._find_index(x)
        except IndexError:
            return None

        y = self._data.get(i)
        self._data.remove(i)

        return y

```

The above implementation of `USet` passes in all tests.

It's time to test and implement `SSet`, most complicated data structure so far.

```python
s = SSet()

assert s.size() == 0

assert s.add(1)
assert s.add(3)
assert s.add(5)
assert s.add(0)
assert s.add(2)
assert s.add(4)
assert s.add(6)

assert not s.add(6)

assert s.size() == 7

assert s.remove(0) == 0
assert s.remove(6) == 6
assert s.remove(0) == None

assert s.size() == 5

assert s.find(0) == 1
assert s.find(1) == 1
assert s.find(3.5) == 4
assert s.find(7.0) == None
```

`SSet` is also going to use a `List` under the hood.

```python
class SSet():
    def __init__(self):
        self._data = List()
```

`size` is the simplest method as usual.

```python
    def size(self):
        return self._data.size()
```

I decided to keep items in `self._data` always sorted, so the `add` methods has to find the correct index for the given element.

```python
    def add(self, x):
        it = self._data.iterator()
        i = 0
        y = None

        while it.has_current() and x <= it.current():
            y = it.current()
            i += 1
            it.advance()

        if x == y:
            return False

        self._data.add(i, x)

        return True
```

It traverses `self._data` until it finds an element greater than `x` or reaches the end of the list, then adds `x` to this position.

`remove` looks similar.

```python
    def remove(self, x):
        it = self._data.iterator()
        i = 0

        while it.has_current() and x != it.current():
            i += 1
            it.advance()

        if not it.has_current():
            return None

        y = it.current()
        self._data.remove(i)

        return y
```

A fundamental difference between `add` and `remove` is that the later searches for an exact match for `x`.

`find` also traverses the list looking for an element equal to or greater than `x`.

```python
    def find(self, x):
        it = self._data.iterator()
        y = None

        while it.has_current() and x <= it.current():
            y = it.current()
            it.advance()

        return y
```

If there are no elements in `self._data` or `x` is greater than all of them, `find` returns `None` -- thanks to the fact `self._data` is always sorted.

This implementation of `SSet` passes in all tests.
