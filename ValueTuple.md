# ValueTuples

1. ValueTuple
1. ValueTuple\<T1>
1. ValueTuple<T1,T2>
1. ValueTuple<T1,T2,T3>
1. ValueTuple<T1,T2,T3,T4>
1. ValueTuple<T1,T2,T3,T4,T5>
1. ValueTuple<T1,T2,T3,T4,T5,T6>
1. ValueTuple<T1,T2,T3,T4,T5,T6,T7>
1. ValueTuple<T1,T2,T3,T4,T5,T6,T7,TRest>

---
1. ValueTuples with arity between 2 and 7 are pretty similar and normal
1. ValueTuple with arity 1 cannot be created in a 'normal' way.
    - It can be created within ValueTuple with arity 8, like this:
    - ValueTuple<int, int, int, int, int, int, int, **ValueTuple\<int>**>
1. Nongeneric ValueTuple, the one without any elements, is also called `nuple`. It doesn't seem to be useful at this moment.

```csharp
var tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16);
Console.WriteLine(tuple.Item16);
```
This above code is totally legal. Although, it's converted by compiler into `tuple.Rest.Rest.Item2` 

---
### Decomposition

- given method 
```csharp
static (int x, int y, string z) MethodReturningTuple()
```
this two are equivalent:
```csharp
(var a, var b, var c) = MethodReturningTuple();
var (a, b, c) = MethodReturningTuple();
```

Decomposition of a tuple to inline class constructor:
```csharp
public class Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) =>
        (X, Y) = (x, y);
}
```