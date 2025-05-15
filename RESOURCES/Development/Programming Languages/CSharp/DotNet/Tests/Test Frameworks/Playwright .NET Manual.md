## 📌 **Introduction**

Playwright is a fast and reliable end-to-end testing framework for modern web applications. It supports multiple browsers (Chromium, Firefox, WebKit) and works seamlessly with [[DotNet|.NET]] for UI automation.

---

## 📦 **Installation**

```bash
dotnet new console -n PlaywrightTests
cd PlaywrightTests

dotnet add package Microsoft.Playwright
```

Initialize Playwright browsers:

```bash
dotnet playwright install
```

---

## ✅ **Basic Usage**

### 🛠️ **Launching a Browser and Navigating**

```csharp
using Microsoft.Playwright;

var playwright = await Playwright.CreateAsync();
var browser = await playwright.Chromium.LaunchAsync(new BrowserTypeLaunchOptions { Headless = false });
var page = await browser.NewPageAsync();

await page.GotoAsync("https://example.com");
await page.ScreenshotAsync(new PageScreenshotOptions { Path = "screenshot.png" });

await browser.CloseAsync();
```

---

## 📋 **Interacting with Elements**

```csharp
await page.FillAsync("#username", "testuser");
await page.FillAsync("#password", "secret");
await page.ClickAsync("button[type='submit']");
```

### 📚 **Assertions**

```csharp
await page.WaitForSelectorAsync(".success-message");
var successMessage = await page.InnerTextAsync(".success-message");

successMessage.Should().Be("Login Successful");
```

---

## 🔐 **Handling Authentication**

```csharp
await page.GotoAsync("https://example.com/login");
await page.FillAsync("#user", "admin");
await page.FillAsync("#pass", "password");
await page.ClickAsync("#login");
```

---

## 📂 **Running Tests with Playwright Test Runner**

```bash
dotnet test
```

### 📖 **Example Test Class**

```csharp
public class LoginTests
{
    [Fact]
    public async Task Should_Login_Successfully()
    {
        using var playwright = await Playwright.CreateAsync();
        var browser = await playwright.Chromium.LaunchAsync();
        var page = await browser.NewPageAsync();

        await page.GotoAsync("https://example.com/login");
        await page.FillAsync("#username", "testuser");
        await page.FillAsync("#password", "password");
        await page.ClickAsync("button[type='submit']");

        var successMessage = await page.InnerTextAsync(".success");
        successMessage.Should().Be("Welcome, testuser!");

        await browser.CloseAsync();
    }
}
```

---

## 🚀 **Parallel Test Execution**

Playwright can run multiple browser instances in parallel to speed up test execution. You can configure parallelism in your CI/CD pipeline or within your test framework settings.

---

## 📅 **Best Practices**

- ✅ Reuse browser contexts to improve performance.
    
- ✅ Use selectors carefully to avoid flaky tests.
    
- ✅ Capture screenshots and logs on test failures.
    
- ✅ Clean up resources by properly closing browsers and contexts.
    
- ✅ Combine with FluentAssertions for expressive validations.
    

---

# 📅 **Changelog**

- **v1.0:** Initial version with browser automation, interaction, and assertions.
    

---

#dotnet #playwright #ui #automation #endtoendtesting #testing`