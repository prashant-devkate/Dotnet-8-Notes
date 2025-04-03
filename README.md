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
