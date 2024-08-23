#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Go : La face cachée des interfaces

---

`go build -gcflags="-m -m" main.go`

```go
package main

import "fmt"

type test struct {
}

//go:noinline
func (t test) String() string {
    return "test"
}

func main() {
    var t test
    print(t)
}

//go:noinline
func print(val fmt.Stringer) {
    fmt.Println(val.String())
}

//go:noinline
// func print[T fmt.Stringer](s T) {
// 	fmt.Println(s.String())
// }

```

Equivalent à :
```rust
trait Abstract {
    fn test(&self);
}

struct Concrete {}

impl Concrete {
    fn new() -> Self {
        Self {}
    }
}

impl Abstract for Concrete {
    fn test(&self) {}
}

fn example_with_dyn(_: &dyn Abstract) {}

fn example_with_generic<T: Abstract>(_: &T) {}

fn main() {
    let t = Concrete::new();
    example_with_dyn(&t);
    example_with_generic(&t);
}
```
