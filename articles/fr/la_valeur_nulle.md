## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) La valeur null, un réel intérêt ?

---

### Sommaire
* [Introduction](#introduction)
*

---

#### Introduction
Beaucoup de langages ont une valeur nulle, telle que `null` en C#, `None` en Python, `nil` en Ruby ou Lua (sans oublier `NULL` en C et `nullptr` en C++ lorsque l'on utilise des pointeurs).
Cette valeur a toujours la même sémantique, à savoir l'absence de valeur. Cela peut avoir ses avantages, comme lorsque l'on veut proposer des paramètres optionnels pour une fonction :

```py
def say_hello(name=None):
    if name is not None:
        print("Hello, {}!".format(name))
    else:
        print("Hello, world!")
```

Mais une valeur nulle pose également bon nombre de soucis, car elle impose par nature des préconditions supplémentaires :

```py
def send_http_request(method, host, timeout=None):
    assert method is not None, "Invalid HTTP method"
    assert host is not None, "Invalid host"
    # ...
```
