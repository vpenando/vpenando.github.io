## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (EN) Explicit typing in Python

---

Introduction
---
As you certainly know, Python is a dynamically, implicitly typed language. That means that this code is valid:
```py
def print_value(value):
    print(value)
    
foo = 42     # here, 'foo' is an int
print_value(foo)
foo = "Foo"  # now, 'foo' is a string
print_value(foo)
```
You don't have to write `foo`'s type: Python will determine it by itself. But, since Python 3.5, there is a way to explicitly specify types that are expected by functions and methods (like the `print_value` function seen above).

---

Explicit typing in Python?
---
Since Python 3.5 ([PEP 484](https://www.python.org/dev/peps/pep-0484/)), explicit typing (known as 'types hints') is supported:
```py
def add(x: int, y: int):
    return x + y
```
It's even possible to specify the return type of a function!
```py
def add(x: int, y: int) -> int:
    return x + y
```
But what happens if we call `add` with, for example, strings?
In C++, a similar example won't compile:
```cpp
int add(int x, int y) {
    return x + y;
}

using namespace std::string_literals;  // for "s" suffix
add("foo"s, "bar"s);

// Output:
//   fatal error: no matching function for call to 'add'
//   note: candidate function not viable: no known conversion from 'basic_string<char>' to 'int' for 1st argument
```

Back to Python, let's try and see the output:
```py
result = add('foo', 'bar')
print(result)
```
Output:
```
foobar
```
It seems that Python doesn't look at `x`'s expected type, as well as `y`'s one. Following the example above, what happened?

---

Mypy
---

Install it with `pip install mypy`.

```py
from typing import Callable, List, Set, Dict, Tuple, Optional

def check_value(value: int) -> Optional[int]:
    return value

print(check_value(42))
```
