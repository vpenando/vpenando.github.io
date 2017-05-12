
Définition de la classe :
```cpp
template<class T>
class DynamicArray final {
public:
  DynamicArray(unsigned size, T const& val = T{});
  ~DynamicArray();
  unsigned size() const noexcept;
  T& operator[](unsigned i);       // Setter
  T  operator[](unsigned i) const; // Getter
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

//! @brief   Setter
//! @param   i Index
//! @returns Une référence vers le i-nième élément du tableau
//! @pre     i ne doit pas être en dehors de [0; size_ - 1]
template<class T>
T& DynamicArray<T>::operator[](unsigned i) {
  assert(i < size_ && "Out of bounds");
  return array_[i];
}

//! @brief   Getter
//! @param   i Index
//! @returns Le i-nième élément du tableau
//! @pre     i ne doit pas être en dehors de [0; size_ - 1]
template<class T>
T DynamicArray<T>::operator[](unsigned i) const {
  assert(i < size_ && "Out of bounds");
  return array_[i];
}
```

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
