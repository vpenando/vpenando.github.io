## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) C++ Amusons-nous avec les variadic templates

---

### Sommaire

---

```cpp
template<class T, unsigned N, class ...TArgs>
struct matches_any_of {
    constexpr static auto has_next_type = N < sizeof...(TArgs) - 1;
    using conditional_type = typename std::conditional<
        has_next_type,
        matches<T, N+1, TArgs...>,
        std::false_type
    >::type;
    using TArg1 = typename std::tuple_element<N, std::tuple<TArgs...> >::type;
    
public:
    constexpr static auto value = std::is_same<T, TArg1>::value || conditional_type::value;
};

template<class T, class ...Args>
struct is_any_of: matches_any_of<T, 0u, Args...> {};
```
