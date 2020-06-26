## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) C++ - Amusons-nous avec les variadic templates

---

### Sommaire
* [Introduction](#introduction)
* [Variadic templates, kézako ?](#variadic-templates-k%C3%A9zako-)
* [Mise en pratique](#mise-en-pratique)
* [En résumé](#en-résumé)

---

### Introduction
En C++, il existe la notion de template, qui permet *-notamment-* de créer des fonctions génériques :
```cpp
template<class T>
void foo(T const& value) {
    std::cout << value;
}

// et à l'usage :
foo(42);     // int
foo(3.14);   // double
foo("foo");  // const char*
```
Ici, la fonction `foo` accepte à peu près tous les types de paramètres, à condition qu'il existe une surcharche de `std::ostream::operator<<` pour ceux-ci.

---

### Variadic templates, kézako ?
Avec la norme C++11, arrivée en 2011, ont été ajoutés les variadic templates, permettant à une fonction, classe, ou structure de prendre un nombre indéfini de paramètres. Depuis C++14, il est même possible d'utiliser les templates *-ainsi que les variadic templates-* conjointement avec une variable `constexpr`.

Ils viennent remplacer de façon moderne la syntaxe C très lourde à base de `...`, `va_list`, `va_start`, `va_arg` et `va_end`.
Un bon exemple de l'utilisation des variadic templates est la classe [`std::tuple`](https://en.cppreference.com/w/cpp/utility/tuple).

Pour utiliser les variadic templates, la syntaxe est la suivante :
```cpp
template<class ...TArgs>
void bar(TArgs ...args) {
    // ...
}
```
Voici un exemple concret d'utilisation des variadic templates, basé sur la fonction `foo` créée plus haut.
```cpp
template<class T>
void foo(T&& value) {
    // version ne prenant qu'un paramètre
    std::cout << value;
}

template<class T, class ...TArgs>
void foo(T&& value, TArgs&& ...args) {
    // version récursive
    foo(std::forward<T>(value));        // on appelle foo<T>
    foo(std::forward<TArgs>(args)...);  // on rappelle foo sans T
}

// et à l'usage :
foo(3, ".", 14);
```
Concrètement, les appels successifs à `foo` sont les suivants :
```cpp
foo(3, ".", 14);
foo(".", 14);
foo(14);
```
À chacun de ceux-ci, un appel unitaire à `foo` est effectué.



---

### Mise en pratique
Par exemple, comment savoir si un type correspond à un autre parmi une liste de types spécifiés ? Illustration :
```cpp
template<class T>
void test_type() {
    // nous voulons savoir si T est un int ou double, par exemple
    constexpr auto is_int_or_double = ???
}
```
Oui, nous pourrions utiliser `std::is_same`, mais ça n'est pas le but de l'exercice.

Solution :
```cpp
namespace impl {
    
    // 'is_any_of' teste récursivement chacun des types de 'TArgs' jusqu'à-ce que
    // l'un d'eux corresponde à 'T' ou que l'on atteigne la fin de 'TArgs'.
    template<class T, unsigned N, class ...TArgs>
    struct _is_any_of {
    private:
        // si l'on n'a pas encore atteint la fin de 'TArgs', cette variable vaut true
        constexpr static auto has_next_type = N < sizeof...(TArgs) - 1;
        
        using condition_type = typename std::conditional<
            has_next_type,                 // existe-t-il encore un (N+1)-ième type ?
            _is_any_of<T, N+1, TArgs...>,  // si oui, 'condition_type' = '_is_any_of<T, N+1, TArgs...>'
            std::false_type                // sinon, 'condition_type' = 'std::false_type'
        >::type;
        
        // N-ième type à tester
        using nth_type = typename std::tuple_element<N, std::tuple<TArgs...>>::type;

    public:
        constexpr static auto value = std::is_same_v<T, nth_type> || condition_type::value;
    };

} // namespace impl

template<class T, class ...TArgs>
using is_any_of = impl::_is_any_of<T, 0, TArgs...>;

// version C++17, avec le suffixe "_v"
template<class T, class ...TArgs>
constexpr static auto is_any_of_v = is_any_of<T, TArgs...>::value;
```
**Note -** `sizeof...(TArgs)` renvoie simplement le nombre de types contenus dans `TArgs`.

À l'usage, notre `is_any_of_v` peut être employé comme suit :
```cpp
template<class T>
void test_type() {
    // nous voulons savoir si T est un int ou un double, par exemple
    constexpr auto is_int_or_double = is_any_of_v<T, int, double>;
}
```

---

### En résumé
En somme, les variadic templates peuvent accomplir bien des choses, le tout avec une syntaxe très légère !
Par ailleurs, l'exemple ci-dessus présente la plupart des choses que vous devez connaître à leur propos :
* `template<class ...TArgs>` pour créer une fonction/classe template ;
* `sizeof...(TArgs)` pour récupérer le nombre de types sous-jacents ;
* `std::tuple_element<N, std::tuple<TArgs...>>::type` pour récupérer le n-ième élément.

**Note -** Pour les fonctions telles que `std::forward<T>`, la syntaxe est `std::forward<TArgs>(args)...`.
