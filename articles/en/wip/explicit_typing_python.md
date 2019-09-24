## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (EN) Explicit typing in Python

---

Introduction
---
As you certainly know, Python is a dynamically, implicitly typed language. Thats means that this code is valid:
```py
foo = 42     # here, 'foo' is an int
foo = "Foo"  # now, 'foo' is a string
```
You don't have to specify the type to Python: it will determine it by itself. But, since Python 3.5, there is a way to explicitly specify types.

---

Explicit typing in Python?
---
Since Python 3.5 ([PEP 484](https://www.python.org/dev/peps/pep-0484/)), explicit typing is supported:
```py
def add(x: int, y: int) :
    return x + y
```
It's even possible to specify the return type of a function!
```py
def add(x: int, y: int) -> int:
    return x + y
```
But what happens if we call `add` with, for example, strings? Let's try and see its output:
```py
result = add('foo', 'bar')
print(result)
```
Output:
```
foobar
```

---

Mypy
---

```py
from typing import Callable, List, Set, Dict, Tuple, Optional

def check_value(value: int) -> Optional[int]:
    return value

print(check_value(42))
```
