# Efficient logging on exception

This method allows to use existing flow in order to log exceptions.
whe `when` part will always be called to check if `catch` block should be called. Since `Log` inside `when` block always returns false, nothing will happen at `catch` statement, and exception will be logged. BRILIANT! 

```csharp
class LoggingFilter
    {
        static void Main()
        {
            try
            {
                UnreliableMethod();
            }
            catch (Exception e) when (Log(e))
            {
            }
        }        

        static void UnreliableMethod()
        {
            throw new Exception("Bang!");
        }

        static bool Log(Exception e)
        {
            Console.WriteLine($"{DateTime.UtcNow}: {e.GetType()} {e.Message}");
            return false;
        }
    }
```