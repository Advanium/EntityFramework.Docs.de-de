---
title: 'Erstellen und Löschen-APIs: EF Core'
description: APIs zum Erstellen und Löschen von Datenbanken mit Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 25e7352269531e881e83e44ea90108f12d4dcbea
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619232"
---
# <a name="create-and-drop-apis"></a>Erstellen und Löschen-APIs

Die `EnsureCreated`- und `EnsureDeleted`-Methoden stellen eine leichte Alternative zu [Migrationen](xref:core/managing-schemas/migrations/index) zum Verwalten des Datenbankschemas dar. Diese Methoden sind in Szenarios nützlich, in denen die Daten vorübergehend sind und gelöscht werden können, wenn sich das Schema ändert. Beispielsweise während der Prototyperstellung in Tests oder für lokale Caches.

Einige Anbieter (insbesondere nicht relationale) unterstützen keine Migrationen. Für diese Anbieter ist `EnsureCreated` oft die einfachste Möglichkeit, das Datenbankschema zu initialisieren.

> [!WARNING]
> `EnsureCreated` und Migrationen funktionieren nicht gut zusammen. Wenn Sie Migrationen verwenden, verwenden Sie `EnsureCreated` nicht, um das Schema zu initialisieren.

Der Übergang von `EnsureCreated` in Migrationen ist nicht nahtlos. Die einfachste Möglichkeit hierzu besteht darin, die Datenbank zu löschen und Sie mithilfe von Migrationen neu zu erstellen. Wenn Sie die Verwendung von Migrationen in der Zukunft erwarten, empfiehlt es sich, einfach mit Migrationen zu beginnen, anstatt `EnsureCreated` zu verwenden.

## <a name="ensuredeleted"></a>EnsureDeleted

Die `EnsureDeleted`-Methode löscht die Datenbank, wenn Sie vorhanden ist. Wenn Sie nicht über die entsprechenden Berechtigungen verfügen, wird eine Ausnahme ausgelöst.

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

`EnsureCreated` erstellt die Datenbank, wenn Sie nicht vorhanden ist, und initialisiert das Datenbankschema. Wenn Tabellen vorhanden sind (einschließlich Tabellen für eine andere `DbContext`-Klasse), wird das Schema nicht initialisiert.

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> Asynchrone Versionen dieser Methoden sind ebenfalls verfügbar.

## <a name="sql-script"></a>SQL-Skript

Um das von `EnsureCreated` verwendete SQL zu erhalten, können Sie die `GenerateCreateScript`-Methode verwenden.

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>Mehrere `DbContext`-Klassen

`EnsureCreated` funktioniert nur, wenn keine Tabellen in der Datenbank vorhanden sind. Bei Bedarf können Sie eine eigene Überprüfung schreiben, um festzustellen, ob das Schema initialisiert werden muss, und den zugrunde liegenden `IRelationalDatabaseCreator`-Dienst verwenden, um das Schema zu initialisieren.

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
