#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Bonnes pratiques en C#

---

### Tout commence par une bonne configuration

```xml
<PropertyGroup>
    <Nullable>enable</Nullable>
    <TreatWarningsAsErrors>True</TreatWarningsAsErrors>
    <WarningLevel>5</WarningLevel>
</PropertyGroup>
```

- `Nullable` : Warning lors de l'usage potentiel d'une valeur nulle
- `TreatWarningsAsErrors` : Transforme tous les avertissements en erreurs
- `WarningLevel` : Niveau d'avertissement (5 = maximum)

---

### Moins de code = moins de bruit

- Implicit usings => moins de code
- `global using`
- inline namespace
- records

```xml
<PropertyGroup>
    <ImplicitUsings>enable</ImplicitUsings>
</PropertyGroup>
```
