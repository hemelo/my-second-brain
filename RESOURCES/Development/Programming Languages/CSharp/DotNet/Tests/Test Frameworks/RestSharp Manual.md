## 📌 **Introduction**

RestSharp is a powerful and easy-to-use [[DotNet|.NET]] library for consuming RESTful APIs. It simplifies HTTP requests and provides a fluent interface for working with APIs.

> ⚠️ **Note:** RestSharp is an HTTP client library, **not a dedicated testing framework**. However, it is often used in **API integration tests** to test ASP.NET Core APIs by simulating HTTP calls.

---

## 📦 **Installation**

```bash
dotnet add package RestSharp
```

---

## ✅ **Basic Usage**

### 🛠️ **GET Request**

```csharp
using RestSharp;

var client = new RestClient("https://api.example.com");
var request = new RestRequest("/users", Method.Get);

var response = await client.ExecuteAsync(request);
Console.WriteLine(response.Content);
```

### 🔄 **POST Request with Body**

```csharp
var request = new RestRequest("/users", Method.Post);
request.AddJsonBody(new { Name = "John Doe", Email = "john@example.com" });

var response = await client.ExecuteAsync(request);
```

### 📚 **Deserializing Response**

```csharp
var response = await client.ExecuteAsync<User>(request);
User user = response.Data;
```

---

## 🔐 **Authentication**

### 🔑 **Bearer Token**

```csharp
request.AddHeader("Authorization", "Bearer your_token_here");
```

### 🔑 **Basic Authentication**

```csharp
client.Authenticator = new RestSharp.Authenticators.HttpBasicAuthenticator("username", "password");
```

---

## 🔍 **Handling Query Parameters**

```csharp
request.AddQueryParameter("page", "1");
request.AddQueryParameter("limit", "10");
```

### 📋 **Adding Headers**

```csharp
request.AddHeader("Accept", "application/json");
request.AddHeader("Custom-Header", "Value");
```

### 📦 **Adding Files (Multipart Form Data)**

```csharp
request.AddFile("file", "path/to/file.txt");
```

---

## 📖 **Handling Responses**

```csharp
if (response.IsSuccessful)
{
    Console.WriteLine("Success: " + response.Content);
}
else
{
    Console.WriteLine($"Error: {response.StatusCode} - {response.ErrorMessage}");
}
```

---

## 🧪 **Using RestSharp for ASP.NET Integration Testing**

### 📖 **Example: API Integration Test**

```csharp
public class UserApiTests
{
    private readonly RestClient _client = new("https://localhost:5001");

    [Fact]
    public async Task Should_Return_User_By_Id()
    {
        var request = new RestRequest("/api/users/1", Method.Get);
        var response = await _client.ExecuteAsync<User>(request);

        response.IsSuccessful.Should().BeTrue();
        response.Data.Should().NotBeNull();
        response.Data!.Id.Should().Be(1);
    }
}
```

### 📚 **When to Use RestSharp**

- ✅ Writing **integration tests** that interact with a real running API.
    
- ✅ Verifying HTTP endpoints of your ASP.NET Core API.
    
- ✅ Simulating external API consumers.
    

### 🚫 **When Not to Use RestSharp**

- ❌ For **unit testing controllers or services** directly. Use xUnit, Moq, FluentAssertions.
    
- ❌ When testing internal application logic without HTTP communication.
    

---

## 📋 **Best Practices**

- ✅ Use typed clients for consistent API interaction.
    
- ✅ Always check `IsSuccessful` and handle errors explicitly.
    
- ✅ Reuse `RestClient` instances to improve performance.
    
- ✅ Use `AddJsonBody` for cleaner payload handling.
    
- ✅ Combine with Polly for retry and resilience strategies.
    

---

# 📅 **Changelog**

- **v1.0:** Initial version with HTTP methods, authentication, response handling, and integration testing guidance.
    

---

#dotnet #restsharp #api #httpclient #integrationtests #aspnet`