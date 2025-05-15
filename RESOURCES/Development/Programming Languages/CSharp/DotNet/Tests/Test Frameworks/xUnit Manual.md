## 📌 **Introduction**

xUnit is a popular testing framework for [[DotNet|.NET]], focused on extensibility, clean syntax, and modern features for unit testing.

---

## 📦 **Installation**

```bash
dotnet add package xunit
dotnet add package xunit.runner.visualstudio
```

---

## ✅ **Basic Usage**

### 🛠️ **Simple Test Class**

```csharp
public class CalculatorTests
{
    [Fact]
    public void Add_ShouldReturnCorrectSum()
    {
        var calculator = new Calculator();
        int result = calculator.Add(2, 3);

        result.Should().Be(5);
    }
}
```

### 📈 **Parameterized Tests**

```csharp
public class MathTests
{
    [Theory]
    [InlineData(2, 3, 5)]
    [InlineData(0, 0, 0)]
    [InlineData(-1, 1, 0)]
    public void Add_ShouldWorkCorrectly(int a, int b, int expected)
    {
        var calculator = new Calculator();
        int result = calculator.Add(a, b);

        result.Should().Be(expected);
    }
}
```

### 🤝 **Member Data Tests**

```csharp
public static IEnumerable<object[]> GetTestData()
{
    yield return new object[] { 1, 2, 3 };
    yield return new object[] { 5, 5, 10 };
}

public class AdvancedTests
{
    [Theory]
    [MemberData(nameof(GetTestData))]
    public void Add_WithMemberData(int a, int b, int expected)
    {
        var calculator = new Calculator();
        int result = calculator.Add(a, b);

        result.Should().Be(expected);
    }
}
```

---

## 🧬 **Lifecycle Hooks**

### 🔙 **Constructor for Setup**

```csharp
public class SampleTests
{
    private readonly Calculator _calculator;

    public SampleTests()
    {
        _calculator = new Calculator();
    }

    [Fact]
    public void TestCalculator()
    {
        _calculator.Add(1, 1).Should().Be(2);
    }
}
```

### ⏳ **IClassFixture for Shared Context**

```csharp
public class DatabaseFixture
{
    public DatabaseFixture()
    {
        // Initialize expensive resources
    }
}

public class DatabaseTests : IClassFixture<DatabaseFixture>
{
    private readonly DatabaseFixture _fixture;

    public DatabaseTests(DatabaseFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public void TestWithFixture()
    {
        // Use _fixture
    }
}
```

---

## 🔍 **Asserting Exceptions**

```csharp
[Fact]
public void ShouldThrowException()
{
    Action act = () => throw new InvalidOperationException("Error!");

    act.Should().Throw<InvalidOperationException>()
       .WithMessage("Error!");
}
```

---

## 📓 **Best Practices**

- ✅ Use `[Fact]` for simple tests without parameters.
    
- ✅ Use `[Theory]` for parameterized tests to avoid code duplication.
    
- ✅ Group related tests in meaningful classes.
    
- ✅ Use `IClassFixture` for expensive or shared setup.
    
- ✅ Combine with **FluentAssertions** for better readability.
    

---

# 📅 **Changelog**

- **v1.0:** Initial version with complete xUnit examples and patterns.
    

---

#dotnet #xunit #testing #unittest