# Map function
* **Common names:** Map, Select
* **What it does:** It lifts a function into an elevated world
* **Signature:** `(a->b) -> E<a> -> E<b>`, alternatively with reversed parameters: `E<a> -> (a->b) -> E<b>`

* Example of `Map` with `Option`:
```fsharp
let mapOption f opt =
    match opt with
    | None -> 
        None
    | Some x -> 
        Some (f x)
```
```fsharp
// Define a function in the normal world
let add1 x = x + 1
// has type : int -> int

// A function lifted to the world of Options
let add1IfSomething = Option.map add1
// has type : int option -> int option

// A function lifted to the world of Lists
let add1ToEachElement = List.map add1
// has type : int list -> int list

---
Some 2 |> add1IfSomething    // Some 3 
[1;2;3] |> add1ToEachElement // [2; 3; 4]

Some 2 |> Option.map add1    // Some 3
```

# Return function
* **Common names:** return, pure, unit, yield
* **What it does:** lifts a single value into an elevated world
* **Signature:** `a -> E<a>`

```fsharp
// A value lifted to the world of Options
let returnOption x = Some x
// has type : 'a -> 'a option

// A value lifted to the world of Lists
let returnList x  = [x]
// has type : 'a -> 'a list
```

# Apply function
* **What it does:** it unpacks a function wrapped inside an elevated value into a lifted function `E<a> -> E<b>`
* **Signature:** `E<(a->b)> -> E<a> -> E<b>`

* Example implementation of `apply` on `Option` type:
```fsharp
// The apply function for Options
let apply fOpt xOpt = 
    match fOpt,xOpt with
    | Some f, Some x -> Some (f x)
    | _ -> None
```
* Implementation of `Apply` on lists:
```fsharp
let apply (fList: ('a->'b) list) (xList: 'a list)  = 
        [ for f in fList do
          for x in xList do
              yield f x ]
```
In this case, a result from this apply is a multiplication product of two lists. That is, given two lists `[f, g]` and `[x, y]`, a result of applying `apply` on two lists is `[f x; f y; g x; g y]`

* It is common to create an infix operator, typically called `<*>` to replace keyworkd `apply`:
```fsharp
let resultOption =  
    let (<*>) = Option.apply
    (Some add) <*> (Some 2) <*> (Some 3)
// resultOption = Some 5
```
# Apply vs. Map
Code can be simplified a bit using infix operators:
```fsharp
let resultOption2 =  
    let (<!>) = Option.map
    let (<*>) = Option.apply

    add <!> (Some 2) <*> (Some 3)
// resultOption2 = Some 5
```

Example usage of map and apply to produce a sequence of strings:
```fsharp
let batman = 
    let (<!>) = List.map
    let (<*>) = List.apply

    // string concatenation using +
    (+) <!> ["bam"; "kapow"; "zap"] <*> ["!"; "!!"]  
    
// result =
// ["bam!"; "bam!!"; "kapow!"; "kapow!!"; "zap!"; "zap!!"]
```

# Applicative laws 
There are four laws that both `map` and `apply and return` should obey:
```haskell
pure id <*> v = v                            -- Identity
pure f <*> pure x = pure (f x)               -- Homomorphism
u <*> pure y = pure ($ y) <*> u              -- Interchange
pure (.) <*> u <*> v <*> w = u <*> (v <*> w) -- Composition
```
1. If you have an identity function in a traditional world, and you lift it into elevated world with `return`, and then you do `apply` the new function `E<a> -> E<a>` should be the same function as the `id` in elevated function *(is there a misteke in here? Shouldn't it be the same as in non-elevated world?)*

2. If you have a functoin `f` and a value `x` in the normal world, and you apply `f(x)` to get a result `y`, and then you elevate `y` with `return`, the result should be the same as first lifting `f` and `x`, and then doing `apply f x`

The other two laws are easily diagramed so there's no explanation of them [and im too lazy to do it by myself :)]

# LiftN family of functions
* **Common names:** Lift2, Lift3, Lift4, ..
* **What it does:** It combines two or more elevated values using a specified function
* **Signature:** 
    * Lift2: `(a->b->c) -> E<a> -> E<b> -> E<c>`
    * Lift3: `(a->b->c->d) -> E<a> -> E<b> -> E<c> -> E<d>`
* Lift1 is equivalent to a `map` function

Example implementation:
```fsharp
module Option = 
    let (<*>) = apply 
    let (<!>) = Option.map

    let lift2 f x y = 
        f <!> x <*> y
        
    let lift3 f x y z = 
        f <!> x <*> y <*> z
        
    let lift4 f x y z w = 
        f <!> x <*> y <*> z <*> w
```

And here's an example of lifting a two - parameter function:
```fsharp
// define a two-parameter function to test with
let addPair x y = x + y 

// lift a two-param function
let addPairOpt = Option.lift2 addPair

// call as normal
addPairOpt (Some 1) (Some 2) 
// result => Some 3
```
And analogically, an example of lifting a 3-parameter function:
```fsharp
// define a three-parameter function to test with
let addTriple x y z = x + y + z

// lift a three-param function
let addTripleOpt = Option.lift3 addTriple

// call as normal
addTripleOpt (Some 1) (Some 2) (Some 3)   
// result => Some 6
```

## Interpreting Lift2 as a combiner
The first one is adding optin values, the second one is multiplication of option values
```fsharp
Option.lift2 (+) (Some 2) (Some 3)   // Some 5
Option.lift2 (*) (Some 2) (Some 3)   // Some 6
```

A more generic approach:
```fsharp
// define a tuple creation function
let tuple x y = x,y

// create a generic combiner of options
// with the tuple constructor baked in
let combineOpt x y = Option.lift2 tuple x y 

// create a generic combiner of lists
// with the tuple constructor baked in
let combineList x y = List.lift2 tuple x y 
```

---
## Dictionary
* Elevation -> process of transforming a 'regular' entity into a functional world, like converting `int` into `Option<int>`
* Elevated function -> a function working on an elevated values, like `DoSomething(input: Option<int>)` 