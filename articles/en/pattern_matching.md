## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### [EN] - Let's play with pattern matching!

---

### 1. What is pattern matching?
In many languages (like C++, C, C#...), we have the `switch` structure that allows us to test a value:
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
Another syntax exists for functions using pattern matching:
```ml
let testMyVariable = function
    | 0 -> (* ... *)
    | 1 -> (* ... *)
    | _ -> (* Default case *)
    ;;
```
Here, the parameter `variable` is implicit. Thanks to the `function` keyword, the compiler knows that `testMyVariable` expects exactly one argument.

It's easy to compute the sum of a list, the depth of a tree, or any complex computation by coupling pattern matching and recursion.

---

### 2. Examples
##### a. The sum of a list
The sum of a list can be computed very easily with pattern matching:
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
In the same way, we can also compute how many elements are contained in a list:
```ml
let rec count = function
    | []    -> 0
    | x::xs -> 1 + (count xs)
    ;;
```
In this function, we have to cases to test:
1. The list is empty: its count is `0`.
2. The list is NOT empty: we return `1` + the count of the rest of the list.

---

### 3. Other uses of pattern matching
We can combine pattern matching and custom types in order to have different computations depending on the type.
Let's take the following code:
```ml
type 'a maybe =
    | Nothing
    | Just of 'a
    ;;
```
As the standard type `option`, `maybe` is a generic type that can take two different values: `Nothing`, that means "no value", and `Just x` taking a value `x` of type `'a`. It can be deconstructed thanks to pattern matching:
```ml
(* Expects a "string maybe" *)
let printIfExists = function
    | Nothing -> ()              (* Nothing to do *)
    | Just x  -> print_string x  (* Let's print x *)
    ;;
```
Another use of pattern matching could be to resolve successive computations.

Simple example:
```ml
(* Accepted operators: +, -, * and / *)
type binaryOperation =
    | Add
    | Sub
    | Mul
    | Div
    ;;
  
(* A value can be a literal one (ex: 42) or computed (ex: 42 * 2) *)
type 'a value =
    | Literal of 'a
    | Computed of binaryOperation * 'a value * 'a value
    ;;
  
let evalOperation op val1 val2 =
    match op with
    | Add -> val1 + val2
    | Sub -> val1 - val2
    | Mul -> val1 * val2
    | Div -> val1 / val2
    ;;
  
let rec computeValue = function
    | Literal literalValue -> literalValue
    | Computed(op, v1, v2) -> evalOperation op (computeValue v1) (computeValue v2)
    ;;
```
We can now resolve computations like:
```ml
let result = computeValue (Computed(Sub, (Computed(Add, Literal 1, Literal 2)), Literal 2));;
```
This is the first step of writing a minimalist interpreter! We can then extend it by adding other binary operations (`Mod`, `Or`, `And`, `Xor`, ...) and unary operations (`Not` *-for booleans-*, `Plus`, `Minus`, ...) and then support them in `evalOperation`. It's also possible to have a function for each type:
```ml
let evalIntOperation op val1 val2 =
    match op with
    | Add -> Some (val1 + val2)
    | Sub -> Some (val1 - val2)
    | Mul -> Some (val1 * val2)
    | Div -> if val2 <> 0 then Some (val1 / val2) else None
    | Mod -> Some (val1 mod val2)
    | _   -> None (* Unsupported *)
    ;;
  
let evalFloatOperation op val1 val2 =
    match op with
    | Add -> Some (val1 +. val2)
    | Sub -> Some (val1 -. val2)
    | Mul -> Some (val1 *. val2)
    | Div -> if val2 <> 0. then Some (val1 /. val2) else None
    | _   -> None (* Unsupported *)
    ;;
  
let evalBoolOperation op val1 val2 =
    match op with
    | Or  -> Some (val1 || val2)
    | And -> Some (val1 && val2)
    | Xor -> Some (val1 <> val2)
    | _   -> None (* Unsupported *)
    ;;
```
Note that we now return a `'a option` instead of `'a`. This allows us to cover the case of unsupported operations. If `evalXXXOperation` returns `None`, we know that we tried to compute an invalid operation. We could also create a type that represents the result:
```ml
type 'a result =
    | Success of 'a
    | UnsupportedOperationError of string
    | InvalidOperationError of string
    ;;
```
This type is easy to extend in order to cover each error that can happen.

Example:
```ml
let evalIntOperation op val1 val2 =
    match op with
    (* ... *)
    | Div -> if val2 <> 0 then Success (val1 / val2) else InvalidOperationError "Dividing by zero"
    | _   -> UnsupportedOperationError "This operation is not supported for type 'int'"
    ;;

