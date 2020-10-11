## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Parlons SOLID 1/5 : SRP

---

### Introduction

---

### SRP : Principe de responsabilité unique

---

### Exemple de non respect du SRP
* `XXXManager`

```py
class FileManager:
    def __init__(self):
        ...
        
    def create_file(self, path: str):
        ...
        
    def delete_file(self, path: str):
        ...
        
    def rename_file(self, path: str):
        ...
```

---

### Conséquences

---

### Solutions
* Fonctions libres (+ namespaces)
* Scinder en plusieurs classes

---

### Conclusion
