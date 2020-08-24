## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### Rust & Ownership

---

### Introduction

One of the main features of Rust is *ownership*. 
Ownership follow three rules.

From the the official documentation:
* Each value in Rust has a variable that’s called its *owner*.
* There can only be one owner at a time.
* When the owner goes out of scope, the value will be dropped.

Example:
```rust
let s = String::from("hello");
```
* Here, `s` is the *owner* of the value `"hello"` stored in the heap.
* `s` is the only owner of this value.
* At the end of the scope where `s` is declared, it will be dropped from memory.

---

### Ownership & moves

Let's take the following code as an example:
```rust
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec1;
    println!("{:?}", vec1);
    println!("{:?}", vec2);
}
```
As a C++ developer, I expected the output to be something like:
```
[1, 2, 3]
[1, 2, 3]
```
But reality is quite different:
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
What happened? `vec1`'s content has been moved into `vec2`. Thus, `vec2` is now the new *owner* of this resource. Remember the second rule of *ownership*: There can only be one owner at a time.

An illustration of this behavior is C++'s **move semantics**:
```cpp
#include <iostream>
#include <vector>

int main() {
    // this is the equivalent of the above code, with a few prints.
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
As we can see, `vec1`'s inner pointer is directly moved into `vec2`. `vec1`'s data is then set to `nullptr`.

---

### Conclusion
