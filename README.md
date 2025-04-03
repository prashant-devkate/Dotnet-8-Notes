# Dotnet-Core-Notes

## 1. **Dependency Injection (DI) - Quick Notes**  

1. **Definition**: DI is a design pattern that helps achieve **loose coupling** by injecting dependencies instead of creating them inside a class.  

2. **Types of DI**:  
   - **Constructor Injection** (Most common)  
   - **Method Injection**  
   - **Property Injection**  

3. **Lifetimes in .NET Core**:  
   - `Singleton` → One instance for the entire app.  
   - `Scoped` → One instance per request.  
   - `Transient` → New instance every time.  

4. **Example in .NET Core `Program.cs`**:  
   ```csharp
   builder.Services.AddScoped<IMyService, MyService>();  
   ```  
   ```csharp
   public class MyController(IMyService service) { _service = service; }
   ```  

5. **Benefits**:  
   ✅ Improves **code maintainability**  
   ✅ Enables **unit testing** (mocking dependencies)  
   ✅ Follows **SOLID principles**

## 2. **`ConfigureServices()` & `Configure()` in .NET Core 8+**  

✅ **No longer exist in .NET 8+** (Removed from `Startup.cs`).  
✅ **Replaced by `Program.cs` minimal hosting model.**  

### **New Approach in .NET 8+**  

1. **Service Configuration** (Replaces `ConfigureServices`)  
   ```csharp
   var builder = WebApplication.CreateBuilder(args);
   builder.Services.AddControllers();
   ```

2. **Middleware Configuration** (Replaces `Configure`)  
   ```csharp
   var app = builder.Build();
   app.UseRouting();
   app.UseAuthorization();
   app.MapControllers();
   app.Run();
   ```  

🚀 **Everything is now configured in `Program.cs`!**

## 3. Serialization & Deserialization in .NET 8
1. Using System.Text.Json (Recommended)
✅ Serialize (Object → JSON)

csharp
Copy
Edit
using System.Text.Json;

var obj = new { Name = "John", Age = 30 };
string json = JsonSerializer.Serialize(obj);
✅ Deserialize (JSON → Object)

```csharp
var person = JsonSerializer.Deserialize<Person>(json);
```
