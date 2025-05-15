## 📌 **Introduction**

NUKE is a modern, strongly-typed [[DotNet|.NET]] build automation framework. It replaces XML-based build scripts (like MSBuild) and task runners (like Cake and FAKE) with clean, maintainable C# code.

---

## 📦 **Installation**

```bash
dotnet tool install Nuke.GlobalTool --global

# Initialize NUKE in your project directory
nuke :setup
```

---

## ✅ **Basic Project Structure**

```
├── build
│   └── Build.cs
├── .nuke
├── global.json
└── source
```

---

## 📖 **Build.cs Example**

```csharp
using Nuke.Common;
using Nuke.Common.ProjectModel;
using Nuke.Common.Tools.DotNet;
using static Nuke.Common.Tools.DotNet.DotNetTasks;

class Build : NukeBuild
{
    [Solution] readonly Solution Solution;

    Target Clean => _ => _
        .Executes(() =>
        {
            Console.WriteLine("Cleaning artifacts...");
            DotNetClean(s => s.SetProject(Solution));
        });

    Target Restore => _ => _
        .DependsOn(Clean)
        .Executes(() =>
        {
            DotNetRestore(s => s.SetProjectFile(Solution));
        });

    Target Compile => _ => _
        .DependsOn(Restore)
        .Executes(() =>
        {
            DotNetBuild(s => s
                .SetProjectFile(Solution)
                .SetConfiguration(Configuration.Release)
                .EnableNoRestore());
        });

    public static int Main() => Execute<Build>(x => x.Compile);
}
```

---

## 🔧 **Common Targets**

- **Clean**: Remove build artifacts.
    
- **Restore**: Restore NuGet dependencies.
    
- **Compile**: Build the solution.
    
- **Test**: Run unit and integration tests.
    
- **Pack**: Create NuGet packages.
    
- **Publish**: Publish to artifact repositories or cloud platforms.
    

---

## 📂 **Running Targets**

```bash
nuke Compile
nuke Clean Restore Compile
```

---

## 📋 **Parameters and Configurations**

```csharp
[Parameter("Build Configuration (Default is 'Debug')")]
readonly Configuration Configuration = Configuration.Debug;
```

Run with custom configuration:

```bash
nuke Compile --configuration Release
```

---

## 🚀 **CI/CD Integration**

NUKE can generate CI configurations automatically:

```bash
nuke :ci
```

Supported CI Systems:

- GitHub Actions
    
- Azure DevOps
    
- GitLab CI
    
- TeamCity
    
- Jenkins
    

---

# 📚 **Advanced Topics**

## 📦 **Versioning and Release Management**

Proper versioning and release management is critical for traceability and maintaining clean deployment pipelines. NUKE supports both static and automated versioning strategies.

### 📖 **Manual Versioning Parameter**

```csharp
[Parameter("Version of the build. Example: 1.2.3")]
readonly string Version = "1.0.0";

Target Pack => _ => _
    .DependsOn(Compile)
    .Executes(() =>
    {
        DotNetPack(s => s
            .SetProject(Solution)
            .SetConfiguration(Configuration.Release)
            .SetVersion(Version)
            .SetOutputDirectory("./artifacts"));
    });
```

Run with a specific version:

```bash
nuke Pack --version 1.2.3
```

### 📖 **Git-Based Versioning (Using GitVersion)**

Automatically derive semantic versions from Git history.

1. Install GitVersion:
    

```bash
dotnet tool install --global GitVersion.Tool
```

2. Integrate in Build.cs:
    

```csharp
Target CalculateVersion => _ => _
    .Executes(() =>
    {
        var result = ProcessTasks.StartProcess("gitversion", "/output json")
                                 .EnsureOnlyStd()
                                 .Select(x => x.Text)
                                 .ToList();

        var json = string.Join("", result);
        var versionInfo = System.Text.Json.JsonSerializer.Deserialize<Dictionary<string, string>>(json);
        var version = versionInfo["NuGetVersionV2"];

        Console.WriteLine($"Calculated Version: {version}");
        Environment.SetEnvironmentVariable("BUILD_VERSION", version);
    });

Target Pack => _ => _
    .DependsOn(Compile, CalculateVersion)
    .Executes(() =>
    {
        var version = Environment.GetEnvironmentVariable("BUILD_VERSION") ?? "1.0.0";

        DotNetPack(s => s
            .SetProject(Solution)
            .SetConfiguration(Configuration.Release)
            .SetVersion(version)
            .SetOutputDirectory("./artifacts"));
    });
```

> 📌 **Tip:** Combine versioning with your Changelog and Notifications to fully automate release notes and communication.

---
## 📦 **NuGet Package Publishing**

Publishing your NuGet packages can be fully automated within NUKE. This ensures version consistency and simplifies deployment.

### 📖 **Prerequisites**

- Ensure the `NUGET_API_KEY` is stored securely as an environment variable in your CI/CD system or local machine.
    
- Packages should be generated using the `DotNetPack` target.
    

### 📖 **NuGet Publishing Target**

```csharp
Target PublishNuGet => _ => _
    .DependsOn(Pack)
    .Executes(() =>
    {
        var apiKey = Environment.GetEnvironmentVariable("NUGET_API_KEY");
        if (string.IsNullOrEmpty(apiKey))
        {
            throw new Exception("NUGET_API_KEY environment variable is not set.");
        }

        GlobFiles("./artifacts", "*.nupkg")
            .ForEach(package =>
            {
                DotNetNuGetPush(s => s
                    .SetTargetPath(package)
                    .SetSource("https://api.nuget.org/v3/index.json")
                    .SetApiKey(apiKey));

                Console.WriteLine($"Published {package} to NuGet.");
            });
    });
```

### 📖 **Running the Publish Target**

```bash
nuke PublishNuGet
```

### 📚 **Best Practices for NuGet Publishing**

- ✅ Automate the publish step only after successful builds and tests.
    
- ✅ Use Git tags and versioning to keep package versions consistent.
    
- ✅ Always store the API key securely and avoid hardcoding sensitive values.
    
- ✅ Validate the `.nupkg` artifacts before pushing to production feeds.
    

---
## 📚 **Documentation Generation**

Automate the generation of project documentation as part of your build pipeline. This ensures that API documentation and developer guides are always up-to-date.

### 📖 **Using DocFX**

DocFX is a popular tool for generating static documentation from .NET projects and XML comments.

#### 📦 **Prerequisites**

- Install DocFX globally:
    

```
dotnet tool install -g docfx
```

- Ensure your project outputs XML documentation files:
    

```
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
</PropertyGroup>
```

#### 📖 **NUKE Target for Doc Generation**

```
Target GenerateDocs => _ => _
    .Executes(() =>
    {
        var docfxPath = ToolPathResolver.GetPathExecutable("docfx");
        if (string.IsNullOrEmpty(docfxPath))
        {
            throw new Exception("DocFX is not installed. Install it using 'dotnet tool install -g docfx'.");
        }

        ProcessTasks.StartProcess(docfxPath, "docfx.json", workingDirectory: RootDirectory)
            .EnsureZeroExitCode();

        Console.WriteLine("Documentation generated successfully.");
    });
```

#### 📖 **Running the Docs Target**

```
nuke GenerateDocs
```

### 📚 **Best Practices for Documentation**

- ✅ Always generate docs as part of the release pipeline.
    
- ✅ Store generated documentation in versioned directories.
    
- ✅ Optionally publish docs to GitHub Pages, Azure Blob Storage, or internal portals.
    
- ✅ Include API XML comments and markdown files for richer documentation.

---
## 📚 **SonarScanner Integration**

Integrate SonarScanner with NUKE to automatically analyze code quality and generate detailed reports as part of your build pipeline.

### 📖 **Prerequisites**

- Install SonarScanner globally:
    

```bash
dotnet tool install --global dotnet-sonarscanner
```

- Ensure the following environment variables are set:
    
    - `SONAR_TOKEN`: Your SonarQube authentication token.
        
    - `SONAR_PROJECT_KEY`: The unique project key in SonarQube.
        
    - `SONAR_HOST_URL`: The SonarQube server URL.
        

### 📖 **NUKE Target for SonarScanner**

```csharp
Target SonarScan => _ => _
    .Executes(() =>
    {
        var sonarToken = Environment.GetEnvironmentVariable("SONAR_TOKEN");
        var projectKey = Environment.GetEnvironmentVariable("SONAR_PROJECT_KEY");
        var hostUrl = Environment.GetEnvironmentVariable("SONAR_HOST_URL");

        if (string.IsNullOrEmpty(sonarToken) || string.IsNullOrEmpty(projectKey) || string.IsNullOrEmpty(hostUrl))
        {
            throw new Exception("SonarScanner environment variables are not properly configured.");
        }

        DotNetTasks.DotNet($"sonarscanner begin /k:{projectKey} /d:sonar.host.url={hostUrl} /d:sonar.login={sonarToken}");

        DotNetTasks.DotNetBuild(s => s
            .SetProjectFile(Solution)
            .SetConfiguration(Configuration.Release));

        DotNetTasks.DotNet("sonarscanner end /d:sonar.login=" + sonarToken);

        Console.WriteLine("SonarQube analysis completed.");
    });
```

### 📖 **Running the SonarScan Target**

```bash
nuke SonarScan
```

### 📚 **Best Practices for SonarScanner**

- ✅ Run code analysis before packaging and publishing artifacts.
    
- ✅ Fail builds if code quality gates are not met.
    
- ✅ Store SonarQube tokens securely in CI/CD secret managers.
    
- ✅ Integrate with branch and PR analysis to maintain code quality.
    

---
## 🧪 **Automated Testing**

Integrate automated testing directly into your NUKE pipeline to ensure that builds only succeed when all tests pass successfully.

### 📖 **Basic Test Target**

```csharp
Target RunTests => _ => _
    .DependsOn(Compile)
    .Executes(() =>
    {
        DotNetTasks.DotNetTest(s => s
            .SetProjectFile(Solution)
            .SetConfiguration(Configuration.Release)
            .EnableNoRestore()
            .EnableNoBuild()
            .SetLogger("trx")
            .SetResultsDirectory("./TestResults"));

        Console.WriteLine("Tests executed successfully.");
    });
```

### 📖 **Collecting Test Coverage Reports**

Using `coverlet` for test coverage:

```bash
dotnet add package coverlet.collector
```

Update the test target to collect coverage:

```csharp
Target RunTestsWithCoverage => _ => _
    .DependsOn(Compile)
    .Executes(() =>
    {
        DotNetTasks.DotNetTest(s => s
            .SetProjectFile(Solution)
            .SetConfiguration(Configuration.Release)
            .EnableNoRestore()
            .EnableNoBuild()
            .SetLogger("trx")
            .SetResultsDirectory("./TestResults")
            .SetCollect("XPlat Code Coverage"));

        Console.WriteLine("Tests with coverage executed successfully.");
    });
```

### 📖 **Generating Coverage Reports with ReportGenerator**

```bash
dotnet tool install -g dotnet-reportgenerator-globaltool
```

Add a target to generate HTML reports:

```csharp
Target GenerateCoverageReport => _ => _
    .DependsOn(RunTestsWithCoverage)
    .Executes(() =>
    {
        var reportGenerator = ToolPathResolver.GetPathExecutable("reportgenerator");

        ProcessTasks.StartProcess(reportGenerator, 
            "-reports:./TestResults/**/*.xml -targetdir:./CoverageReport -reporttypes:Html").EnsureZeroExitCode();

        Console.WriteLine("Coverage report generated at ./CoverageReport/index.html");
    });
```

### 📚 **Best Practices for Testing**

- ✅ Always run tests before packaging or publishing artifacts.
    
- ✅ Collect and publish test coverage reports as part of your pipeline.
    
- ✅ Store test results in a dedicated directory for easy access in CI/CD reports.
    
- ✅ Combine coverage checks with code quality gates in SonarQube.
    

###### 📖 [[Test Frameworks|How to test?]]

---
## 📅 **Dynamic Changelogs Generation**

Generate changelogs directly from Git commit history:

```csharp
Target Changelog => _ => _
    .Executes(() =>
    {
        var changelog = ProcessTasks.StartProcess("git", "log --pretty=format:'- %s (%h)'")
                                    .EnsureOnlyStd()
                                    .Select(x => x.Text)
                                    .ToList();

        File.WriteAllLines("./artifacts/CHANGELOG.md", changelog);
    });
```

---

## 📣 **Integrating Slack/Teams Notifications**

### 📖 **Slack Notification**

```csharp
Target NotifySlack => _ => _
    .Executes(() =>
    {
        var webhookUrl = Environment.GetEnvironmentVariable("SLACK_WEBHOOK_URL");
        var status = Build.Failure == null ? "Success" : "Failure";
        var emoji = status == "Success" ? ":white_check_mark:" : ":x:";
        var message = $"{{ \"text\": \"Build {status}! {emoji}\" }}";

        HttpTasks.HttpPost(x => x
            .SetUrl(webhookUrl)
            .SetContent(message)
            .SetContentType("application/json"));
    });
```

### 📖 **Teams Notification**

```csharp
Target NotifyTeams => _ => _
    .Executes(() =>
    {
        var webhookUrl = Environment.GetEnvironmentVariable("TEAMS_WEBHOOK_URL");
        var status = Build.Failure == null ? "Success" : "Failure";
        var message = $"{{ \"text\": \"Build {status}!\" }}";

        HttpTasks.HttpPost(x => x
            .SetUrl(webhookUrl)
            .SetContent(message)
            .SetContentType("application/json"));
    });
```

Integrate these targets at the end of your pipeline:

```csharp
Target CompletePipeline => _ => _
    .DependsOn(Publish, Changelog)
    .Triggers(NotifySlack, NotifyTeams);
```

---

# 📅 **Changelog**

- **v1.0:** Initial version with setup, basic build targets, advanced topics, CI integration, dynamic changelogs, and notifications.
    

---

#dotnet #nuke #buildautomation #ci #devops #automation #versioning #secrets #notifications #changelog