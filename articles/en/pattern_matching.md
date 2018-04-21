## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### [EN] - Let's play with pattern matching!

#### 1. What is pattern matching?
In languages like C++, C, C#, we have the `switch` structure that allows us to test a variable.

Pattern matching is a simple and powerful way to test a sequence of tokens. It becomes easy to compute the sum of a list, the depth of a tree, or any complex computation by coupling pattern matching and recursion.

#### 2. Examples
##### a. The sum of a list
```ml
let rec sum = function
  | []    -> 0 (* The sum of an empty list is 0 *)
  | x::xs -> x + (sum xs)
  ;;
```

##### b. The number of elements in a list
```ml
let rec count = function
  | []    -> 0
  | x::xs -> 1 + (count xs)
  ;;
```
