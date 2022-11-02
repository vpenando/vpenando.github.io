#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Mon implémentation de `Result` en C#

---

### Syntaxe

```c#
record Error(/* ... */);

Result<int, Error> SomethingThatMayFail() {
    if (/* ...*/) {
        return Err(new Error(/* ... */));
    }
    return Ok(42);
}
```
