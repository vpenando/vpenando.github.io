
## Introduction
Définition de la classe :
```cpp
template<class T>
class DynamicArray final {
public:
  DynamicArray(unsigned size, T const& val = T{});
  ~DynamicArray();
  unsigned size() const noexcept;
  T& operator[](unsigned idx);       // Setter
  T  operator[](unsigned idx) const; // Getter
private:
  T *array_;
  unsigned size_;
};
```

Implémentation :
```cpp
//! @brief Constructeur
//! @param size Taille du tableau
//! @param val  (Optionnel) Valeur par défaut des éléments du tableau
//! @pre   size doit être supérieur à 0
template<class T>
DynamicArray<T>::DynamicArray(unsigned size, T const& val)
  : array_(new T[size]),
    size_(size)
{
  assert(size > 0 && "Invalid size");
  std::fill(array_, array_ + size, val);
}

//! @brief Destructeur
template<class T>
DynamicArray<T>::~DynamicArray() {
  delete[] array_;
}

//! @brief   Getter vers size_
//! @returns La taille du tableau
template<class T>
unsigned DynamicArray<T>::size() const noexcept {
  return size_;
}

//! @brief   Setter vers le i-ième élément du tableau
//! @param   idx Index
//! @returns Une référence vers le i-nième élément du tableau
//! @pre     idx ne doit pas être en dehors de la plage [0; size_ - 1]
template<class T>
T& DynamicArray<T>::operator[](unsigned idx) {
  assert(idx < size_ && "Out of bounds");
  return array_[idx];
}

//! @brief   Getter vers le i-ième élément du tableau
//! @param   idx Index
//! @returns Le i-nième élément du tableau
//! @pre     idx ne doit pas être en dehors de la plage [0; size_ - 1]
template<class T>
T DynamicArray<T>::operator[](unsigned idx) const {
  assert(idx < size_ && "Out of bounds");
  return array_[idx];
}
```

## Le problème
Cette classe ne redéfinissant pas de constructeur de copie et d'opérateur d'affectation, la compilation provoquera le message suivant avec un compilateur bien réglé :
```
main.cpp: In instantiation of 'class DynamicArray<int>':
main.cpp:55:26:   required from here
main.cpp:5:7: error: 'class DynamicArray<int>' has pointer data members [-Werror=effc++]
 class DynamicArray final {
       ^~~~~~~~~~~~
main.cpp:5:7: error:   but does not override 'DynamicArray<int>(const DynamicArray<int>&)' [-Werror=effc++]
main.cpp:5:7: error:   or 'operator=(const DynamicArray<int>&)' [-Werror=effc++]
```

Et pour cause :
```cpp
DynamicArray<T> copy(42, 10);
copy = arr; // ???
```
Que se passera-t-il dans ce cas ? Que devient `copy.array_` lors de l'affectation ? Ou plutôt, à quel moment ce pointeur est-il libéré ?

La réponse est simple : la mémoire allouée n'est tout simplement pas libérée.

De plus, un autre problème se pose. Imaginons le code suivant :
```cpp
void foo(DynamicArray<int> const& arr) {
  auto cpy = arr; // cpy.array_ = arr.array_ => copie de pointeur
  // ...
} // Ici, copy.array_ est libéré

int main() {
  DynamicArray<int> arr{10}; // Création d'un tableau de 10 éléments
  foo(arr);
  // A ce stade, arr.array_ est libéré
} // Arrivé ici, arr.array_ est de nouveau libéré
```
L'erreur suivante arrivera au runtime (ou tout du moins, une erreur similaire) :
```
*** Error in `./a.out': double free or corruption (fasttop): 0x0000000000db8c20 ***
======= Backtrace: =========
/lib/x86_64-linux-gnu/libc.so.6(+0x777e5)[0x7f7a475737e5]
/lib/x86_64-linux-gnu/libc.so.6(+0x7fe0a)[0x7f7a4757be0a]
/lib/x86_64-linux-gnu/libc.so.6(cfree+0x4c)[0x7f7a4757f98c]
./a.out[0x400a8d]
./a.out[0x4009bf]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xf0)[0x7f7a4751c830]
./a.out[0x400859]
======= Memory map: ========
0bash: line 7:   724 Aborted                 (core dumped) ./a.out
```

## Solution
