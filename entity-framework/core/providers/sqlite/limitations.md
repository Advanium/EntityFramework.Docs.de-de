---
title: SQLite-Datenbankanbieter-Einschränkungen-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 2f80dc195265787318ac4925dd937da45ffad011
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414758"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>Einschränkungen für SQLite EF Core-Datenbankanbieter

Der SQLite-Anbieter hat eine Reihe von Migrations Einschränkungen. Die meisten dieser Einschränkungen sind das Ergebnis von Einschränkungen in der zugrunde liegenden SQLite-Datenbank-Engine und nicht spezifisch für EF.

## <a name="modeling-limitations"></a>Modellierungs Einschränkungen

Die gemeinsame relationale Bibliothek (die von Entity Framework relationalen Datenbankanbietern gemeinsam verwendet wird) definiert APIs zum Modellieren von Konzepten, die für die meisten relationalen Daten Bank Engines Einige dieser Konzepte werden vom SQLite-Anbieter nicht unterstützt.

* Schemas
* Sequenzen
* Berechnete Spalten

## <a name="query-limitations"></a>Abfrage Einschränkungen

SQLite unterstützt die folgenden Datentypen nicht nativ. EF Core können Werte dieser Typen lesen und schreiben, und die Abfrage auf Gleichheit (`where e.Property == value`) wird ebenfalls unterstützt. Für andere Vorgänge, wie z. b. Vergleich und Reihenfolge, ist jedoch eine Auswertung auf dem Client erforderlich.

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

Anstelle `DateTimeOffset`empfiehlt es sich, DateTime-Werte zu verwenden. Wenn Sie mehrere Zeitzonen verarbeiten, empfiehlt es sich, die Werte vor dem Speichern in die UTC zu umrechnen und dann wieder in die entsprechende Zeitzone umzuwandeln.

Der `Decimal`-Typ bietet ein hohes Maß an Genauigkeit. Wenn Sie diese Genauigkeits Stufe nicht benötigen, empfiehlt sich stattdessen die Verwendung von Double. Sie können einen [Wert Konverter](../../modeling/value-conversions.md) verwenden, um in ihren Klassen weiterhin Decimal zu verwenden.

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>Migrations Einschränkungen

Die SQLite-Datenbank-Engine unterstützt keine Reihe von Schema Vorgängen, die von den meisten anderen relationalen Datenbanken unterstützt werden. Wenn Sie versuchen, einen der nicht unterstützten Vorgänge auf eine SQLite-Datenbank anzuwenden, wird eine `NotSupportedException` ausgelöst.

| Vorgang            | Unterstützt? | Erfordert Version |
|:---------------------|:-----------|:-----------------|
| AddColumn            | ✔          | 1.0              |
| AddForeignKey        | ✗          |                  |
| AddPrimaryKey        | ✗          |                  |
| AddUniqueConstraint  | ✗          |                  |
| AlterColumn          | ✗          |                  |
| CreateIndex          | ✔          | 1.0              |
| CreateTable          | ✔          | 1.0              |
| DropColumn           | ✗          |                  |
| DropForeignKey       | ✗          |                  |
| DropIndex            | ✔          | 1.0              |
| DropPrimaryKey       | ✗          |                  |
| DropTable            | ✔          | 1.0              |
| DropUniqueConstraint | ✗          |                  |
| Renamecolumn         | ✔          | 2.2.2            |
| Renameingedex          | ✔          | 2.1              |
| RenameTable          | ✔          | 1.0              |
| Ensureschema         | ✔ (No-OP)  | 2.0              |
| DropSchema           | ✔ (No-OP)  | 2.0              |
| Einfügen               | ✔          | 2.0              |
| Aktualisieren               | ✔          | 2.0              |
| Löschen               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>Problem Umgehung der Migrations Einschränkungen

Sie können einige dieser Einschränkungen umgehen, indem Sie Code in ihren Migrationen manuell schreiben, um eine Tabellen Neuerstellung durchzuführen. Eine Tabellenneuerstellung umfasst Umbenennen der vorhandenen Tabelle, Erstellen einer neuen Tabelle, Kopieren von Daten in die neue Tabelle und Löschen der alten Tabelle. Sie müssen die `Sql(string)`-Methode verwenden, um einige dieser Schritte auszuführen.

Weitere Informationen finden Sie unter [vornehmen anderer Arten von Tabellen Schema Änderungen](https://sqlite.org/lang_altertable.html#otheralter) in der SQLite-Dokumentation.

In Zukunft kann EF einige dieser Vorgänge unterstützen, indem er den Ansatz für die Tabellen Neuerstellung unter den Decken verwendet. Sie können [Diese Funktion in unserem GitHub-Projekt nachverfolgen](https://github.com/aspnet/EntityFrameworkCore/issues/329).
