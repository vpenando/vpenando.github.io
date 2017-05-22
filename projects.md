## [Welcome here!](index.md) | [Articles](articles.md) | Main projects | [About me](about.md)
Here are my main projects.

---


### [SDL Wrapper](https://github.com/tyr-sl3/sdl-cpp) [![Build Status](https://travis-ci.org/tyr-sl3/sdl-cpp.svg)](https://travis-ci.org/tyr-sl3/sdl-cpp)
A free, open source and RAII-conform SDL wrapper written in C++14. Its goal is to make easier and safier to develop SDL programs with C++.

Short example:
```cpp
sdl::init(sdl::INIT_VIDEO);
sdl::Window window{"My Window", sdl::Size{500, 500}};
bool loop = true;
sdl::EventHandler handler; // Global event handler (for both keyboard and mouse)
handler.on_quit([&loop]() { loop = false; });
auto& mouse_handler = handler.get<sdl::MouseStateHandler>();
while (loop) {
  // ...
  window.update();
  handler.update();
}
```
Useful links:
- [Ideas](https://github.com/tyr-sl3/sdl-cpp/wiki/Ideas)
- [Todo list](https://github.com/tyr-sl3/sdl-cpp/wiki/Todo-list)

---

### [MatMath](https://github.com/tyr-sl3/MatMath) [![Build Status](https://travis-ci.org/tyr-sl3/MatMath.svg)](https://travis-ci.org/tyr-sl3/MatMath)
A free, open source, header-only C++ API for mathematic operations on matrices. Provides two types of matrix:
- Allocated on the stack
- Allocated on the heap

Short example:
```cpp
const mat::Matrix<int, 3, 3> mat1 = {
  1, 2, 3,
  4, 5, 6,
  7, 8, 9
};
mat::Dynamic<int> mat2(3, 3);
mat2 = {
  3, 2, 1,
  6, 5, 4,
  9, 8, 7
};
const auto product = mat1 * mat2;
const auto identity = Matrix<int, 4, 4>::identity();
```

---
