
## 📌 **Introduction**

BenchmarkDotNet is a powerful [[DotNet|.NET]] library for benchmarking and measuring the performance of your code with high precision. It handles warm-up iterations, statistical analysis, and provides rich reporting.

---

## 📦 **Installation**

```bash
dotnet add package BenchmarkDotNet
```

---

## ✅ **Basic Usage**

### 🔄 **Creating a Benchmark Class**

```csharp
using BenchmarkDotNet.Attributes;
using BenchmarkDotNet.Running;

public class MathBenchmarks
{
    [Benchmark]
    public int SumLoop()
    {
        int sum = 0;
        for (int i = 0; i < 1000; i++)
            sum += i;
        return sum;
    }

    [Benchmark]
    public int SumLinq()
    {
        return Enumerable.Range(0, 1000).Sum();
    }
}

class Program
{
    static void Main(string[] args)
    {
        BenchmarkRunner.Run<MathBenchmarks>();
    }
}
```

---

## 🔍 **Benchmark Attributes**

* `[Benchmark]`: Marks a method as a benchmark.
* `[Params(100, 1000)]`: Defines input parameters for benchmarking.
* `[GlobalSetup]`: Runs setup code before benchmarks.
* `[GlobalCleanup]`: Runs cleanup after benchmarks.

#### 🔹 Example with Parameters:

```csharp
public class ParameterizedBenchmarks
{
    [Params(100, 1000, 10000)]
    public int N;

    [Benchmark]
    public int SumRange() => Enumerable.Range(0, N).Sum();
}
```

---

## 📊 **Customizing Benchmark Configurations**

```csharp
using BenchmarkDotNet.Configs;
using BenchmarkDotNet.Jobs;

public class CustomConfig : ManualConfig
{
    public CustomConfig()
    {
        AddJob(Job.Default.WithIterationCount(10));
    }
}

BenchmarkRunner.Run<MathBenchmarks>(new CustomConfig());
```

---

## 📖 **Exporting Results**

BenchmarkDotNet automatically generates:

* `.md` (Markdown)
* `.html` (HTML Report)
* `.csv` (Raw Data)

Files are placed in the `BenchmarkDotNet.Artifacts` folder.

#### 🔹 **Specify Output Folder:**

```csharp
[Config(typeof(Config))]
public class ExportBenchmarks
{
    public class Config : ManualConfig
    {
        public Config() => AddExporter(BenchmarkDotNet.Exporters.MarkdownExporter.Default);
    }
}
```

---

## 📓 **Best Practices**

* ✅ Run benchmarks on **Release mode**.
* ✅ Disable **Debugger** and ensure **JIT optimizations** are enabled.
* ✅ Avoid running on a highly loaded system.
* ✅ Isolate benchmarks from unrelated background processes.
* ✅ Always use `[Params]` to benchmark different data sets.

---

# 📅 **Changelog**

* **v1.0:** Initial version with basic usage, attributes, and exporting reports.

---

#dotnet #benchmarking #performance #benchmarkdotnet #profiling`
