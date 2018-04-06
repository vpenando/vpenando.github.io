
```ml
type Operand =
  | Add
  | Sub
  | Mul
  | Div
  | Mod
  | And
  | Or
  ;;

type Expression<'a> =
  | Literal of 'a
  | Operation of Expression<'a> * Operand * Expression<'a>
  ;;

type Statement<'a> =
  | Assignment of string * Expression<'a>
  | Sequence of Statement<'a> * Statement<'a>
  ;;

type Maybe<'a> =
  | Just of 'a
  | Nothing
  ;;

let rec evalExpr expr realEval = realEval expr;;
  
exception OperatorNotSupported of string;;

let rec evalIntOperation (expr: Expression<int>) =
  match expr with
  | Literal literalValue -> literalValue
  | Operation(e1,Add,e2) -> (evalIntOperation e1) + (evalIntOperation e2)
  | Operation(e1,Sub,e2) -> (evalIntOperation e1) - (evalIntOperation e2)
  | Operation(e1,Mul,e2) -> (evalIntOperation e1) * (evalIntOperation e2)
  | Operation(e1,Div,e2) -> (evalIntOperation e1) / (evalIntOperation e2)
  | Operation(e1,Mod,e2) -> (evalIntOperation e1) % (evalIntOperation e2)
  | Operation(_,_,_)     -> raise (OperatorNotSupported "Operator not supported for type 'int'")
  ;;

let rec evalFloatOperation (expr: Expression<float>) =
  match expr with
  | Literal literalValue -> literalValue
  | Operation(e1,Add,e2) -> (evalFloatOperation e1) + (evalFloatOperation e2)
  | Operation(e1,Sub,e2) -> (evalFloatOperation e1) - (evalFloatOperation e2)
  | Operation(e1,Mul,e2) -> (evalFloatOperation e1) * (evalFloatOperation e2)
  | Operation(e1,Div,e2) -> (evalFloatOperation e1) / (evalFloatOperation e2)
  | Operation(_,_,_)     -> raise (OperatorNotSupported "Operator not supported for type 'float'")
  ;;

let rec evalBoolOperation (expr: Expression<bool>) =
  match expr with
  | Literal literalValue -> literalValue
  | Operation(e1,And,e2) -> (evalBoolOperation e1) && (evalBoolOperation e2)
  | Operation(e1,Or,e2)  -> (evalBoolOperation e1) || (evalBoolOperation e2)
  | Operation(_,_,_)     -> raise (OperatorNotSupported "Operator not supported for type 'bool'")
  ;;

let assignment =
  Assignment("result", Operation(Literal 42, Add, Literal 1));;

printfn "%A" (evalBoolOperation (Operation(Literal true, Or, Literal false)));;
printfn "%d" (evalIntOperation (Operation(Literal 42, Add, Literal 1)));;
printfn "%f" (evalFloatOperation (Operation(Literal 42.0, Add, Literal 1.0)));;
ignore (System.Console.Read())
```
