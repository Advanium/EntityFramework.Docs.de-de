---
title: Verbindungsresilienz und Wiederholungs Logik EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: a01216c3399ca4a04943563435eacd0047337a5f
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306576"
---
# <a name="connection-resiliency-and-retry-logic"></a>Verbindungsresilienz und Wiederholungs Logik
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Anwendungen, die eine Verbindung mit einem Datenbankserver herstellen, waren aufgrund von Back-End-Fehlern und Netzwerk Instabilität immer anfällig für Verbindungsunterbrechungen. In einer LAN-basierten Umgebung, die mit dedizierten Datenbankservern arbeitet, sind diese Fehler jedoch selten genug, dass die zusätzliche Logik zur Behandlung dieser Fehler nicht häufig erforderlich ist. Mit dem Anstieg von cloudbasierten Datenbankservern wie Windows Azure SQL-Datenbank und Verbindungen über weniger zuverlässige Netzwerke kommt es nun häufiger vor, dass Verbindungsunterbrechungen auftreten. Dies liegt möglicherweise an der Abwehr von Techniken, die clouddatenbanken verwenden, um die Fairness des Diensts zu gewährleisten, z. b. die Verbindungs Drosselung oder eine Instabilität im Netzwerk, die vorübergehende Timeouts und andere vorübergehende Fehler verursacht.  

Verbindungsresilienz bezieht sich auf die Fähigkeit von EF, Befehle, die aufgrund dieser Verbindungsunterbrechungen fehlschlagen, automatisch zu wiederholen.  

## <a name="execution-strategies"></a>Ausführungs Strategien  

Die Verbindungs Wiederholung erfolgt durch eine Implementierung der idbexecutionstrategy-Schnittstelle. Implementierungen der idbexecutionstrategy-Strategie sind dafür verantwortlich, einen Vorgang zu akzeptieren und, wenn eine Ausnahme auftritt, festzustellen, ob eine Wiederholung angemessen ist, und den Vorgang zu wiederholen, wenn dies der Fall ist. Es gibt vier Ausführungs Strategien, die mit EF ausgeliefert werden:  

1. **Defaultexecutionstrategy**: Diese Ausführungs Strategie führt keinen Wiederholungsversuch für Vorgänge aus. Dies ist die Standardeinstellung für andere Datenbanken als SQL Server.  
2. **Defaultionqlexecutionstrategy**: Dies ist eine interne Ausführungs Strategie, die standardmäßig verwendet wird. Diese Strategie führt nicht zu einem erneuten Versuch, sondern schließt alle Ausnahmen ein, die vorübergehend sein könnten, um Benutzer darüber zu informieren, dass Sie möglicherweise die verbindungsresilienz aktivieren möchten.  
3. **Dbexecutionstrategy**: Diese Klasse eignet sich als Basisklasse für andere Ausführungs Strategien, einschließlich ihrer eigenen benutzerdefinierten. Es implementiert eine exponentielle Wiederholungs Richtlinie, bei der der anfängliche Wiederholungsversuch mit einer Verzögerung von 0 (null) erfolgt, und die Verzögerung erhöht sich exponentiell, bis die maximale Anzahl der Wiederholungs Versuche übertrifft. Diese Klasse verfügt über eine abstrakte Methode "schuldretryon", die in abgeleiteten Ausführungs Strategien implementiert werden kann, um zu steuern, welche Ausnahmen wiederholt werden sollen.  
4. **Sqlazureexecutionstrategy**: Diese Ausführungs Strategie erbt von dbexecutionstrategy und führt einen Wiederholungsversuch für Ausnahmen aus, die bekanntermaßen bei der Arbeit mit Azure SQL-Datenbank vorübergehend sind.

> [!NOTE]
> Die Ausführungs Strategien 2 und 4 sind im SQL Server-Anbieter enthalten, der im Lieferumfang von EF enthalten ist, das sich in der EntityFramework. SqlServer-Assembly befindet und für die Arbeit mit SQL Server konzipiert ist.  

## <a name="enabling-an-execution-strategy"></a>Aktivieren einer Ausführungs Strategie  

Die einfachste Möglichkeit, EF die Verwendung einer Ausführungs Strategie mitzuteilen, ist die Methode "" der Methode "" der Klasse " [dbconfiguration](~/ef6/fundamentals/configuring/code-based.md) ":  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

Dieser Code weist EF an, beim Herstellen einer Verbindung mit SQL Server sqlazureexecutionstrategy zu verwenden.  

## <a name="configuring-the-execution-strategy"></a>Konfigurieren der Ausführungs Strategie  

Der Konstruktor von sqlazureexecutionstrategy kann zwei Parameter akzeptieren: "maxRetryCount" und "MaxDelay". Die Anzahl der maxretry-Werte ist die maximale Anzahl von Wiederholungs versuchen für die Strategie. MaxDelay ist ein TimeSpan-Wert, der die maximale Verzögerung zwischen Wiederholungs versuchen darstellt, die von der Ausführungs Strategie verwendet werden.  

Führen Sie die folgenden Schritte aus, um die maximale Anzahl von Wiederholungen auf 1 und die maximale Verzögerung auf 30 Sekunden festzulegen:  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

Sqlazureexecutionstrategy wird beim ersten Auftreten eines vorübergehenden Fehlers sofort wiederholt, verzögert sich jedoch zwischen den einzelnen Wiederholungen, bis entweder die maximale Wiederholungs Grenze überschritten wird oder die Gesamtzeit die maximale Verzögerung erreicht.  

Bei den Ausführungs Strategien wird nur eine begrenzte Anzahl von Ausnahmen wiederholt, die normalerweise vorübergehend sind. Sie müssen weiterhin andere Fehler behandeln und die Ausnahme "retrylimitexceging" abfangen, wenn ein Fehler nicht vorübergehend ist oder zu lange für die Auflösung benötigt wird. etabliert.  

Bei der Verwendung einer Wiederholungs Ausführungs Strategie gibt es einige bekannte Einschränkungen:  

## <a name="streaming-queries-are-not-supported"></a>Streaming-Abfragen werden nicht unterstützt.  

Standardmäßig puffert EF6 und höhere Versionen Abfrageergebnisse, anstatt Sie zu streamen. Wenn die Ergebnisse gestreamt werden sollen, können Sie die asstreaming-Methode verwenden, um eine LINQ to Entities Abfrage in das Streaming zu ändern.  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

Streaming wird nicht unterstützt, wenn eine Wiederholungs Ausführungs Strategie registriert wird. Diese Einschränkung ist vorhanden, da die Verbindung die Ergebnisse, die zurückgegeben werden, auf dem Weg ablegen könnte. In diesem Fall muss EF die gesamte Abfrage erneut ausführen, kann jedoch nicht zuverlässig festzustellen, welche Ergebnisse bereits zurückgegeben wurden (die Daten haben sich möglicherweise seit dem Senden der ersten Abfrage geändert, Ergebnisse können in einer anderen Reihenfolge zurückgegeben werden, und die Ergebnisse haben möglicherweise keinen eindeutigen Bezeichner. , usw.).  

## <a name="user-initiated-transactions-are-not-supported"></a>Vom Benutzer initiierte Transaktionen werden nicht unterstützt.  

Wenn Sie eine Ausführungs Strategie konfiguriert haben, die zu Wiederholungen führt, gibt es einige Einschränkungen bei der Verwendung von Transaktionen.  

Standardmäßig führt EF innerhalb einer Transaktion alle Datenbankupdates aus. Sie müssen nichts tun, um dies zu ermöglichen. EF führt dies immer automatisch durch.  

Im folgenden Code wird z. b. "SaveChanges" automatisch innerhalb einer Transaktion ausgeführt. Wenn SaveChanges nach dem Einfügen einer der neuen Standorte fehlschlagen würde, wird für die Transaktion ein Rollback ausgeführt, und es werden keine Änderungen auf die Datenbank angewendet. Außerdem bleibt der Kontext in einem Zustand, in dem SaveChanges erneut aufgerufen werden kann, um die Änderungen zu übernehmen.  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

Wenn keine Wiederholungs Ausführungs Strategie verwendet wird, können Sie mehrere Vorgänge in einer einzelnen Transaktion umschließen. Der folgende Code umschließt z. b. zwei SaveChanges-Aufrufe in einer einzelnen Transaktion. Wenn ein Teil eines der beiden Vorgänge fehlschlägt, wird keine der Änderungen angewendet.  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

Dies wird nicht unterstützt, wenn eine Wiederholungs Ausführungs Strategie verwendet wird, da EF keine früheren Vorgänge kennt und die Wiederholungs Versuche ausgeführt werden. Wenn beispielsweise die zweite SaveChanges fehlgeschlagen ist, verfügt EF nicht mehr über die erforderlichen Informationen, um den ersten SaveChanges-Befehl zu wiederholen.  

### <a name="workaround-suspend-execution-strategy"></a>Problemumgehung: Ausführungs Strategie aussetzen  

Eine mögliche Problem Umgehung besteht darin, die Wiederholungs Ausführungs Strategie für den Code anzuhalten, der eine vom Benutzer initiierte Transaktion verwenden muss. Die einfachste Möglichkeit hierzu ist das Hinzufügen eines suspendexecutionstrategy-Flags zu ihrer Code basierten Konfigurations Klasse und das Ändern des Ausführungs Strategie-Lambda-Ausdrucks, um die standardmäßige (nicht retalisierende) Ausführungs Strategie zurückzugeben, wenn das Flag festgelegt ist.  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;
using System.Runtime.Remoting.Messaging;

namespace Demo
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            this.SetExecutionStrategy("System.Data.SqlClient", () => SuspendExecutionStrategy
              ? (IDbExecutionStrategy)new DefaultExecutionStrategy()
              : new SqlAzureExecutionStrategy());
        }

        public static bool SuspendExecutionStrategy
        {
            get
            {
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy") ?? false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

Beachten Sie, dass wir CallContext verwenden, um den Flagwert zu speichern. Dies bietet eine ähnliche Funktionalität wie der lokale Thread Speicher, kann jedoch mit asynchronem Code verwendet werden, einschließlich asynchroner Abfragen und speichern mit Entity Framework.  

Wir können nun die Ausführungs Strategie für den Code Abschnitt aussetzen, der eine vom Benutzer initiierte Transaktion verwendet.  

``` csharp
using (var db = new BloggingContext())
{
    MyConfiguration.SuspendExecutionStrategy = true;

    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }

    MyConfiguration.SuspendExecutionStrategy = false;
}
```  

### <a name="workaround-manually-call-execution-strategy"></a>Problemumgehung: Manuelles Abrufen der Ausführungs Strategie  

Eine andere Möglichkeit besteht darin, die Ausführungs Strategie manuell zu verwenden und ihr den gesamten Logik Satz zuzuweisen, der ausgeführt werden soll, damit Sie alles wiederholen kann, wenn einer der Vorgänge fehlschlägt. Wir müssen die Ausführungs Strategie mithilfe der oben gezeigten Technik weiterhin aussetzen, damit alle im wiederholbaren Codeblock verwendeten Kontexte nicht versuchen, den Vorgang zu wiederholen.  

Beachten Sie, dass alle Kontexte innerhalb des Codeblocks erstellt werden müssen, um erneut versucht werden zu können. Dadurch wird sichergestellt, dass für jeden Wiederholungsversuch ein sauberer Zustand gestartet wird.  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

MyConfiguration.SuspendExecutionStrategy = true;

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });

MyConfiguration.SuspendExecutionStrategy = false;
```  
