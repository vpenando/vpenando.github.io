## [Welcome here!](index.md) | [Articles](articles.md) | [Main projects](projects.md) | [About me](about.md)

[FR] Projet - Présentation d'un wrapper C++14 pour la SDL2
---
Version | Statut
-----------|-------------
`0.1.0` | [![Build Status](https://travis-ci.org/vpenando/sdl-cpp.svg)](https://travis-ci.org/vpenando/sdl-cpp)



### 1. Création d'une fenêtre
En SDL classique, la création d'une fenêtre suit quatre étapes :
- Initialisation
- Test de non-nullité
- (En admettant que le test précédent passe) Utilisation
- Libération

```cpp
// Initialisation
SDL_Window *window = SDL_CreateWindow(
  "Ma fenêtre",           // Nom
  SDL_WINDOWPOS_CENTERED, // Position en X
  SDL_WINDOWPOS_CENTERED, // Position en Y
  500,                    // Largeur
  500,                    // Hauteur
  0                       // Flags
);
  
// Test de non-nullité
if (!window) {
  // Gestion de l’erreur
}

// Utilisation
// ...

// Libération
SDL_DestroyWindow(window);
```

Avec le wrapper, le code est considérablement simplifié. Le code ci-dessous est (presque) équivalent au précédent :
```cpp
sdl::Window window{
  "Ma fenêtre",
  sdl::Size{500, 500}
};
```
* **Note** - *Si le test de non-nullité n'est pas satisfait, alors une exception sera levée.*

### 2. Chargement d'une image

```cpp
sdl::Window window{
  "Ma fenêtre",
  sdl::Size{500, 500}
};
sdl::Surface image = sdl::load_bmp("image.bmp");

// Dans la boucle principale :
window.blit(
  image,             // Image à afficher
  sdl::Point{50, 50} // Coordonnées
);
```

### 3. Gestion des événements
La gestion des événements est centralisée par la classe `sdl::EventHandler handler;`, qui simplifie grandement le travail :
```cpp
sdl::EventHandler handler;

// Action à faire à la fermeture de l'application
handler.on_quit([] -> void { /* ... */ });

// Gestion des touches du clavier
handler.on_press(
  sdl::KeyCode::A,         // Touche concernée
  [] -> void { /* ... */ } // Action à effectuer
);

// Gestion des clics de la souris
handler.on_click(
  sdl::ClickCode::Left,    // Touche concernée
  [] -> void { /* ... */ } // Action à effectuer
);
```
Exemple concret :
```cpp
sdl::EventHandler handler;
bool loop = true; // Indique si la main loop tourne
handler.on_quit([&loop)() -> void { loop = false; });
handler.on_press(sdl::KeyCode::Esc, [&loop] -> void { loop = false; });
while (loop) {
  // ...
  // Update
  handler.update(); // Met à jour les handlers pour le clavier et la souris
  window.update();
}
```

Il est bien entendu possible de récupérer soi-même les inputs :
```cpp
sdl::EventHandler handler;
auto& keyboard_handler = handler.get<sdl::KeyboardStateHandler>();
auto& mouse_handler    = handler.get<sdl::MouseStateHandler>();
bool loop = true; // Indique si la main loop tourne
while (loop) {
  const auto keyboard_state = keyboard_handler.state();
  const auto key_a_pressed  = keyboard_state[sdl::KeyCode::A].pressed;
  if (key_a_pressed) {
    std::cout << "Key A pressed!" << std::endl;
  }
  const auto mouse_state  = mouse_handler.state();
  const auto mouse_coords = mouse_state.coords();
  std::cout << "Mouse: (" << mouse_coords.x << ", " << mouse_coords.y << ")" << std::endl;
  // Update
  handler.update(); // Met à jour les handlers pour le clavier et la souris
  window.update();
}
```

Cela est certes plus compliqué, mais permet la récupération d'inputs multiples :
```cpp
sdl::EventHandler handler;
auto& keyboard_handler = handler.get<sdl::KeyboardStateHandler>();
const auto state = keyboard_handler.state();
const auto abc = state[sdl::Key::A].pressed
              && state[sdl::Key::B].pressed
              && state[sdl::Key::C].pressed;
```
