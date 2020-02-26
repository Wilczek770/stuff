[Link to description @Twitter](https://twitter.com/pzelmanski/status/1232610956741664768)

```csharp
    public class CollectionInitializer
    {
        public List<string> CreateListWithCollectionInitializer() =>
            new List<string>
            {
                1, // Extension method Add(int value)
                {20, 3}, // Extension method Add(int value, int repeat)
                "Default Add(string)" // Default Add(string)
            };

        public List<string> CreateListWithoutCollectionInitializer()
        {
            var toReturn = new List<string>();
            toReturn.Add(1);
            toReturn.Add(20, 3);
            toReturn.Add("Default Add(string)");
            return toReturn;
        }

        public List<string> CreateListStringWithCollectionInitializerUsingGenericAdd() =>
            new List<string>
            {
                new List<string> {"one", "two", "three"},
                new List<string> {"four", "five"}
            };

        public List<int> CreateListIntWithCollectionInitializerUsingGenericAdd() =>
            new List<int>
            {
                new List<int> {1, 2, 3},
                new List<int> {4, 5}
            };
    }

    // Extension methods adding needed Add methods for collection initializer to work
    public static class StringExtensions
    {
        public static void Add<T>(this List<T> list, List<T> values) =>
            list.AddRange(values);
        
        public static void Add(this List<string> list, int value) =>
            list.Add(value.ToString());

        public static void Add(this List<string> list, int value, int repeat) =>
            list.AddRange(Enumerable.Repeat(value.ToString(), repeat));
    }
```