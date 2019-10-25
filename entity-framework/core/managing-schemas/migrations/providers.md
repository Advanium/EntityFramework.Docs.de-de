---
title: Migrationen mit mehreren Anbietern-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: c9b1a2563ef548e592374f90a6242b0bd851bc98
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811956"
---
# <a name="migrations-with-multiple-providers"></a>Migrationen mit mehreren Anbietern

Mit den [EF Core Tools][1] werden nur die Migrationen für den aktiven Anbieter Gerüstbau. Manchmal möchten Sie jedoch möglicherweise mehr als einen Anbieter (z. b. Microsoft SQL Server und SQLite) mit dbcontext verwenden. Es gibt zwei Möglichkeiten, dies mit Migrationen zu behandeln. Sie können zwei Sätze von Migrationen verwalten: eine für jeden Anbieter, oder Sie können Sie in einem einzelnen Satz zusammenführen, der für beides geeignet ist.

## <a name="two-migration-sets"></a>Zwei Migrations Sätze

Beim ersten Ansatz generieren Sie zwei Migrationen für jede Modell Änderung.

Eine Möglichkeit besteht darin, jeden Migrations Satz [in einer separaten Assembly][2] zu platzieren und den aktiven Anbieter (und die Migrationsassembly) manuell zwischen dem Hinzufügen der beiden Migrationen zu wechseln.

Ein weiterer Ansatz, der die Arbeit mit den Tools vereinfacht, besteht darin, einen neuen Typ zu erstellen, der von dbcontext abgeleitet ist und den aktiven Anbieter überschreibt. Dieser Typ wird zur Entwurfszeit beim Hinzufügen oder Anwenden von Migrationen verwendet.

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> Da für jeden Migrations Satz seine eigenen dbcontext-Typen verwendet werden, ist für diese Vorgehensweise die Verwendung einer separaten Migrationsassembly nicht erforderlich.

Beim Hinzufügen einer neuen Migration geben Sie die Kontext Typen an.

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

``` Console
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

> [!TIP]
> Sie müssen das Ausgabeverzeichnis für nachfolgende Migrationen nicht angeben, da diese als gleich geordnete Elemente der letzten erstellt werden.

## <a name="one-migration-set"></a>Ein Migrations Satz

Wenn Sie nicht möchten, dass zwei Migrations Sätze vorhanden sind, können Sie diese manuell in einem einzelnen Satz kombinieren, der auf beide Anbieter angewendet werden kann.

Anmerkungen können gleichzeitig vorhanden sein, da ein Anbieter alle Anmerkungen ignoriert, die er nicht versteht. Beispielsweise könnte eine Primärschlüssel Spalte, die sowohl mit Microsoft SQL Server als auch mit SQLite funktioniert, wie folgt aussehen.

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

Wenn Vorgänge nur auf einen Anbieter angewendet werden können (oder zwischen den Anbietern unterschiedlich sind), verwenden Sie die `ActiveProvider`-Eigenschaft, um festzustellen, welcher Anbieter aktiv ist.

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
