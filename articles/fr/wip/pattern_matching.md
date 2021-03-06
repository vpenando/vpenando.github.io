
```ml
module Core

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
let splitString str = Array.toList (Regex.Split(str, "(\\s|=)").Where(fun s -> not (isEmpty s)).ToArray())
let splitted = splitString "let   a=42"

type VariableType =
  | Int
  | Float
  | Bool
  ;;

let emptyVariables: (VariableType * string) list = List.Empty;;
let emptyIntVariables:   (string * int)   list = List.Empty;;
let emptyFloatVariables: (string * float) list = List.Empty;;
let emptyBoolVariables:  (string * bool)  list = List.Empty;;

let variables      = ref emptyVariables;;
let intVariables   = ref emptyIntVariables;;
let floatVariables = ref emptyFloatVariables;;
let boolVariables  = ref emptyBoolVariables;;

let parseInt (str: string) = System.Convert.ToInt32 str
let parseFloat (str: string) = System.Convert.ToDouble str
let parseBool (str: string) = System.Convert.ToBoolean str

let typeOf str =
  if intRgx.IsMatch str then
    Just Int
  elif floatRgx.IsMatch str then
    Just Float
  elif boolRgx.IsMatch str then
    Just Bool
  else
    Nothing
   ;;

let addVariable name value vars =
  if List.exists (fun (_,n) -> n = name) vars then
    Nothing
  else
  let t = typeOf value in
  let assignInt   v = intVariables := (name, (parseInt v)) :: !intVariables in
  let assignFloat v = floatVariables := (name, (parseFloat v)) :: !floatVariables in
  let assignBool  v = boolVariables := (name, (parseBool v))  :: !boolVariables in
  match t with
  | Just Int   -> assignInt value;   Just ((Int, name) :: vars)
  | Just Float -> assignFloat value; Just ((Float, name) :: vars)
  | Just Bool  -> assignBool value;  Just ((Bool, name) :: vars)
  | _ -> Nothing
  ;;

let emptyErrs: (int * string) list = List.Empty;;
let errs = ref emptyErrs;;

let evalLetStatement vars name ls =
  match ls with
  | "="::xs ->
    match xs with
    | hd::[] -> addVariable name hd vars
    | _ -> Nothing
  | _       -> Nothing
  ;;
    
let evalVarStatement vars name ls =
  match ls with
  | "="::xs -> Just vars
  | _       -> Nothing
  ;;

let evalAssignment vars ls =
  match ls with
  | "let"::n::xs -> evalLetStatement vars n xs
  | "var"::n::xs -> evalVarStatement vars n xs
  | _            -> Nothing
  ;;

type VarsList = (VariableType * string) list;;

type 'a Evaluator = (VariableType * string) list -> 'a list -> (VariableType * string) list maybe;;

type Reader = System.IO.StreamReader

let readLine line fn idx =
  if not (System.String.IsNullOrWhiteSpace !line) then
      let vars = fn !variables (splitString !line)
      if vars = Nothing then
        errs := (!idx, "Error") :: !errs

let readAllLines (path: string) (fn: string Evaluator) =
  use reader = new Reader(path) in
  let line = ref "" in
  let idx = ref 1 in
  while !line <> null do 
    line := reader.ReadLine()
    printfn "%d %s" !idx !line
    readLine line fn idx
    idx := !idx + 1
  done
  ;;

let eval path =
  readAllLines path evalAssignment;;

System.Console.WriteLine(splitted)
ignore(System.Console.Read())
```
