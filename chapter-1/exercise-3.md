Each opening character (`(`, `{`, `[`) represents an `append` operation, while a enclosing character (`)`, `}`, `]`) represents a `pop` operation.

If the character being popped isn't the opening for the current one, then the string isn't a match.

```python
pair = { '(': ')', '{': '}', '[': ']' }

stack = []

matched = True

for c in '{{()[]}}':
    if c in pair:
        stack.append(c)
    elif pair[stack.pop()] != c:
        matched = False
        break

print('Matched' if matched else 'Not matched')
```
