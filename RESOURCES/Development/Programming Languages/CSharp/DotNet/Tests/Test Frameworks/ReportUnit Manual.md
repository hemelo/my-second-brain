## 📌 **Introduction**

ReportUnit is a report generator for unit test runners over [[DotNet|.NET]]. It takes test result files ([[xUnit Manual|xUnit]], NUnit, MSTest) and produces beautiful, interactive HTML reports. It is framework-agnostic and works with most CI/CD pipelines.

---

## 📦 **Installation**

```bash
# Using Chocolatey
choco install reportunit

# Using NuGet (if needed in projects)
dotnet add package ReportUnit
```

---

## ✅ **Basic Usage**

### 🔄 **Generate Report from Command Line**

```bash
reportunit [input-folder|input-file] [output-folder]
```

#### 🔹 **Example:**

```bash
reportunit ./TestResults ./Reports
```

This will scan the `TestResults` folder for supported test result files and generate reports into the `Reports` folder.

### 🔹 **Supported Input Formats**

- [[xUnit Manual|xUnit]] (`.xml`)
    
- NUnit (`TestResult.xml`)
    
- MSTest (`.trx`)
    

---

## 🔍 **Using in CI/CD Pipelines**

### 📲 **Azure DevOps Example**

```yaml
- task: PowerShell@2
  inputs:
    targetType: 'inline'
    script: |
      reportunit ./TestResults ./Reports
```

### 📱 **GitHub Actions Example**

```yaml
- name: Generate Test Report
  run: reportunit ./TestResults ./Reports
```

---

## 🔄 **Report Structure**

- ✅ Overview Dashboard
    
- ✅ Pass/Fail Statistics
    
- ✅ Test Execution Time Charts
    
- ✅ Test Case Details with Stack Traces
    

Reports are interactive and can be opened directly in a browser by navigating to the `index.html` file in the output directory.

---

## 📓 **Best Practices**

- ✅ Clean old reports before generating new ones.
    
- ✅ Store reports in versioned artifact folders in CI/CD.
    
- ✅ Link the report generation step after running tests in the pipeline.
    
- ✅ Always archive reports for traceability.
    

---

# 📅 **Changelog**

- **v1.0:** Initial version with CLI usage and CI/CD integration examples.
    

---

#dotnet #reportunit #testing #ci #reports`