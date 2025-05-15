## 📌 **Introduction**

The [[DotNet|.NET]] CLI (Command-Line Interface) provides powerful tools to manage projects, dependencies, builds, testing, and publishing directly from the terminal. It is essential for automating tasks, integrating with CI/CD pipelines, and managing modern [[DotNet|.NET]] projects.

---

## 📦 **Installing the .NET SDK**

```bash
# Check if .NET is installed
dotnet --version

# Install latest version from https://dotnet.microsoft.com/en-us/download
```

---

## ✅ **Essential .NET CLI Commands**

### 📁 **Project Management**

```bash
# Create a new console project
dotnet new console -n MyApp

# Create a new web API project
dotnet new webapi -n MyApi

# List available templates
dotnet new --list
```

### 📚 **Dependency Management**

```bash
# Add a NuGet package
dotnet add package FluentAssertions

# Remove a package
dotnet remove package FluentAssertions

# List project dependencies
dotnet list package
```

### 📦 **Build and Run**

```bash
# Build the project
dotnet build

# Run the application
dotnet run
```

### 🧪 **Testing**

```bash
# Run tests in the solution
dotnet test
```

### 🚀 **Publishing Applications**

```bash
# Publish for production deployment
dotnet publish -c Release -o ./publish
```

---

## 🔧 **Global Tools**

.NET allows installing global tools to simplify repetitive tasks.

### 📖 **Managing Tools**

```bash
# Install a global tool
dotnet tool install -g dotnet-ef

# List installed tools
dotnet tool list -g

# Update a tool
dotnet tool update -g dotnet-ef

# Uninstall a tool
dotnet tool uninstall -g dotnet-ef
```

### 📦 **Popular .NET Global Tools**

|Tool|Command|Purpose|
|---|---|---|
|dotnet-ef|Migrations, DB Management||
|dotnet-sonarscanner|Code Analysis with SonarQube||
|dotnet-outdated|Check outdated NuGet packages||
|dotnet-reportgenerator|Generate test coverage reports||
|dotnet-format|Format code automatically||

---

## 📅 **Best Practices**

- ✅ Automate repetitive tasks using CLI commands in scripts.
    
- ✅ Use `dotnet tool restore` to manage local tools via `dotnet-tools.json`.
    
- ✅ Keep global tools updated regularly.
    
- ✅ Integrate CLI commands in CI/CD pipelines.
    

---

## 🚀 **Conclusion**

Mastering [[DotNet|.NET]] CLI tools boosts productivity, enforces project consistency, and simplifies automation in modern software development.

Use `dotnet --help` for a full list of commands and options.

---

#dotnet #cli #globaltools #automation #productivity