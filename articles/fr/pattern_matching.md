
```ml
type operand =
  | Add
  | Sub
  | Mul
  | Div
  | Mod
  | And
  | Or
  ;;

type 'a expression =
  | Literal of 'a
  | Variable of string
  | Operation of 'a expression * operand * 'a expression
  ;;

type 'a statement =
  | Assignment of string * 'a expression
  | Sequence of 'a statement * 'a statement
  ;;

type 'a maybe =
  | Just of 'a
  | Nothing
  ;;

let evalIntExpression expr =
  let rec evalExpr = function
    | Literal literalValue -> literalValue
    | Operation(e1,Add,e2) -> (evalExpr e1) + (evalExpr e2)
    | Operation(e1,Sub,e2) -> (evalExpr e1) - (evalExpr e2)
    | Operation(e1,Mul,e2) -> (evalExpr e1) * (evalExpr e2)
    | Operation(e1,Div,e2) -> (evalExpr e1) / (evalExpr e2)
    | Operation(e1,Mod,e2) -> (evalExpr e1) % (evalExpr e2)
    | _ -> failwith "Operator not supported for type 'int'"
  in
  match expr with
  | Literal _
  | Operation(_,Add,_)
  | Operation(_,Sub,_)
  | Operation(_,Mul,_)
  | Operation(_,Div,_)
  | Operation(_,Mod,_) -> Just (evalExpr expr)
  | _                  -> Nothing
  ;;

let rec evalFloatExpression expr =
  let rec evalExpr = function
    | Literal literalValue -> literalValue
    | Operation(e1,Add,e2) -> (evalExpr e1) + (evalExpr e2)
    | Operation(e1,Sub,e2) -> (evalExpr e1) - (evalExpr e2)
    | Operation(e1,Mul,e2) -> (evalExpr e1) * (evalExpr e2)
    | Operation(e1,Div,e2) -> (evalExpr e1) / (evalExpr e2)
    | _  -> failwith "Operator not supported for type 'float'"
  in
  match expr with
  | Literal _
  | Operation(_,Add,_)
  | Operation(_,Sub,_)
  | Operation(_,Mul,_)
  | Operation(_,Div,_) -> Just (evalExpr expr)
  | _                  -> Nothing
  ;;

let evalBoolExpression expr =
  let rec evalExpr = function
    | Literal literalValue -> literalValue
    | Operation(e1,And,e2) -> (evalExpr e1) && (evalExpr e2)
    | Operation(e1,Or,e2)  -> (evalExpr e1) || (evalExpr e2)
    | _  -> failwith "Operator not supported for type 'bool'"
  in
  match expr with
  | Literal _
  | Operation(_,And,_)
  | Operation(_,Or,_) -> Just(evalExpr expr)
  | _                 -> Nothing
  ;;

let assignment =
  Assignment("result", Operation(Literal 42, Add, Literal 1));;
  
let bres = evalBoolExpression (Operation(Literal true, Or, Literal false));;
match bres with
| Just res -> Printf.printfn "%A" res
| Nothing  -> Printf.printfn "Nothing";;

let ires = evalIntExpression (Operation(Literal 42, Add, Literal 1));;
match ires with
| Just res -> Printf.printfn "%d" res
| Nothing  -> Printf.printfn "Nothing";;

let fres = evalFloatExpression (Operation(Literal 42.0, Add, Literal 1.0));;
match fres with
| Just res -> Printf.printfn "%f" res
| Nothing  -> Printf.printfn "Nothing";;

open System.Linq
open System.Text.RegularExpressions
let intRgx   = new Regex("[0-9]+",        RegexOptions.Compiled)
let floatRgx = new Regex("[0-9]+.[0-9]*", RegexOptions.Compiled)
let boolRgx  = new Regex("(true|false)",  RegexOptions.Compiled)

let isEmpty = System.String.IsNullOrWhiteSpace
let splitString str tok = Regex.Split(str, "(\\s|=)").Where(fun s -> not (isEmpty s)).ToList()
let splitted = splitString "let   a=42" ""

System.Console.WriteLine(splitted)
```
