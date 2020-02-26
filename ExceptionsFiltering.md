# Exceptions filtering

* Program:

```csharp
    class Program
    {
        static void Main(string[] args)
        {       
            try
            {
                new ExceptionsFiltering().Do();
            }
            catch (Exception e)
            {
                Console.WriteLine($"Uncaught: {e.Message}");
            }
        }
    }

    public class ExceptionsFiltering
    {
        public void Do()
        {
            var messages = new[] {"inner", "inner too", "outer"};
            foreach (var message in messages)
            {
                try
                {
                    throw new ArgumentException(message);

                }
                catch (ArgumentException e) when (e.Message.Contains("inner"))
                {
                    
                    Console.WriteLine($"Caught: {e.Message}");
                }
            }
        }
    }
```
* Console output:
```
    Caught: inner
    Caught: inner too
    Uncaught: outer
```