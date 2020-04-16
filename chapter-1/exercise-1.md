The exercise 1 is actually a collection of related micro-exercises.

---

**Note**

All snippets assume the `sys` module is imported.

---

The first sub-exercise can be implemented with a stack (First-in, First-out) structure. Each line is printed as it's popped off of the stack.

A Python `list` can efficiently represent a stack due to their `pop` operation being constant time.

```python
stack = []

for line in sys.stdin:
    stack.append(line.strip())

while stack:
    print('>>>', stack.pop())
```

The second one is pretty similar.

```python
stack = []

for line in sys.stdin:
    stack.append(line.strip())

    if len(stack) == 50:
        while stack:
            print('>>>', stack.pop())

while stack:
    print('>>>', stack.pop())
```

The third exercise can be implemented with a queue (Last-in, First-out) structure. If its length is kept as 42 then the `remove` operation will return the correct element to be printed.

The `list` structure can be used to represent a queue, but it's not efficient. For this you should use `collections.deque` instead.

```python
from collections import deque

queue = deque()

for line in sys.stdin:
    x = line.strip()

    queue.append(x)

    if x == '':
        print('>>>', queue.popleft())
    elif len(queue) == 42:
        queue.popleft()
```

The fourth exercise is a nice use case for sets. Just use it to cache previous lines, then print the current one as long as it's not in the set.

Python provides a built-in class for this.

```python
previous_lines = set()

for line in sys.stdin:
    x = line.strip()

    if x not in previous_lines:
        print('>>>', x)

    previous_lines.add(x)
```

Same thing but inverted. Print the current line as long as it's in the set.

```python
previous_lines = set()

for line in sys.stdin:
    x = line.strip()

    if x in previous_lines:
        print('>>>', x)

    previous_lines.add(x)
```

Sets automatically removes duplicates, so the sixth sub-exercise is a perfect fit for them too.

Python provides a global function called `sorted` that sorts any iterable object and returns a `list`. Here the also global function `len` is used as the key of the sorting algorithm, so the lines are sorted based on their length.

```python
previous_lines = set()

for line in sys.stdin:
    previous_lines.add(line.strip())

for line in sorted(previous_lines, key=len):
    print('>>>', line)
```

Same as the previous one, but using a regular list because duplicates must be printed.

```python
previous_lines = []

for line in sys.stdin:
    previous_lines.append(line.strip())

for line in sorted(previous_lines, key=len):
    print('>>>', line)
```

The eigth sub-exercise is interesting because there are so many ways to implement it efficiently (in fact all exercises have multiple correct answers!). I decided to "abuse" the fact `i % 2` returns `0` or `1` depending on `i` being even or odd, respectively. Not the most aesthetic solution, but an amusing one imho.

```python
from collections import deque

previous_lines = [deque(), deque()]

for i, line in enumerate(sys.stdin):
    previous_lines[i % 2].append(line.strip())

while previous_lines[0]:
    print('>>>', previous_lines[0].popleft())

while previous_lines[1]:
    print('>>>', previous_lines[1].popleft())

```

The last sub-exercise becomes a no brainer when the `random.shuffle` function is used. Just use a list to store all lines, then shuffle it and print each line.

```python
from random import shuffle

previous_lines = []

for line in sys.stdin:
    previous_lines.append(line.strip())

shuffle(previous_lines)

for line in previous_lines:
    print('>>>', line)
```
