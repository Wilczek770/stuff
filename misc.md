# out var

```csharp
static int? ParseInt23(string text) =>
    int.TryParse(text, out int value) ? value : (int?) null;
```

---

Underscores within numeric literals are being ignored. They can be used to improve readability, like (all vars are valid):
```csharp
byte b = 0b1000_0111;
byte b = 0b_1000_0111;
var maxInt = 2_142_483_647;
var wideFifteen = 1_____________________5;
```

---

Adding an attribute to a backing field of a property 

* Old way:

```csharp
[Demo]
private string _name;
public string Name
{
    get { return name; }
    set { name = value; }
}
```

* New, fantastic way:
```csharp
[field: Demo]
public string Name{ get; set; }
```
