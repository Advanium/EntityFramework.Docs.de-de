---
title: Verbindungs Verwaltung-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: ecaa5a27-b19e-4bf9-8142-a3fb00642270
ms.openlocfilehash: a6352bbbc38c38bd5f30536736ec969056df2c7d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414866"
---
# <a name="connection-management"></a><span data-ttu-id="4ff22-102">Verbindungsverwaltung</span><span class="sxs-lookup"><span data-stu-id="4ff22-102">Connection management</span></span>
<span data-ttu-id="4ff22-103">Auf dieser Seite wird das Verhalten von Entity Framework in Bezug auf das Übergeben von Verbindungen mit dem Kontext und die Funktionalität der **Database. Connection. Open ()** -API beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4ff22-103">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="4ff22-104">Übergeben von Verbindungen an den Kontext</span><span class="sxs-lookup"><span data-stu-id="4ff22-104">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="4ff22-105">Verhalten für EF5 und frühere Versionen</span><span class="sxs-lookup"><span data-stu-id="4ff22-105">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="4ff22-106">Es gibt zwei Konstruktoren, die Verbindungen akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="4ff22-106">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="4ff22-107">Es ist möglich, diese zu verwenden, aber Sie müssen einige Einschränkungen umgehen:</span><span class="sxs-lookup"><span data-stu-id="4ff22-107">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="4ff22-108">Wenn Sie eine offene Verbindung an eine dieser Optionen übergeben, wird das erste Mal, wenn das Framework versucht, es zu verwenden, eine InvalidOperationException ausgelöst, die besagt, dass eine bereits geöffnete Verbindung nicht erneut geöffnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="4ff22-108">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="4ff22-109">Das contextownsconnection-Flag gibt an, ob die zugrunde liegende Speicher Verbindung verworfen werden soll, wenn der Kontext verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="4ff22-109">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="4ff22-110">Unabhängig von dieser Einstellung wird die Speicher Verbindung jedoch immer geschlossen, wenn der Kontext verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="4ff22-110">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="4ff22-111">Wenn Sie also über mehr als einen dbcontext mit derselben Verbindung verfügen, wenn der Kontext zuerst verworfen wird, wird die Verbindung geschlossen (ähnlich, wenn Sie eine vorhandene ADO.NET-Verbindung mit einem dbcontext gemischt haben, schließt dbcontext die Verbindung immer, wenn Sie verworfen wird). .</span><span class="sxs-lookup"><span data-stu-id="4ff22-111">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="4ff22-112">Es ist möglich, die oben beschriebene Einschränkung zu umgehen, indem Sie eine geschlossene Verbindung übergibt und nur Code ausführt, der Sie nach der Erstellung aller Kontexte öffnet:</span><span class="sxs-lookup"><span data-stu-id="4ff22-112">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

<span data-ttu-id="4ff22-113">Die zweite Einschränkung bedeutet lediglich, dass Sie Ihre dbcontext-Objekte verwerfen müssen, bis Sie bereit sind, die Verbindung zu schließen.</span><span class="sxs-lookup"><span data-stu-id="4ff22-113">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="4ff22-114">Verhalten in EF6 und zukünftigen Versionen</span><span class="sxs-lookup"><span data-stu-id="4ff22-114">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="4ff22-115">In EF6 und zukünftigen Versionen hat dbcontext die gleichen beiden Konstruktoren, erfordert jedoch nicht mehr, dass die an den Konstruktor weiter gegebene Verbindung geschlossen wird, wenn Sie empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="4ff22-115">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="4ff22-116">Dies ist nun möglich:</span><span class="sxs-lookup"><span data-stu-id="4ff22-116">So this is now possible:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

<span data-ttu-id="4ff22-117">Außerdem steuert das contextownsconnection-Flag nun, ob die Verbindung geschlossen und freigegeben wird, wenn der dbcontext verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="4ff22-117">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="4ff22-118">Im obigen Beispiel wird die Verbindung nicht geschlossen, wenn der kontextfrei gegeben wird (Zeile 32), wie es in früheren Versionen von EF der Fall war, sondern wenn die Verbindung selbst verworfen wurde (Zeile 40).</span><span class="sxs-lookup"><span data-stu-id="4ff22-118">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="4ff22-119">Natürlich kann dbcontext weiterhin die Kontrolle über die Verbindung übernehmen (legen Sie einfach "contextownsconnection" auf "true" fest, oder verwenden Sie einen der anderen Konstruktoren), wenn Sie dies wünschen.</span><span class="sxs-lookup"><span data-stu-id="4ff22-119">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="4ff22-120">Bei der Verwendung von Transaktionen mit diesem neuen Modell sind einige weitere Überlegungen zu beachten.</span><span class="sxs-lookup"><span data-stu-id="4ff22-120">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="4ff22-121">Weitere Informationen finden Sie [unter Arbeiten mit Transaktionen](~/ef6/saving/transactions.md).</span><span class="sxs-lookup"><span data-stu-id="4ff22-121">For details see [Working with Transactions](~/ef6/saving/transactions.md).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="4ff22-122">Database. Connection. Open ()</span><span class="sxs-lookup"><span data-stu-id="4ff22-122">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="4ff22-123">Verhalten für EF5 und frühere Versionen</span><span class="sxs-lookup"><span data-stu-id="4ff22-123">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="4ff22-124">In EF5 und früheren Versionen ist ein Fehler aufgetreten, so dass **ObjectContext. Connection. State** nicht aktualisiert wurde, um den tatsächlichen Status der zugrunde liegenden Speicher Verbindung widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="4ff22-124">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="4ff22-125">Wenn Sie z. b. den folgenden Code ausgeführt haben, können Sie den Status " **geschlossen** " zurückgeben, obwohl die zugrunde liegende Speicher Verbindung tatsächlich **geöffnet**ist.</span><span class="sxs-lookup"><span data-stu-id="4ff22-125">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="4ff22-126">Wenn Sie die Datenbankverbindung öffnen, indem Sie Database. Connection. Open () aufrufen, wird Sie separat geöffnet, bis Sie das nächste Mal eine Abfrage ausführen oder etwas aufrufen, das eine Datenbankverbindung erfordert (z. b. SaveChanges ()), aber danach den zugrunde liegenden Speicher. die Verbindung wird geschlossen.</span><span class="sxs-lookup"><span data-stu-id="4ff22-126">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="4ff22-127">Der Kontext wird dann erneut geöffnet, und die Verbindung wird immer wieder geschlossen, wenn ein anderer Daten Bank Vorgang erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="4ff22-127">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="4ff22-128">Verhalten in EF6 und zukünftigen Versionen</span><span class="sxs-lookup"><span data-stu-id="4ff22-128">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="4ff22-129">Bei EF6 und zukünftigen Versionen haben wir den Ansatz herangezogen, wenn der aufrufenden Code die Verbindung durch Aufrufen von Context öffnet. Database. Connection. Open () hat dann einen guten Grund dafür, und das Framework geht davon aus, dass es die Kontrolle über das Öffnen und Schließen der Verbindung wünscht und die Verbindung nicht mehr automatisch schließt.</span><span class="sxs-lookup"><span data-stu-id="4ff22-129">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="4ff22-130">Dies kann potenziell zu Verbindungen führen, die für einen längeren Zeitraum geöffnet sind. verwenden Sie daher mit Bedacht.</span><span class="sxs-lookup"><span data-stu-id="4ff22-130">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="4ff22-131">Wir haben auch den Code so aktualisiert, dass ObjectContext. Connection. State nun den Status der zugrunde liegenden Verbindung korrekt nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="4ff22-131">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
