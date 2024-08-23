#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Go : La face cachée des interfaces

---

`go build -gcflags="-m -m" main.go`

```go
package main

import (
    "fmt"
)

type abstract interface {
    test()
}

type concrete struct {
}

func (c concrete) test() {
    fmt.Println("test")
}

func main() {
    var c concrete
    print(c)
}

func print(input abstract) {
    input.test()
}

// func print[T abstract](input T) {
//    input.test()
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
    fn test(&self) {
        println!("test")
    }
}

fn example_with_dyn(input: &dyn Abstract) {
    input.test();
}

fn example_with_generic<T: Abstract>(input: &T) {
    input.test();
}

fn main() {
    let t = Concrete::new();
    example_with_dyn(&t);
    example_with_generic(&t);
}

```
