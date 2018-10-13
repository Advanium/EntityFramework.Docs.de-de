---
title: Migrationen – EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 5ae06a4342a556936dc44c5bf6622814eaad4733
ms.sourcegitcommit: 7a7da65404c9338e1e3df42576a13be536a6f95f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2018
ms.locfileid: "48834746"
---
<a name="migrations"></a>Migrationen
==========

Ein Datenmodell ändert sich im Lauf der Entwicklung und ist nicht mehr synchron mit der Datenbank. Sie können die Datenbank verwerfen und EF eine neue erstellen lassen, die dem Modell entspricht, aber dieses Vorgehen führt zum Verlust von Daten. Das Migrations-Feature in EF Core bietet einen Weg, das Datenbankschema inkrementell zu aktualisieren, um es mit dem Datenmodell der Anwendung synchron zu halten und zugleich die vorhandenen Daten in der Datenbank beizubehalten.

Migrations enthält Befehlszeilentools und APIs, die Sie bei den folgenden Aufgaben unterstützen:

* [Erstellen einer Migration](#create-a-migration). Generieren Sie Code, mit dem die Datenbank aktualisiert werden kann, um sie mit einer Sammlung von Modelländerungen synchron zu halten.
* [Aktualisieren der Datenbank](#update-the-database). Wenden Sie ausstehende Migrationen an, um das Datenbankschema zu aktualisieren.
* [Anpassen des Migrationscodes](#customize-migration-code). Manchmal muss der generierte Code geändert oder ergänzt werden.
* [Entfernen einer Migration](#remove-a-migration). Löschen Sie den generierten Code.
* [Rückgängigmachen einer Migration](#revert-a-migration). Machen Sie die Datenbankänderungen rückgängig.
* [Generieren von SQL-Skripts](#generate-sql-scripts). Für das Aktualisieren einer Produktionsdatenbank oder zur Problembehandlung von Migrationscode benötigen Sie möglicherweise ein Skript.
* [Anwenden von Migrationen zur Laufzeit](#apply-migrations-at-runtime). Wenn Aktualisierungen zur Entwurfszeit und das Ausführen von Skripts nicht die besten Optionen sind, rufen Sie die Methode `Migrate()` auf.

<a name="install-the-tools"></a>Installieren der Tools
-----------------

Installieren Sie die [Befehlszeilentools](xref:core/miscellaneous/cli/index):
* Für Visual Studio empfehlen wir die [Paket-Manager-Konsolentools](xref:core/miscellaneous/cli/powershell).
* Wählen Sie für andere Entwicklungsumgebungen die [.NET Core-CLI-Tools aus](xref:core/miscellaneous/cli/dotnet).

<a name="create-a-migration"></a>Erstellen einer Migration
------------------

Nach dem [Definieren des Ausgangsmodells](xref:core/modeling/index) ist der Zeitpunkt gekommen, die Datenbank zu erstellen. Um eine anfängliche Migration hinzuzufügen, führen Sie den folgenden Befehl aus:

``` powershell
Add-Migration InitialCreate
```
``` Console
dotnet ef migrations add InitialCreate
```

Drei Dateien werden Ihrem Projekt im Verzeichnis **Migrationen** hinzugefügt:

* **00000000000000_InitialCreate.cs** – Die wichtigste Migrationsdatei. Enthält die Vorgänge, die zum Durchführen der Migration (in `Up()`) und Rückgängigmachen (in `Down()`) erforderlich sind.
* **00000000000000_InitialCreate.Designer.cs** – Die Metadatendatei für die Migration. Enthält Informationen, die vom EF verwendet werden.
* **MyContextModelSnapshot.cs** – Eine Momentaufnahme des aktuellen Modells. Diese wird verwendet, um festzustellen, was sich beim Hinzufügen der nächsten Migration geändert hat.

Mit dem Zeitstempel im Dateinamen können Migrationen chronologisch angeordnet werden, sodass Sie den Fortschritt der Änderungen mitverfolgen können.

> [!TIP]
> Es steht Ihnen frei, Migrationsdateien zu verschieben und ihren Namespace zu ändern. Neue Migrationen werden als gleichgeordnete Elemente der letzten Migration erstellt.

<a name="update-the-database"></a>Aktualisieren der Datenbank
-------------------

Als Nächstes führen Sie für die Datenbank die Migration durch, um das Schema zu erstellen.

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="customize-migration-code"></a>Anpassen des Migrationscodes
------------------------

Nachdem Sie Änderungen an Ihrem EF Core-Modell vorgenommen haben, ist das Datenbankschema möglicherweise nicht mehr synchron. Fügen Sie eine andere Migration hinzu, um es auf dem neuesten Stand zu bringen. Der Migrationsname kann wie eine Commitnachricht in einem Versionskontrollsystem verwendet werden. Beispielsweise können Sie einen Namen wie *AddProductReviews* wählen, wenn die Änderung eine neue Entitätsklasse für Rezensionen ist.

``` powershell
Add-Migration AddProductReviews
```
``` Console
dotnet ef migrations add AddProductReviews
```

Nachdem das Gerüst für die Migration erstellt (Code für sie generiert) wurde, prüfen Sie den Code auf Genauigkeit und fügen Sie alle für seine korrekte Anwendung erforderlichen Änderungen hinzu, bzw. entfernen oder bearbeiten Sie sie.

Beispielsweise kann eine Migration die folgenden Vorgänge beinhalten:

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

Durch diese Vorgänge wird zwar Kompatibilität mit dem Datenbankschema hergestellt, die bestehenden Kundennamen werden jedoch nicht gespeichert. Um dies zu korrigieren, schreiben Sie es wie folgt neu:

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

> [!TIP]
> Der Einrüstvorgang der Migration warnt, wenn bei einem Vorgang Datenverlust eintreten kann (etwa beim Verwerfen einer Spalte). Wenn diese Warnung angezeigt wird, überprüfen Sie den Migrationscode besonders gründlich auf Genauigkeit.

Führen Sie für die Datenbank die Migration mit dem entsprechenden Befehl durch.

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

### <a name="empty-migrations"></a>Leere Migrationen

Manchmal ist es sinnvoll, eine Migration hinzuzufügen, ohne Modelländerungen vorzunehmen. In diesem Fall werden durch das Hinzufügen einer neuen Migration Codedateien mit leeren Klassen erstellt. Sie können diese Migration anpassen, um Vorgänge durchzuführen, die sich nicht direkt auf das EF Core-Modell beziehen. Einige Dinge, die Sie vielleicht auf diese Weise handhaben wollen, sind Folgende:

* Volltextsuche
* Funktionen
* Gespeicherte Prozeduren
* Trigger
* Ansichten

<a name="remove-a-migration"></a>Entfernen einer Migration
------------------
Es kann vorkommen, dass Sie eine Migration hinzufügen und feststellen, dass Sie zusätzliche Änderungen an Ihrem EF Core-Modell vornehmen müssen, bevor Sie diese durchführen. Um die letzte Migration zu entfernen, verwenden Sie diesen Befehl.

``` powershell
Remove-Migration
```
``` Console
dotnet ef migrations remove
```

Nachdem Sie die Migration entfernt haben, können Sie die zusätzlichen Modelländerungen vornehmen und sie erneut hinzufügen.

<a name="revert-a-migration"></a>Rückgängigmachen einer Migration
------------------
Wenn Sie bereits eine Migration (oder mehrere Migrationen) für die Datenbank durchgeführt haben, diese jedoch rückgängig machen müssen, können Sie mit demselben Befehl Migrationen durchführen, aber den Namen der Migration angeben, für die Sie ein Rollback ausführen möchten.

``` powershell
Update-Database LastGoodMigration
```
``` Console
dotnet ef database update LastGoodMigration
```

<a name="generate-sql-scripts"></a>Generieren von SQL-Skripts
--------------------
Wenn Sie Ihre Migrationen debuggen oder in einer Produktionsdatenbank bereitstellen, ist es sinnvoll, ein SQL-Skript zu generieren. Das Skript kann dann weiter auf Genauigkeit überprüft und auf die Anforderungen einer Produktionsdatenbank abgestimmt werden. Das Skript kann auch in Verbindung mit einer Bereitstellungstechnologie verwendet werden. Der grundlegende Befehl lautet wie folgt:

``` powershell
Script-Migration
```
``` Console
dotnet ef migrations script
```

Es gibt mehrere Optionen für diesen Befehl.

Die **from**-Migration sollte die letzte Migration sein, die vor der Skriptausführung für die Datenbank durchgeführt wurde. Wenn keine Migrationen durchgeführt wurden, geben Sie `0` an (dies ist die Standardeinstellung).

Die **to**-Migration ist die letzte Migration, die nach der Skriptausführung für die Datenbank durchgeführt wurde. Dies ist standardmäßig die letzte Migration in Ihrem Projekt.

Optional kann ein **idempotent**-Skript generiert werden. Dieses Skript wendet nur Migrationen an, wenn sie nicht bereits für die Datenbank durchgeführt wurden. Dies ist hilfreich, wenn Sie nicht genau wissen, welche Migration für die Datenbank zuletzt durchgeführt wurde oder wenn Bereitstellungen in mehreren Datenbanken, die sich jeweils in unterschiedlichen Migrationen befinden, erfolgen.

<a name="apply-migrations-at-runtime"></a>Anwenden von Migrationen zur Laufzeit
---------------------------
Einige Apps sollten beim Start oder bei der ersten Ausführung Migrationen zur Runtime durchführen. Dies ist mit der Methode `Migrate()` möglich.

Diese Methode basiert auf dem `IMigrator`-Dienst, der für erweiterte Szenarien verwendet werden kann. Verwenden Sie `DbContext.GetService<IMigrator>()`, um darauf zugreifen.

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
> * Diese Vorgehensweise ist nicht für alle geeignet. Diese eignet sich zwar ideal für Apps mit einer lokalen Datenbank, doch die meisten Anwendungen erfordern zuverlässigere Bereitstellungsstrategien wie etwa durch das Generieren von SQL-Skripts.
> * Rufen Sie `EnsureCreated()` nicht vor `Migrate()` auf. `EnsureCreated()` umgeht Migrationen zum Erstellen von Schemas, was dazu führt, dass `Migrate()` fehlerhaft ist.

<a name="next-steps"></a>Nächste Schritte
----------

Weitere Informationen finden Sie unter <xref:core/miscellaneous/cli/index>.