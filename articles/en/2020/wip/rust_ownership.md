## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### Rust & Ownership

---

### Introduction


---

### Rust 

```rust
fn main() {
    let vec1 = vec![1, 2, 3];
    let _vec2 = vec1;
    println!("{:?}", vec1);
}
```

Output:

```
error[E0382]: borrow of moved value: `vec1`
 --> tempCodeRunnerFile.rust:5:22
  |
3 |     let vec1 = vec![1,2,3];
  |         ---- move occurs because `vec1` has type `std::vec::Vec<i32>`, which does not implement the `Copy` trait
4 |     let _vec2 = vec1;
  |                 ---- value moved here
5 |     println!("{:?}", vec1);
  |                      ^^^^ value borrowed here after move

error: aborting due to previous error

For more information about this error, try `rustc --explain E0382`.
```
An illustration of this behavior is C++'s **move semantics**:
```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vec1{1, 2, 3};
    std::cout << "vec1.data(): " << vec1.data() << "\n";
    std::vector<int> vec2 = std::move(vec1);
    std::cout << "vec2.data(): " << vec2.data() << "\n";
    std::cout << "vec1.data(): " << vec1.data() << "\n";
}
```
Output:
```
vec1.data(): 0x8c6c20
vec2.data(): 0x8c6c20
vec1.data(): 0
```
As we can see, `vec1`'s inner pointer is directly moved into `vec2`.
