## 📌 **Introduction**

Fluent Assertions is a [[DotNet|.NET]] library that makes unit tests more readable and expressive.

---

## 📦 **Installation**

```bash
dotnet add package FluentAssertions
```

---

## ✅ **Basic Usage**

### 🗂️ **Common Assertions**

```csharp
int result = 5;
result.Should().Be(5);
result.Should().BeGreaterThan(3);
result.Should().BeInRange(1, 10);
```

### 📚 **String Assertions**

```csharp
string name = "FluentAssertions";

name.Should().StartWith("Fluent");
name.Should().EndWith("Assertions");
name.Should().Contain("Assert");
name.Should().NotBeNullOrEmpty();
```

### 📅 **DateTime Assertions**

```csharp
DateTime date = DateTime.Now;

date.Should().BeAfter(DateTime.Today);
date.Should().BeOnOrBefore(DateTime.Today.AddDays(1));
```

### 📋 **Collection Assertions**

```csharp
var numbers = new[] { 1, 2, 3 };

numbers.Should().HaveCount(3);
numbers.Should().Contain(2);
numbers.Should().ContainInOrder(new[] { 1, 2, 3 });
numbers.Should().OnlyHaveUniqueItems();
```

---

## 🧹 **Advanced Assertions**

### 📦 **Object Equivalency**

```csharp
var expected = new { Id = 1, Name = "Test" };
var actual = new { Id = 1, Name = "Test" };

actual.Should().BeEquivalentTo(expected);
```

#### 🎯 Ignoring Specific Members

```csharp
actual.Should().BeEquivalentTo(expected, options => options.Excluding(obj => obj.Id));
```

### 📜 **Exception Assertions**

```csharp
Action action = () => throw new InvalidOperationException("Invalid!");

action.Should().Throw<InvalidOperationException>()
    .WithMessage("Invalid!");
```

---

## 📖 **Assertions on Collections of Objects**

```csharp
var users = new[]
{
    new User { Id = 1, Name = "John" },
    new User { Id = 2, Name = "Jane" }
};

users.Should().ContainSingle(u => u.Name == "John");
users.Should().AllSatisfy(u => u.Id.Should().BeGreaterThan(0));
```

---

## 🛠️ **Custom Assertion Scopes**

```csharp
using (new AssertionScope())
{
    5.Should().Be(10);
    "abc".Should().HaveLength(5);
}
// Both failures will be reported together.
```

---

## 🦪 **Async Assertions**

```csharp
Func<Task> asyncAction = async () => await Task.Delay(100);

await asyncAction.Should().NotThrowAsync();
```

---

## 📅 **Date and Time Precision**

```csharp
var time = DateTime.Now;

time.Should().BeCloseTo(DateTime.Now, TimeSpan.FromSeconds(5));
```

---

## 📓 **Best Practices**

- ✅ Use `BeEquivalentTo` for comparing object graphs.
    
- ✅ Use Assertion Scopes to aggregate multiple failures.
    
- ✅ Always check for exceptions using `.Throw<ExceptionType>()`.
    
- ✅ Prefer `.Should().NotBeNull()` instead of direct `Assert`.
    

---

# 📅 **Changelog**

- **v1.0:** Initial version with all common usages.
    

---

#dotnet #fluentassertions #testing #unittest`