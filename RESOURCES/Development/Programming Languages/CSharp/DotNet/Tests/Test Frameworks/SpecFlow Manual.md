## 📌 **Introduction**

SpecFlow brings Behavior-Driven Development (BDD) to [[DotNet|.NET]], allowing you to write tests in Gherkin language using human-readable scenarios. It integrates with popular test runners like [[xUnit Manual|xUnit]], NUnit, and MSTest.

---

## 📦 **Installation**

```bash
# Install SpecFlow NuGet Packages
dotnet add package SpecFlow

dotnet add package SpecFlow.xUnit  # or SpecFlow.NUnit / SpecFlow.MSTest

dotnet add package SpecFlow.Tools.MsBuild.Generation
```

---

## 🔄 **Creating a Feature File**

Create a `.feature` file in your test project:

```gherkin
Feature: Calculator
  In order to avoid silly mistakes
  As a math student
  I want to be told the sum of two numbers

  Scenario: Add two numbers
    Given the first number is 50
    And the second number is 70
    When the two numbers are added
    Then the result should be 120
```

---

## 🛠️ **Generating Step Definitions**

```csharp
[Binding]
public class CalculatorSteps
{
    private int _firstNumber;
    private int _secondNumber;
    private int _result;

    [Given("the first number is (.*)")]
    public void GivenTheFirstNumberIs(int number)
    {
        _firstNumber = number;
    }

    [Given("the second number is (.*)")]
    public void GivenTheSecondNumberIs(int number)
    {
        _secondNumber = number;
    }

    [When("the two numbers are added")]
    public void WhenTheTwoNumbersAreAdded()
    {
        _result = _firstNumber + _secondNumber;
    }

    [Then("the result should be (.*)")]
    public void ThenTheResultShouldBe(int expectedResult)
    {
        _result.Should().Be(expectedResult);
    }
}
```

---

## 📑 **Running Tests**

- You can run SpecFlow tests using any supported runner (xUnit, NUnit, MSTest).
    
- Compatible with Visual Studio Test Explorer and CLI:
    

```bash
dotnet test
```

---

## 🔍 **Advanced Topics**

### 🏰 **Scenario Outline (Data-Driven Tests)**

```gherkin
Scenario Outline: Multiply numbers
    Given the first number is <first>
    And the second number is <second>
    When the two numbers are multiplied
    Then the result should be <result>

    Examples:
      | first | second | result |
      | 2     | 3      | 6      |
      | 5     | 4      | 20     |
```

### 🏙️ **Hooks (Before/After Execution)**

```csharp
[Binding]
public class Hooks
{
    [BeforeScenario]
    public void BeforeScenario()
    {
        Console.WriteLine("Starting Scenario...");
    }

    [AfterScenario]
    public void AfterScenario()
    {
        Console.WriteLine("Scenario Completed.");
    }
}
```

### 📊 **Context Injection**

```csharp
public class CalculatorContext
{
    public int FirstNumber { get; set; }
    public int SecondNumber { get; set; }
    public int Result { get; set; }
}

[Binding]
public class CalculatorSteps
{
    private readonly CalculatorContext _context;

    public CalculatorSteps(CalculatorContext context)
    {
        _context = context;
    }

    [Given("the first number is (.*)")]
    public void GivenTheFirstNumberIs(int number)
    {
        _context.FirstNumber = number;
    }
}
```

---

## 📓 **Best Practices**

- ✅ Keep feature files focused on business logic, not implementation.
    
- ✅ Reuse step definitions to avoid redundancy.
    
- ✅ Use Scenario Outlines for data-driven testing.
    
- ✅ Organize hooks carefully to avoid unnecessary setup overhead.
    
- ✅ Combine with FluentAssertions for better validation readability.
    

---

# 📅 **Changelog**

- **v1.0:** Initial version with BDD workflow and examples.
    

---

#dotnet #specflow #bdd #testing #gherkin`