---
title: Verwenden eines separaten Migrations Projekts-EF Core
description: Verwenden eines separaten Migrationsprojekts zum Verwalten von Datenbankschemas mit Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: a3f0ed96c6a8e3e8629d9a4bb1610fcbfe6ca043
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617918"
---
# <a name="using-a-separate-migrations-project"></a>Verwenden eines separaten Migrations Projekts

Möglicherweise möchten Sie Ihre Migrationen in einer anderen Assembly als der mit Ihrem speichern `DbContext` . Sie können diese Strategie auch verwenden, um mehrere Sätze von Migrationen beizubehalten, z. b. eine für die Entwicklung und eine andere für releaseaktualisierungen.

Aufgabe

1. Erstellen Sie eine neue Klassenbibliothek.

2. Fügen Sie einen Verweis auf die dbcontext-Assembly hinzu.

3. Verschieben Sie die Migrationen und die Modell Momentaufnahme-Dateien in die Klassenbibliothek.
   > [!TIP]
   > Wenn keine Migrationen vorhanden sind, generieren Sie eine im Projekt, das den dbcontext enthält, und verschieben Sie Sie dann.
   > Dies ist wichtig, denn wenn die Migrationsassembly keine vorhandene Migration enthält, kann der Befehl "Add-Migration" den dbcontext nicht finden.

4. Konfigurieren Sie die Migrations Assembly:

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. Fügen Sie einen Verweis auf ihre Migrationen-Assembly aus der Startassembly hinzu.
   * Wenn dies eine zirkuläre Abhängigkeit verursacht, aktualisieren Sie den Ausgabepfad der Klassenbibliothek:

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

Wenn Sie alles ordnungsgemäß durchgeführt haben, sollten Sie dem Projekt neue Migrationen hinzufügen können.

## <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
