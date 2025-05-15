
This guide demonstrates how to **validate Entity Framework (EF) Core entity mappings against the actual database schema**. This is particularly useful in scenarios where:

- You want to ensure EF Core mappings correctly reflect the underlying database structure.
- You’re working with legacy databases or shared schemas where discrepancies may occur.
- You’re performing automated regression tests to detect unintended schema changes.

In this example, we use a **fictional entity called `Book`**, representing a simple table in a relational database. The validation covers:

- ✅ Column Name Mapping
- ✅ Column Type Mapping (with normalization to handle cases like `varchar(255)` vs `varchar`)
- ✅ Nullability Checks

The test uses **[[xUnit Manual|xUnit]]** for test execution, **[[Fluent Assertions Manual|FluentAssertions]]** for expressive validations, and **MySQL** as the database provider. Adjust the context and connection to match your actual database environment.


```csharp
using System;
using System.Text.RegularExpressions;
using FluentAssertions;
using Microsoft.EntityFrameworkCore;
using MySql.Data.MySqlClient;
using Xunit;

public class Book
{
    public int Id { get; set; }                  // Should map to 'id' (bigint, not nullable)
    public string? Title { get; set; }            // Should map to 'title' (varchar(255), nullable)
    public string? Author { get; set; }           // Should map to 'author' (varchar(255), nullable)
    public bool IsPublished { get; set; }         // Should map to 'isPublished' (tinyint(1), not nullable)
}

[Theory]
[InlineData(nameof(Book.Id), "id", "bigint(20)", false)]
[InlineData(nameof(Book.Title), "title", "varchar(255)", true)]
[InlineData(nameof(Book.Author), "author", "varchar(255)", true)]
[InlineData(nameof(Book.IsPublished), "isPublished", "tinyint(1)", false)]
public void Book_Entity_Should_Have_Correct_Column_Mapping_Type_And_Nullability(
    string propertyName,
    string expectedColumnName,
    string expectedColumnType,
    bool isNullable)
{
    var context = /* Get your DbContext instance here */;
    var connection = /* Get your MySqlConnection instance here */;
    const string tableName = "book";

    var entityType = context.Model.FindEntityType(typeof(Book));
    var property = entityType?.FindProperty(propertyName);

    var efColumnName = property?.GetColumnName(StoreObjectIdentifier.Table(tableName, null));
    var efColumnType = property?.GetColumnType();
    var efIsNullable = property?.IsNullable;

    string assertionContext =
        $"[Property: {propertyName}, Expected Column: {expectedColumnName}, Expected Type: {expectedColumnType}, Expected Nullable: {isNullable}]";

    string NormalizeType(string columnType) =>
        Regex.Replace(columnType ?? string.Empty, @"\(\d+\)", string.Empty).Trim().ToLowerInvariant();

    // EF Core Metadata Assertions
    efColumnName.Should().Be(expectedColumnName, $"EF column name mismatch. {assertionContext}");
    NormalizeType(efColumnType).Should().Be(NormalizeType(expectedColumnType),
        $"EF column type mismatch. {assertionContext}");
    efIsNullable.Should().Be(isNullable, $"EF nullability mismatch. {assertionContext}");

    // Validate Against Real Database
    var query = @"
        SELECT COLUMN_NAME, COLUMN_TYPE, IS_NULLABLE 
        FROM INFORMATION_SCHEMA.COLUMNS 
        WHERE TABLE_SCHEMA = DATABASE() 
          AND TABLE_NAME = @TableName 
          AND COLUMN_NAME = @ColumnName;";

    using var command = new MySqlCommand(query, connection);
    command.Parameters.AddWithValue("@TableName", tableName);
    command.Parameters.AddWithValue("@ColumnName", expectedColumnName);

    using var reader = command.ExecuteReader();

    reader.Read().Should().BeTrue(
        $"Column '{expectedColumnName}' should exist in the database. {assertionContext}");

    var dbColumnType = reader.GetString("COLUMN_TYPE");
    var dbIsNullable = reader.GetString("IS_NULLABLE").Equals("YES", StringComparison.OrdinalIgnoreCase);

    NormalizeType(dbColumnType).Should().Be(NormalizeType(expectedColumnType),
        $"Database column type mismatch. {assertionContext}");
    dbIsNullable.Should().Be(isNullable, $"Database nullability mismatch. {assertionContext}");
}

```

---
#dotnet #xunit #fluentassertions #entityframework #mysql