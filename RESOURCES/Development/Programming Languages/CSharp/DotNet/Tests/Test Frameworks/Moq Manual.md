## 📌 **Introduction**

Moq is a popular [[DotNet|.NET]] library for creating mock objects in unit tests, allowing you to isolate and verify behaviors of dependencies.

---

## 📦 **Installation**

```bash
dotnet add package Moq
```

---

## ✅ **Basic Usage**

### 🛠️ **Creating a Mock**

```csharp
var mockService = new Mock<IUserService>();
```

### 🌐 **Setting Up Methods**

```csharp
mockService.Setup(s => s.GetUserName(1)).Returns("John Doe");

string name = mockService.Object.GetUserName(1);
name.Should().Be("John Doe");
```

### ⏳ **Setting Up Async Methods**

```csharp
mockService.Setup(s => s.GetUserAsync(1))
            .ReturnsAsync(new User { Id = 1, Name = "John" });

var user = await mockService.Object.GetUserAsync(1);
user.Name.Should().Be("John");
```

### 🌮 **Mocking Properties**

```csharp
mockService.SetupProperty(s => s.IsActive, true);

bool isActive = mockService.Object.IsActive;
isActive.Should().BeTrue();
```

---

## 🔍 **Verification**

### 🔹 **Verify Method Calls**

```csharp
mockService.Verify(s => s.GetUserName(1), Times.Once());
```

### 🔹 **Verify Property Access**

```csharp
mockService.VerifyGet(s => s.IsActive, Times.AtLeastOnce());
```

### 🔹 **Verify No Other Calls**

```csharp
mockService.VerifyNoOtherCalls();
```

---

## 💖 **Advanced Configurations**

### 🔄 **Chained Setups (Callbacks)**

```csharp
mockService.Setup(s => s.SaveUser(It.IsAny<User>()))
            .Callback<User>(u => Console.WriteLine($"Saved: {u.Name}"));
```

### 🔄 **Throwing Exceptions**

```csharp
mockService.Setup(s => s.GetUserName(It.IsAny<int>()))
            .Throws(new InvalidOperationException("User not found"));
```

### 📅 **Mocking Sequences**

```csharp
mockService.SetupSequence(s => s.GetUserName(1))
            .Returns("First Call")
            .Returns("Second Call");
```

---

## 🧑‍💻 **Partial Mocks**

```csharp
var mock = new Mock<MyService>() { CallBase = true };
mock.Setup(m => m.VirtualMethod()).Returns("Mocked Result");
```

---

## 📓 **Best Practices**

- ✅ Use `It.IsAny<T>()` for parameter flexibility.
    
- ✅ Verify critical calls with `Verify` to ensure expected behavior.
    
- ✅ Use `Callback` to simulate side effects.
    
- ✅ Clean up mocks using `VerifyNoOtherCalls` to ensure no unexpected interactions.
    

---

# 📅 **Changelog**

- **v1.0:** Initial version with core usage and examples.
    

---

#dotnet #moq #testing #unittest #mocking`