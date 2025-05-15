## 📌 **Introduction**

AutoFixture is a [[DotNet|.NET]] library designed to minimize the setup phase of unit tests by automatically generating object instances with random data. It’s perfect for reducing boilerplate and focusing on behavior rather than data preparation.

---

## 📦 **Installation**

```bash
dotnet add package AutoFixture

# For xUnit integration
dotnet add package AutoFixture.Xunit2

# For Moq integration
dotnet add package AutoFixture.AutoMoq
```

---

## ✅ **Basic Usage**

### 🛠️ **Creating a Fixture**

```csharp
using AutoFixture;

var fixture = new Fixture();
var user = fixture.Create<User>();
```

This will automatically populate all properties of the `User` class with random data.

### 🔹 **Customizing Property Values**

```csharp
var user = fixture.Build<User>()
                  .With(u => u.Name, "John Doe")
                  .Without(u => u.Address)  // Skip Address
                  .Create();
```

### 🌮 **Generating Collections**

```csharp
var users = fixture.CreateMany<User>(5); // Generates 5 User objects
```

---

## 📈 **Using with xUnit (AutoData)**

```csharp
public class UserTests
{
    [Theory, AutoData]
    public void UserName_ShouldNotBeNull(User user)
    {
        user.Name.Should().NotBeNullOrEmpty();
    }
}
```

### 🔄 **Inline AutoData**

```csharp
public class OrderTests
{
    [Theory, InlineAutoData(100)]
    public void Order_ShouldHaveCorrectAmount(int amount, Order order)
    {
        order.Amount = amount;
        order.Amount.Should().Be(100);
    }
}
```

---

## 🔍 **Integrating with Moq**

```csharp
var fixture = new Fixture().Customize(new AutoMoqCustomization());

var mockService = fixture.Freeze<Mock<IUserService>>();
mockService.Setup(s => s.GetUserName()).Returns("Mocked User");
```

---

## 🦜 **Advanced Customization**

### 🔄 **Customizing Data Generation Globally**

```csharp
fixture.Customize<User>(c => c.With(u => u.Name, "Fixed Name"));
```

### 🏢 **Customizing Complex Types**

```csharp
fixture.Customize<Address>(c => c.Without(a => a.Country));
```

---

## 📓 **Best Practices**

- ✅ Use `Freeze<T>()` to ensure consistent objects across a test.
    
- ✅ Prefer `.Build<T>()` for partial customization.
    
- ✅ Combine with Moq for cleaner mock setups.
    
- ✅ Use `AutoData` attributes to reduce manual parameter setup.
    
- ✅ Keep customization logic organized to avoid cluttered test code.
    

---

# 📅 **Changelog**

- **v1.0:** Initial version with core usage, integration, and customization.
    

---

#dotnet #autofixture #testing #unittest #mocking`