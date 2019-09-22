## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (EN) Explicit typing in Python

---


```py
from typing import Callable, List, Set, Dict, Tuple, Optional


Int = int
Bool = bool
Float = float
String = str


def check_value(value: int) -> Optional[int]:
    return value



print(check_value(42))
```
