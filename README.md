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

```csharp
using System.Text.Json;

var obj = new { Name = "John", Age = 30 };
string json = JsonSerializer.Serialize(obj);
```

✅ Deserialize (JSON → Object)

```csharp
var person = JsonSerializer.Deserialize<Person>(json);
```

## 4. **Synchronous vs Asynchronous Programming**  

#### **1. Synchronous Programming**  
✅ **Executes tasks sequentially** (one after another).  
✅ **Blocks execution** until the current task completes.  
✅ **Slower for I/O operations** (e.g., database, API calls).  

**Example:**  
```csharp
public void SyncMethod() {
    Console.WriteLine("Task 1");
    Thread.Sleep(3000); // Simulating delay
    Console.WriteLine("Task 2");
}
```

#### **2. Asynchronous Programming**  
✅ **Executes tasks concurrently** (doesn’t block).  
✅ **Uses `async` & `await` for non-blocking execution.**  
✅ **Faster for I/O-bound tasks.**  

**Example:**  
```csharp
public async Task AsyncMethod() {
    Console.WriteLine("Task 1");
    await Task.Delay(3000); // Non-blocking delay
    Console.WriteLine("Task 2");
}
```

🚀 **Use async for better performance in I/O-bound operations!**

## 5. Attributes in .NET Core 8
✅ Definition: Attributes are metadata annotations.

1. Commonly Used Attributes
   
✔ API & Routing Attributes
[ApiController] → Marks a controller as an API controller.

[Route("api/[controller]")] → Defines the route pattern.

[HttpGet], [HttpPost], [HttpPut], [HttpDelete] → Define HTTP methods.

```csharp
[ApiController]
[Route("api/users")]
public class UsersController : ControllerBase {
    [HttpGet] public IActionResult GetUsers() => Ok(new { Name = "John" });
}
```
✔ Data Validation Attributes (System.ComponentModel.DataAnnotations)
[Required] → Field must have a value.
[MaxLength(50)] → Restricts max length.
[Range(1, 100)] → Ensures value is within range.
[EmailAddress] → Validates email format.

```csharp
public class User {
    [Required] public string Name { get; set; }
    [EmailAddress] public string Email { get; set; }
}
```
🚀 Attributes enhance readability, validation, and API behavior!

## 6. **Nullable Reference Types in .NET Core 8**  

✅ **Prevents `null` reference errors at compile time.**  
✅ **Introduced in C# 8, enabled by default in .NET 8.**  

---

### **1. Enable/Disable Nullable Reference Types**  
- **Enabled by default in .NET 8** (`.csproj` file):  
  ```xml
  <Nullable>enable</Nullable>
  ```
- **Disable it if needed:**  
  ```xml
  <Nullable>disable</Nullable>
  ```

---

### **2. Usage**  

#### **✔ Nullable (`?` operator)**
```csharp
string? name = null; // Allowed
```

#### **✔ Non-Nullable (Default)**
```csharp
string name = null; // ❌ Compilation Warning (Possible null assignment)
```

🚀 **Helps avoid runtime `NullReferenceException`**

## 7. **Logging in .NET Core 8**  

✅ **Built-in logging via `Microsoft.Extensions.Logging`**  

### **1. Log Levels**  
🔹 `LogTrace` → Detailed logs  
🔹 `LogDebug` → Debug info  
🔹 `LogInformation` → App flow  
🔹 `LogWarning` → Potential issues  
🔹 `LogError` → Errors  
🔹 `LogCritical` → Critical failures  

---

### **2. Configure Logging (`Program.cs`)**  
```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Logging.AddConsole(); // Add logging
var app = builder.Build();
```

---

### **3. Use Logging in Services/Controllers**  
```csharp
public class MyService(ILogger<MyService> logger) {
    public void Process() {
        logger.LogInformation("Processing started");
        logger.LogError("Error occurred!");
    }
}
```

## 8. **HttpClient vs. HttpClientFactory in .NET Core 8**  

### **1. ❌ `HttpClient` (Not Recommended)**  → Simple but can cause **socket exhaustion**.
```csharp
using var client = new HttpClient();
var response = await client.GetAsync("https://api.example.com/data");
```

### **2. ✅ `HttpClientFactory` (Best Practice)**  → Manages `HttpClient` efficiently. 
🔹 **Register in `Program.cs`**  
```csharp
builder.Services.AddHttpClient();
```

🔹 **Inject & Use in Service**  
```csharp
public class MyService(HttpClient client) {
    public async Task<string> GetData() => await client.GetStringAsync("https://api.example.com/data");
}
```
🚀 **Use `HttpClientFactory` for better performance!**

## 9. **`ref` vs. `out` in C#**  

✅ **Use `ref` to modify & `out` to return multiple values!**

🔹 **`ref`** → Requires initialization before passing.  
🔹 **`out`** → No need to initialize before passing.  

```csharp
void Modify(ref int x) { x += 10; }  
int value = 5; Modify(ref value); // ✅ Must be initialized

void GetValues(out int a, out int b) { a = 10; b = 20; }  
int x, y; GetValues(out x, out y); // ✅ No need to initialize  
```

## 10. **`const` for  vs. `readonly` in C#**  

🔹 **`const`** → Compile-time, fixed value, must initialize.  
🔹 **`readonly`** → Runtime values.  

```csharp
const double Pi = 3.14;  // Compile-time constant
readonly int id;         
public MyClass(int value) { id = value; } // Set at runtime
```

## 11. **Action Verbs in .NET Core**  

#### **1. HTTP (Web API)**  
- `Get` → Retrieve  
- `Post` → Create  
- `Put` → Update  
- `Delete` → Remove  

```csharp
[HttpGet] public IActionResult GetItems() { }
```
#### **2. EF Core (Database)**  
- `Add` → Insert  
- `Find` → Locate  
- `Update` → Modify  
- `Remove` → Delete  
- `SaveChanges` → Commit  

```csharp
dbContext.Add(entity);
dbContext.SaveChanges();
```
#### **3. Dependency Injection**  
- `Register` → Add service  
- `Inject` → Use service  

#### **4. Middleware**  
- `Use` → Add  
- `Invoke` → Execute  
```csharp
app.UseRouting();
```

## 12. **Important .NET API Status Codes**  

#### **1. Success Responses**  
- **`200 OK`** → Request successful  
- **`201 Created`** → Resource created  
- **`204 No Content`** → Successful, but no response  

#### **2. Client Errors**  
- **`400 Bad Request`** → Invalid input  
- **`401 Unauthorized`** → Authentication required  
- **`403 Forbidden`** → Access denied  
- **`404 Not Found`** → Resource missing  
- **`409 Conflict`** → Duplicate/conflicting request  

#### **3. Server Errors**  
- **`500 Internal Server Error`** → Unexpected error  
- **`502 Bad Gateway`** → Invalid response from upstream  
- **`503 Service Unavailable`** → Server overloaded

## 13. **.NET Core Middleware - Quick Notes**  

Middleware processes requests & responses in the pipeline.  

#### **1. Built-in Middleware**  ✔ Add in `Program.cs`.
- `UseRouting()` → Enables routing.  
- `UseAuthorization()` → Handles security.  
- `UseExceptionHandler()` → Manages errors.  
- `UseCors()` → Allows cross-origin requests.  

#### **2. Custom Middleware**  
✔ Create a separate class.  
✔ Implement `Invoke` method.  
✔ Register in `Program.cs`.  

```csharp
public class MyMiddleware(RequestDelegate next) {  
    public async Task Invoke(HttpContext ctx) {  
        // Custom logic  
        await next(ctx);  
    }  
}
app.UseMiddleware<MyMiddleware>();
```

## 14. **Exception Handling in .NET Core**  

✅ **1. Try-Catch (Method-Level)**  
```csharp
try { /* Code */ }  
catch (Exception ex) { return StatusCode(500, "Error"); }
```

✅ **2. Exception Filters (Controller-Level)**  
```csharp
public class CustomExceptionFilter : ExceptionFilterAttribute {
    public override void OnException(ExceptionContext ctx) {
        ctx.Result = new ObjectResult("Error") { StatusCode = 500 };
    }
}
```
`[ServiceFilter(typeof(CustomExceptionFilter))]`

✅ **3. Global Handling (`UseExceptionHandler`)**  -> `Program.cs`
```csharp
app.UseExceptionHandler("/error");
```

✅ **4. Return Structured Error (`ProblemDetails`)**  
```csharp
return Problem(statusCode: 500, title: "Error");
```

## 15. ### **.NET Core Filters**  

 **Filters** control execution flow 

✅ **1. Types of Filters**  (ARAER)
- **Authorization** → `[Authorize]` (Runs before action).  
- **Resource** → Before/after model binding.  
- **Action** → Before/after action execution.  
- **Exception** → Handles unhandled errors.  
- **Result** → Before/after returning results.  

✅ **2. Custom Action Filter**  
```csharp
public class LogFilter : ActionFilterAttribute {
    public override void OnActionExecuting(ActionExecutingContext ctx) {
        Console.WriteLine("Action Started...");
    }
}
```

