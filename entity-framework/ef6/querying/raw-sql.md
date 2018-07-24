---
title: Unformatierte SQL-Abfragen – EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
caps.latest.revision: 3
ms.openlocfilehash: 1d968604cfa500784c4699b0923512572a06d773
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121132"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="262d8-102">Unformatierte SQL-Abfragen</span><span class="sxs-lookup"><span data-stu-id="262d8-102">Raw SQL Queries</span></span>
<span data-ttu-id="262d8-103">Entitätsframework können Sie Abfragen mithilfe von LINQ mit Ihrer Entitätsklassen.</span><span class="sxs-lookup"><span data-stu-id="262d8-103">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="262d8-104">Allerdings gibt es möglicherweise Fälle, in denen Sie Abfragen mithilfe roher SQL direkt in der Datenbank ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="262d8-104">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="262d8-105">Dazu gehört das Aufrufen von gespeicherter Prozeduren, die für die Code First-Modelle hilfreich sein können, die derzeit keine Zuordnung zu gespeicherten Prozeduren unterstützen.</span><span class="sxs-lookup"><span data-stu-id="262d8-105">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="262d8-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="262d8-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="262d8-107">Schreiben von SQL-Abfragen für Entitäten</span><span class="sxs-lookup"><span data-stu-id="262d8-107">Writing SQL queries for entities</span></span>  

<span data-ttu-id="262d8-108">Die SqlQuery-Methode auf "DbSet" ermöglicht eine unformatierte SQL-Abfrage geschrieben werden, die Instanzen der Entität zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="262d8-108">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="262d8-109">Die zurückgegebenen Objekte werden vom Kontext nachverfolgt werden, wie dies der Fall wäre, wenn sie von einer LINQ-Abfrage zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="262d8-109">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="262d8-110">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="262d8-110">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="262d8-111">Beachten Sie, dass genau wie LINQ-Abfragen, die Abfrage nicht ausgeführt wird, bis die Ergebnisse aufgelistet werden, in dem Beispiel oben ist dies durch den Aufruf von "ToList".</span><span class="sxs-lookup"><span data-stu-id="262d8-111">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="262d8-112">Vorsichtig sollte vorgenommen werden, wenn unformatierte SQL-Abfragen aus zwei Gründen geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="262d8-112">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="262d8-113">Zunächst sollte die Abfrage geschrieben werden, um sicherzustellen, dass nur Entitäten zurückgegeben, die tatsächlich vom angeforderten Typ sind.</span><span class="sxs-lookup"><span data-stu-id="262d8-113">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="262d8-114">Beispielsweise ist bei Verwendung von Features wie z. B. Vererbung es einfach eine Abfrage schreiben, die Entitäten erstellen, die von den falschen CLR-Typ sind.</span><span class="sxs-lookup"><span data-stu-id="262d8-114">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="262d8-115">Zweitens machen einige Arten von unformatierte SQL-Abfrage potenziellen Sicherheitsrisiken, insbesondere SQL Injection-Angriffe.</span><span class="sxs-lookup"><span data-stu-id="262d8-115">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="262d8-116">Stellen Sie sicher, dass Sie Parameter in der Abfrage in die richtige Methode, die gegen solche Angriffe verwenden.</span><span class="sxs-lookup"><span data-stu-id="262d8-116">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="262d8-117">Laden von Entitäten von gespeicherten Prozeduren</span><span class="sxs-lookup"><span data-stu-id="262d8-117">Loading entities from stored procedures</span></span>  

<span data-ttu-id="262d8-118">Sie können die DbSet.SqlQuery verwenden, um Entitäten aus den Ergebnissen einer gespeicherten Prozedur zu laden.</span><span class="sxs-lookup"><span data-stu-id="262d8-118">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="262d8-119">Der folgende Code ruft z. B. das "Dbo". GetBlogs Prozedur in der Datenbank:</span><span class="sxs-lookup"><span data-stu-id="262d8-119">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="262d8-120">Sie können auch Parameter an eine gespeicherte Prozedur, die mit der folgenden Syntax übergeben:</span><span class="sxs-lookup"><span data-stu-id="262d8-120">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="262d8-121">Schreiben von SQL-Abfragen für nicht-Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="262d8-121">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="262d8-122">Eine SQL-Abfrage, die Instanzen eines beliebigen Typs, einschließlich primitiver Typen zurückzugeben, kann mithilfe der SqlQuery-Methode für die Datenbank-Klasse erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="262d8-122">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="262d8-123">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="262d8-123">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="262d8-124">Aus SqlQuery für Datenbank zurückgegebenen Ergebnisse werden nie vom Kontext nachverfolgt werden, auch wenn die Objekte Instanzen eines Entitätstyps sind.</span><span class="sxs-lookup"><span data-stu-id="262d8-124">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="262d8-125">Raw-Befehle an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="262d8-125">Sending raw commands to the database</span></span>  

<span data-ttu-id="262d8-126">Nicht-Abfragebefehle können mit der Datenbank mithilfe der ExecuteSqlCommand-Methode für die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="262d8-126">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="262d8-127">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="262d8-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="262d8-128">Beachten Sie, dass alle Änderungen an Daten in der Datenbank mithilfe von ExecuteSqlCommand für den Kontext nicht transparent bis Entitäten geladen oder aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="262d8-128">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="262d8-129">Ausgabeparameter</span><span class="sxs-lookup"><span data-stu-id="262d8-129">Output Parameters</span></span>  

<span data-ttu-id="262d8-130">Wenn Output-Parameter verwendet werden, werden ihre Werte nicht verfügbar, bis die Ergebnisse vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="262d8-130">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="262d8-131">Dies liegt an der zugrunde liegende Verhalten von DbDataReader, finden Sie unter [Abrufen von Daten mit einem DataReader](http://go.microsoft.com/fwlink/?LinkID=398589) Weitere Details.</span><span class="sxs-lookup"><span data-stu-id="262d8-131">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](http://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  