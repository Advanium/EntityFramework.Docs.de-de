---
title: "Neue Features in EF Core 2.1 – EF Core"
author: divega
ms.author: divega
ms.date: 2/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: bb1e691e0f22bd36467d58c02bde22c63067207e
ms.sourcegitcommit: fcaeaf085171dfb5c080ec42df1d1df8dfe204fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/15/2018
---
# <a name="new-features-in-ef-core-21"></a>Neue Features in EF Core 2.1
> [!NOTE]  
> Dieses Release befindet sich weiterhin in der Vorschauversion.

Neben zahlreichen geringfügigen Verbesserungen und über 100 Fehlerbehebungen weist EF Core 2.1 mehrere neue Features auf:

## <a name="lazy-loading"></a>Lazy Loading
EF Core enthält nun die erforderlichen Bausteine für Benutzer, die Entitätsklassen zum bedarfsgesteuerten Laden ihrer Navigationseigenschaften erstellen möchten. Wir haben zudem das neue Paket „Microsoft.EntityFrameworkCore.Proxies“ erstellt, das mithilfe dieser Bausteine Proxyklassen zum Lazy Loading auf Grundlage minimal geänderter Entitätsklassen (z.B. Klassen mit virtuellen Navigationseigenschaften) erzeugt.

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Lazy Loading](xref:core/querying/related-data#lazy-loading).

## <a name="parameters-in-entity-constructors"></a>Parameter in Entitätskonstruktoren
Als einer der erforderlichen Bausteine zum Lazy Loading haben wir die Erstellung von Entitäten ermöglicht, die Parameter in ihren Konstruktoren akzeptieren. Sie können mithilfe von Parametern Eigenschaftswerte, Delegaten zum Lazy Loading und Dienste einfügen.

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Entitätskonstruktor mit Parametern](xref:core/modeling/constructors).

## <a name="value-conversions"></a>Wertkonvertierungen
Bislang konnte EF Core nur Eigenschaften von Typen zuordnen, die vom zugrunde liegenden Datenbankanbieter nativ unterstützt wurden. Werte wurden zwischen Spalten und Eigenschaften ohne jegliche Transformation hin und her kopiert. Ab EF Core 2.1 können Wertkonvertierungen angewendet werden, um die aus Spalten abgerufenen Werte vor der Anwendung auf Eigenschaften zu transformieren (und umgekehrt). Wir verfügen über eine Reihe von Konvertierungen, die der Konvention entsprechend bei Bedarf angewendet werden können, sowie über eine explizite Konfigurations-API, die die Registrierung von benutzerdefinierten Konvertierungen zwischen Spalten und Eigenschaften ermöglicht. Einige Anwendungsgebiete für dieses Feature sind z.B. Folgende:

- Speichern von Enumerationen als Zeichenfolgen
- Zuordnen von ganzen Zahlen ohne Vorzeichen mit SQL Server
- Automatische Ver- und Entschlüsselung von Eigenschaftswerten

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu Wertkonvertierungen](xref:core/modeling/value-conversions).  

## <a name="linq-groupby-translation"></a>LINQ-GroupBy-Übersetzung
Vor Version 2.1 wurde der GroupBy-LINQ-Operator in EF Core immer in der In-Memory Database ausgewertet. Nun bieten wir in den meisten Fällen Unterstützung für dessen Übersetzung in die GROUP BY-SQL-Klausel.

Dieses Beispiel zeigt eine Abfrage mit GroupBy zum Berechnen verschiedener Aggregatfunktionen:

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => Amount)
        });
```

Die entsprechende SQL-Übersetzung sieht wie folgt aus:

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a>Datenseeding
Mit dem neuen Release ist es möglich, die ursprünglichen Daten zum Auffüllen einer Datenbank anzugeben. Im Gegensatz zu EF 6 wird das Datenseeding mit einem Entitätstyp als Teil der Modellkonfiguration verknüpft. EF Core-Migrationen können dann automatisch berechnen, welche Einfüge-, Aktualisierungs- oder Löschvorgänge beim Durchführen eines Upgrades für die Datenbank auf eine neue Modellversion angewendet werden müssen.

Beispielsweise können Sie damit Seeddaten für ein Post-Element in `OnModelCreating` konfigurieren:

``` csharp
modelBuilder.Entity<Post>().SeedData(new Post{ Id = 1, Text = "Hello World!" });
```

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Datenseeding](xref:core/modeling/data-seeding).  

## <a name="query-types"></a>Abfragetypen
Ein EF Core-Modell kann nun Abfragetypen einschließen. Im Gegensatz zu Entitätstypen werden für Abfragetypen keine Schlüssel definiert, und sie können nicht eingefügt, gelöscht oder aktualisiert werden (d.h., sie sind schreibgeschützt). Sie können jedoch direkt von Abfragen zurückgegeben werden. Einige Verwendungsszenarien für Abfragetypen sind z.B. Folgende:

- Zuordnen mit Ansichten ohne Primärschlüssel
- Zuordnen mit Tabellen ohne Primärschlüssel
- Zuordnen mit im Modell definierten Abfragen
- Verwendung als Rückgabetyp für `FromSql()`-Abfragen

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu Abfragetypen](xref:core/modeling/query-types).

## <a name="include-for-derived-types"></a>Einschließen für abgeleitete Typen
Nun ist es möglich, beim Schreiben von Ausdrücken für die `Include`-Methode nur für abgeleitete Typen definierte Navigationseigenschaften anzugeben. Für die stark typisierte `Include`-Version unterstützen wir entweder die Verwendung einer expliziten Umwandlung oder des `as`-Operators. Nun bieten wir auch Unterstützung für den Verweis der für abgeleitete Typen definierten Namen der Navigationseigenschaften in der Zeichenfolgenversion `Include`:

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Einschließen mit abgeleiteten Typen](xref:core/querying/related-data#include-on-derived-types).

## <a name="systemtransactions-support"></a>System.Transactions-Unterstützung
Wir haben die Möglichkeit zum Arbeiten mit System.Transactions-Features wie TransactionScope hinzugefügt. Dieses funktioniert in .NET Framework und .NET Core bei der Verwendung von Datenbankanbietern, die Unterstützung hierfür bieten.

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu System.Transactions](xref:core/saving/transactions#using-systemtransactions).

## <a name="better-column-ordering-in-initial-migration"></a>Bessere Spaltensortierung bei der anfänglichen Migration
Basierend auf dem Feedback von Kunden haben wir Migrationen dahingehend aktualisiert, dass Spalten für Tabellen zunächst in der gleichen Reihenfolge wie die Deklaration von Eigenschaften in Klassen generiert werden. Beachten Sie, dass EF Core nicht die Reihenfolge ändern kann, wenn nach der Erstellung der ersten Tabelle neue Member hinzugefügt werden.

## <a name="optimization-of-correlated-subqueries"></a>Optimierung von korrelierten Unterabfragen
Wir haben unsere Abfrageübersetzung dahingehend verbessert, dass die Ausführung von „N+1“-SQL-Abfragen in vielen gängigen Szenarien verhindert wird, in denen die Verwendung einer Navigationseigenschaft in der Projektion zum Verknüpfen von Daten aus der Stammabfrage mit Daten aus einer korrelierten Unterabfrage führt. Für die Optimierung ist das Puffern der Ergebnisse aus der Unterabfrage erforderlich. Zudem fordern wir Sie dazu auf, die Abfrage zum Auswählen des neuen Verhaltens zu ändern.

Die folgende Abfrage wird z.B. normalerweise in eine Abfrage für Kunden plus N separate Abfragen für Aufträge übersetzt („N“ steht hierbei für die Anzahl der zurückgegebenen Kunden):

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

Durch Einschließen von `ToList()` an der richtigen Stelle geben Sie an, dass eine Pufferung für die Aufträge, die die Optimierung ermöglichen, angemessen ist:

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

Beachten Sie, dass diese Abfrage nur in zwei SQL-Abfragen übersetzt wird: eine für Kunden und die nächste für Aufträge.

## <a name="ownedattribute"></a>OwnedAttribute

Nun können [Entitätstypen im Besitz](xref:core/modeling/owned-entities) konfiguriert werden, indem einfach der Typ mit `[Owned]` kommentiert und dann sichergestellt wird, dass die Besitzerentität zum Modell hinzugefügt wird:

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="database-provider-compatibility"></a>Kompatibilität mit Datenbankanbietern

EF Core 2.1 wurde zur Bereitstellung von Kompatibilität mit Datenbankanbietern für EF Core 2.0 entwickelt. Während einige der oben beschriebenen Features (z.B. Wertkonvertierungen) einen aktualisierten Anbieter erfordern, sind andere Features (z.B. das Lazy Loading) mit den vorhandenen Anbietern kompatibel.

> [!TIP]
> Falls unerwarteterweise keine Kompatibilität besteht oder Probleme mit den neuen Features auftreten, oder falls Sie uns Ihre Feedback mitteilen möchten, melden Sie uns dies über [unsere Problemverfolgung](https://github.com/aspnet/EntityFrameworkCore/issues/new).
