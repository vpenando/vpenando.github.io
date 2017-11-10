
```cpp
template<class Fn, class T>
auto curry(Fn&& fn, T const& val) {
  return [fn, &val](auto&& ...a) {
    return std::bind(fn, val, a...)();
  };
}

template<class Fn, class T, class ...Args>
auto curry(Fn&& fn, T const& val, Args&& ...args) {
  return curry([fn, &val](auto&& ...a) {
    return std::bind(fn, val, a...)();
  }, args...);
}

void print_args(int a, int b, int c, int d) {
  std::cout << a << std::endl;
  std::cout << b << std::endl;
  std::cout << c << std::endl;
  std::cout << d << std::endl;
}

int main() {
  auto curryed = curry(print_args, 1, 2, 3);
  curryed(4);
  std::cin.get();
}
```
