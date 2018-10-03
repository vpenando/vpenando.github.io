## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### [EN] - Let's play with pattern matching!

#### 1. What is pattern matching?
In languages like C++, C, C#, we have the `switch` structure that allows us to test a variable:
```cs
void testMyVariable(int variable) {
  switch (variable) {
  case 0:
    // ...
    break;
  case 1:
    // ...
    break;
  // ...
  }
}
```

Pattern matching is a simple and powerful way to test a sequence of tokens. It's similar to the `switch` structure:
```ml
let testMyVariable variable =
  match variable with
  | 0 -> (* ... *)
  | 1 -> (* ... *)
  | _ -> (* Default case *)
  ;;
```
It becomes easy to compute the sum of a list, the depth of a tree, or any complex computation by coupling pattern matching and recursion.

Another syntax exists for functions using pattern matching:
```ml
let testMyVariable = function
  | 0 -> (* ... *)
  | 1 -> (* ... *)
  | _ -> (* Default case *)
  ;;
```
Here, the parameter `variable` is implicit. Thanks to the `function` keyword, the compiler knows that `testMyVariable` expects exactly one argument.

---

#### 2. Examples
##### a. The sum of a list
With OCaml / F#:
```ml
let rec sum = function
  | []    -> 0 (* The sum of an empty list is 0 *)
  | x::xs -> x + (sum xs)
  ;;
```
In this function, we have to cases to test:
1. The list is empty: its sum is `0`.
2. The list is NOT empty: we return the first element (`x`) + the sum of the rest of the list (`xs`).

##### b. The number of elements in a list
With OCaml / F#:
```ml
let rec count = function
  | []    -> 0
  | x::xs -> 1 + (count xs)
  ;;
```
In this function, we have to cases to test:
1. The list is empty: its count is `0`.
2. The list is NOT empty: we return `1` + the count of the rest of the list.


#### c. Other uses of pattern matching?
We can combine pattern matching and custom types in order to have different computations depending on the type.
Let's take the following code:
```ml
type 'a maybe =
  | Nothing
  | Just of 'a
  ;;
```
`maybe` is a generic type that can take two different values: `Nothing` that means "no value" and `Just x` taking a value `x`. It can be deconstructed thanks to pattern matching:
```ml
let printIfExists = function
  | Nothing -> ()              (* Nothing to do *)
  | Just x  -> print_string x  (* Let's print x *)
  ;;
```


Pow:
```ml
let rec pow n p =
  if p < 0 then failwith "Expected a positive number"
  match p with
  | 0 -> 1
  | _ -> n * (pow n (p-1))
  ;;
```
Factorial:
```ml
let rec fac n =
  if n < 0 then failwith "Expected a positive number"
  match n with
  | 0 -> 1
  | _ -> n * (fac (n-1))
  ;;
```
