## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Structure d'une ROM de jeu NES


```py

def readbin(path: str):
    """Lecture d'un fichier en mode binaire.
    
    :param path: Chemin vers le fichier
    :type path: str
    :return:     Une liste avec le contenu du fichier
    """
    with open(path, "rb") as f:
        content = f.read()
        return list(content)
	
NES_HEADER = [78, 69, 83, 26] # "NES" + 0x1A

def readrom(path: str):
    """Lecture d'une ROM de jeu NES
    
    :param path: Chemin vers le fichier
    :type path: str
    :return:     Une liste avec le contenu du fichier
    :warning:    Si le fichier n'est pas une ROM NES, lève une IOError
    """
    rom = readbin(path)
    isnes = rom[0:4] == NES_HEADER
    if not isnes:
    	raise IOError("Not a NES ROM")
    return rom
    
file = "xxxx.nes"
rom = readrom(file)

print(rom[:15])
```

Exemple d'output : `[78, 69, 83, 26, 2, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0]`
