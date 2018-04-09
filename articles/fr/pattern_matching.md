
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

let rec evalExpr expr realEval = realEval expr;;
  
exception InvalidOperatorException of string;;

let evalIntExpression expr =
  let rec eval = function
    | Literal literalValue -> literalValue
    | Operation(e1,Add,e2) -> (eval e1) + (eval e2)
    | Operation(e1,Sub,e2) -> (eval e1) - (eval e2)
    | Operation(e1,Mul,e2) -> (eval e1) * (eval e2)
    | Operation(e1,Div,e2) -> (eval e1) / (eval e2)
    | Operation(e1,Mod,e2) -> (eval e1) % (eval e2)
    | _ -> failwith "Operator not supported for type 'int'"
  in
  match expr with
    | Literal _
    | Operation(_,Add,_)
    | Operation(_,Sub,_)
    | Operation(_,Mul,_)
    | Operation(_,Div,_)
    | Operation(_,Mod,_) -> Just (eval expr)
    | _                  -> Nothing
  ;;

let rec evalFloatExpression expr =
  let rec eval = function
    | Literal literalValue -> literalValue
    | Operation(e1,Add,e2) -> (eval e1) + (eval e2)
    | Operation(e1,Sub,e2) -> (eval e1) - (eval e2)
    | Operation(e1,Mul,e2) -> (eval e1) * (eval e2)
    | Operation(e1,Div,e2) -> (eval e1) / (eval e2)
    | _  -> failwith "Operator not supported for type 'float'"
  in
  match expr with
  | Literal _
  | Operation(_,Add,_)
  | Operation(_,Sub,_)
  | Operation(_,Mul,_)
  | Operation(_,Div,_) -> Just (eval expr)
  | _                  -> Nothing
  ;;

let evalBoolExpression expr =
  let rec eval = function
    | Literal literalValue -> literalValue
    | Operation(e1,And,e2) -> (eval e1) && (eval e2)
    | Operation(e1,Or,e2)  -> (eval e1) || (eval e2)
    | _  -> failwith "Operator not supported for type 'bool'"
  in
  match expr with
  | Literal _
  | Operation(_,And,_)
  | Operation(_,Or,_) -> Just(eval expr)
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
```
