## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Quelques tricks avec `std::vector`

---

### Sommaire
* [Introduction](#introduction)
* Supprimer des éléments selon un prédicat
* Supprimer l'élément situé à l'index `n`

---

### Introduction

---

### Supprimer des éléments selon un prédicat
```cpp
std::vector<int> vec = { 1, 2, 3, 4, 5 };
const auto pred = [](auto i) -> bool { return i < 5; };
const auto begin = std::remove_if(vec.begin(), vec.end(), pred);
vec.erase(begin, vec.end());
```

---

### Supprimer l'élément situé à l'index `n`
```cpp
std::vector<int> vec = { 1, 2, 3, 4, 5 };
const auto idx = 2;
assert(idx < vec.size() && "Out of bounds");
std::swap(vec.back(), vec[idx]);
vec.pop_back();
```

---
