---
title: Komplexe Abfrageoperatoren – EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 2e187a2a-4072-4198-9040-aaad68e424fd
uid: core/querying/complex-query-operators
ms.openlocfilehash: 350a7fa6a3ee1de16bad4b63e10842f9356a1b60
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72186233"
---
# <a name="complex-query-operators"></a><span data-ttu-id="2a78a-102">Komplexe Abfrageoperatoren</span><span class="sxs-lookup"><span data-stu-id="2a78a-102">Complex Query Operators</span></span>

<span data-ttu-id="2a78a-103">LINQ (Language Integrated Query) enthält viele komplexe Operatoren, die mehrere Datenquellen kombinieren oder komplexe Verarbeitungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="2a78a-103">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="2a78a-104">Nicht für alle LINQ-Operatoren gibt es geeignete Übersetzungen auf der Serverseite.</span><span class="sxs-lookup"><span data-stu-id="2a78a-104">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="2a78a-105">Manchmal wird eine Abfrage in einem Format für den Server übersetzt, aber in einem anderen Format nicht, obwohl das Ergebnis gleich ist.</span><span class="sxs-lookup"><span data-stu-id="2a78a-105">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="2a78a-106">Auf dieser Seite werden einige der komplexen Operatoren und ihre unterstützten Variationen beschrieben.</span><span class="sxs-lookup"><span data-stu-id="2a78a-106">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="2a78a-107">Möglicherweise werden in zukünftigen Releases mehr Muster erkannt und deren entsprechenden Übersetzungen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-107">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="2a78a-108">Es ist auch wichtig, zu beachten, dass die Übersetzungsunterstützung je nach Anbieter variiert.</span><span class="sxs-lookup"><span data-stu-id="2a78a-108">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="2a78a-109">Eine bestimmte Abfrage, die für SQL Server übersetzt wird, funktioniert möglicherweise nicht für SQLite-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="2a78a-109">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="2a78a-110">Das in diesem Artikel verwendete [Beispiel](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="2a78a-110">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="2a78a-111">Join</span><span class="sxs-lookup"><span data-stu-id="2a78a-111">Join</span></span>

<span data-ttu-id="2a78a-112">Mit dem LINQ-Join-Operator können Sie zwei Datenquellen anhand des Schlüsselselektors für die jeweilige Quelle verbinden und ein Tupel von Werten erstellen, wenn der Schlüssel übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-112">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="2a78a-113">Dies wird für relationale Datenbanken natürlich in `INNER JOIN` übersetzt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-113">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="2a78a-114">Während der LINQ-Join-Operator äußere und innere Schlüsselselektoren aufweist, erfordert die Datenbank eine einzelne Verknüpfungsbedingung.</span><span class="sxs-lookup"><span data-stu-id="2a78a-114">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="2a78a-115">Daher generiert EF Core eine Verknüpfungsbedingung mithilfe eines Vergleichs der Gleichheit des äußeren und inneren Schlüsselselektors.</span><span class="sxs-lookup"><span data-stu-id="2a78a-115">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span> <span data-ttu-id="2a78a-116">Des Weiteren generiert EF Core eine Verknüpfungsbedingung, um die Gleichheit der Komponenten zu vergleichen, wenn die Schlüsselselektoren einen anonymen Typ aufweisen.</span><span class="sxs-lookup"><span data-stu-id="2a78a-116">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a><span data-ttu-id="2a78a-117">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="2a78a-117">GroupJoin</span></span>

<span data-ttu-id="2a78a-118">Mit dem LINQ-GroupJoin-Operator können Sie zwei Datenquellen ähnlich wie mit dem Join-Operator verbinden, jedoch wird dabei eine Gruppe innerer Werte zum Vergleichen äußerer Elemente erstellt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-118">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="2a78a-119">Das Ausführen einer Abfrage ähnlich dem folgenden Beispiel führt zum Ergebnis: `Blog` & `IEnumerable<Post>`.</span><span class="sxs-lookup"><span data-stu-id="2a78a-119">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="2a78a-120">Da Datenbanken (insbesondere relationale Datenbanken) keine Möglichkeit zum Darstellen einer Sammlung von clientseitigen Objekten aufweisen, können GroupJoin-Operatoren in vielen Fällen nicht auf den Server übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="2a78a-120">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="2a78a-121">Hierzu ist erforderlich, dass Sie alle Daten für GroupJoin ohne einen speziellen Selektor vom Server abrufen (die erste Abfrage unten).</span><span class="sxs-lookup"><span data-stu-id="2a78a-121">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="2a78a-122">Wenn der Selektor jedoch die Auswahl von Daten einschränkt und dann alle Daten vom Server abruft, können Leistungsprobleme auftreten (die zweite Abfrage unten).</span><span class="sxs-lookup"><span data-stu-id="2a78a-122">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="2a78a-123">Daher übersetzt EF Core GroupJoin nicht.</span><span class="sxs-lookup"><span data-stu-id="2a78a-123">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="2a78a-124">SelectMany</span><span class="sxs-lookup"><span data-stu-id="2a78a-124">SelectMany</span></span>

<span data-ttu-id="2a78a-125">Mit dem LINQ-SelectMany-Operator können Sie einen Sammlungsselektor für äußere Elemente aufzählen und Tupel von Werten aus einzelnen Datenquellen generieren.</span><span class="sxs-lookup"><span data-stu-id="2a78a-125">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="2a78a-126">Auf gewisse Zeit ist dies ein Join, der jedoch keine Bedingungen aufweist, sodass alle äußeren Elemente mit einem Element aus der Sammlungsquelle verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="2a78a-126">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="2a78a-127">Je nachdem, wie der Sammlungsselektor im Bezug mit der äußeren Datenquelle steht, kann SelectMany in verschiedene Abfragen auf der Serverseite übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="2a78a-127">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="2a78a-128">Sammlungsselektor referenziert nicht die äußere Quelle</span><span class="sxs-lookup"><span data-stu-id="2a78a-128">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="2a78a-129">Wenn der Sammlungsselektor nichts von der äußeren Quelle referenziert, ist das Ergebnis ein kartesisches Produkt beider Datenquellen.</span><span class="sxs-lookup"><span data-stu-id="2a78a-129">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="2a78a-130">Dies wird für relationale Datenbanken in `CROSS JOIN` übersetzt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-130">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="2a78a-131">Sammlungsselektor referenziert die äußere Datenquelle in einer WHERE-Klausel</span><span class="sxs-lookup"><span data-stu-id="2a78a-131">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="2a78a-132">Wenn der Sammlungsselektor eine WHERE-Klausel enthält, die das äußere Element referenziert, dann übersetzt EF Core sie in einen Join der Datenbank und verwendet das Prädikat als Joinbedingung.</span><span class="sxs-lookup"><span data-stu-id="2a78a-132">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="2a78a-133">Normalerweise tritt dieser Fall auf, wenn die Sammlungsnavigation im äußeren Element als Sammlungsselektor verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2a78a-133">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="2a78a-134">Wenn die Sammlung für ein äußeres Element leer ist, werden keine Ergebnisse für dieses generiert.</span><span class="sxs-lookup"><span data-stu-id="2a78a-134">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="2a78a-135">Wenn `DefaultIfEmpty` jedoch auf den Sammlungsselektor angewendet wird, wird das äußere Element mit einem Standardwert des inneren Elements verknüpft.</span><span class="sxs-lookup"><span data-stu-id="2a78a-135">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="2a78a-136">Aufgrund dieser Unterscheidung wird diese Art von Abfrage bei Abwesenheit von `DefaultIfEmpty` in `INNER JOIN` übersetzt und in `LEFT JOIN`, wenn `DefaultIfEmpty` angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="2a78a-136">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="2a78a-137">Sammlungsselektor referenziert die äußere Datenquelle in einem Fall ohne WHERE-Klausel</span><span class="sxs-lookup"><span data-stu-id="2a78a-137">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="2a78a-138">Wenn der Sammlungsselektor das äußere Element referenziert, das sich nicht in einer WHERE-Klausel befindet (wie im obigen Fall), wird es nicht in einen Join der Datenbank übersetzt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-138">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="2a78a-139">Daher muss der Sammlungsselektor für alle äußeren Elemente ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="2a78a-139">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="2a78a-140">Für viele relationale Datenbanken wird dies in `APPLY`-Vorgänge übersetzt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-140">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="2a78a-141">Wenn die Sammlung für ein äußeres Element leer ist, werden keine Ergebnisse für dieses generiert.</span><span class="sxs-lookup"><span data-stu-id="2a78a-141">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="2a78a-142">Wenn `DefaultIfEmpty` jedoch auf den Sammlungsselektor angewendet wird, wird das äußere Element mit einem Standardwert des inneren Elements verknüpft.</span><span class="sxs-lookup"><span data-stu-id="2a78a-142">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="2a78a-143">Aufgrund dieser Unterscheidung wird diese Art von Abfrage bei Abwesenheit von `DefaultIfEmpty` in `CROSS APPLY` übersetzt und in `OUTER APPLY`, wenn `DefaultIfEmpty` angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="2a78a-143">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="2a78a-144">Bestimmte Datenbanken wie SQLite unterstützen `APPLY`-Operatoren nicht. Daher wird diese Art von Abfrage möglicherweise nicht übersetzt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-144">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="2a78a-145">GroupBy</span><span class="sxs-lookup"><span data-stu-id="2a78a-145">GroupBy</span></span>

<span data-ttu-id="2a78a-146">LINQ-GroupBy-Operatoren erstellen ein Ergebnis vom Typ `IGrouping<TKey, TElement>`, wobei `TKey` und `TElement` einen beliebigen arbiträren Typ aufweisen können.</span><span class="sxs-lookup"><span data-stu-id="2a78a-146">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="2a78a-147">Außerdem wird `IEnumerable<TElement>` von `IGrouping` implementiert, d. h. Sie können es nach der Gruppierung mithilfe eines beliebigen LINQ-Operators zusammensetzen.</span><span class="sxs-lookup"><span data-stu-id="2a78a-147">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="2a78a-148">Da keine Datenbankstruktur eine `IGrouping`-Schnittstelle darstellen kann, gibt es in den meisten Fällen keine Übersetzung für GroupBy-Operatoren.</span><span class="sxs-lookup"><span data-stu-id="2a78a-148">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="2a78a-149">Wenn ein Aggregate-Operator auf alle Gruppen angewendet wird, die einen Skalarwert zurückgeben, kann dieser für relationale Datenbanken in die SQL-Anweisung `GROUP BY` übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="2a78a-149">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="2a78a-150">Die SQL-Anweisung `GROUP BY` ist ebenfalls einschränkend.</span><span class="sxs-lookup"><span data-stu-id="2a78a-150">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="2a78a-151">Sie erfordert, dass Sie nur nach Skalarwerten gruppieren.</span><span class="sxs-lookup"><span data-stu-id="2a78a-151">It requires you to group only by scalar values.</span></span> <span data-ttu-id="2a78a-152">Die Projektion kann nur gruppierende Schlüsselspalten oder auf eine Spalte angewendete Aggregate-Operatoren enthalten.</span><span class="sxs-lookup"><span data-stu-id="2a78a-152">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="2a78a-153">EF Core identifiziert dieses Muster und übersetzt es wie im folgenden Beispiel für den Server:</span><span class="sxs-lookup"><span data-stu-id="2a78a-153">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="2a78a-154">EF Core übersetzt Abfragen auch, wenn ein Aggregate-Operator für die Gruppierung in einem WHERE- oder OrderBy-LINQ-Operator (oder in anderen Order-Operatoren) vorliegt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-154">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="2a78a-155">Für die WHERE-Klausel wird in SQL eine `HAVING`-Klausel verwendet.</span><span class="sxs-lookup"><span data-stu-id="2a78a-155">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="2a78a-156">Der Teil der Abfrage, bevor der GroupBy-Operator angewendet wird, kann einer beliebigen komplexen Abfrage entsprechen, solange diese für den Server übersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="2a78a-156">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="2a78a-157">Sobald Sie Aggregate-Operatoren in einer Gruppierungsabfrage anwenden, um Gruppierungen aus der resultierenden Quelle zu entfernen, können Sie sie außerdem wie jede andere Abfrage zusammensetzen.</span><span class="sxs-lookup"><span data-stu-id="2a78a-157">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="2a78a-158">Die von EF Core unterstützten Aggregate-Operatoren lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="2a78a-158">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="2a78a-159">Average</span><span class="sxs-lookup"><span data-stu-id="2a78a-159">Average</span></span>
- <span data-ttu-id="2a78a-160">Anzahl</span><span class="sxs-lookup"><span data-stu-id="2a78a-160">Count</span></span>
- <span data-ttu-id="2a78a-161">LongCount</span><span class="sxs-lookup"><span data-stu-id="2a78a-161">LongCount</span></span>
- <span data-ttu-id="2a78a-162">Max.</span><span class="sxs-lookup"><span data-stu-id="2a78a-162">Max</span></span>
- <span data-ttu-id="2a78a-163">Min.</span><span class="sxs-lookup"><span data-stu-id="2a78a-163">Min</span></span>
- <span data-ttu-id="2a78a-164">Summe</span><span class="sxs-lookup"><span data-stu-id="2a78a-164">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="2a78a-165">Left Join</span><span class="sxs-lookup"><span data-stu-id="2a78a-165">Left Join</span></span>

<span data-ttu-id="2a78a-166">„Left Join“ ist zwar kein LINQ-Operator, jedoch verfügen relationale Datenbanken über das Konzept von „Left Join“, das häufig in Abfragen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2a78a-166">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="2a78a-167">Ein bestimmtes Muster in LINQ-Abfragen führt zu demselben Ergebnis wie ein `LEFT JOIN` auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="2a78a-167">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="2a78a-168">EF Core identifiziert solche Muster und generiert den entsprechenden `LEFT JOIN` auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="2a78a-168">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="2a78a-169">Das Muster umfasst die Erstellung eines GroupJoin-Operators auf beiden Datenquellen sowie die anschließende Vereinfachung der Gruppierung mithilfe des SelectMany-Operators mit DefaultIfEmpty für die Gruppierungsquelle, um eine Übereinstimmung mit NULL zu suchen, wenn die innere Quelle kein zugehöriges Element aufweist.</span><span class="sxs-lookup"><span data-stu-id="2a78a-169">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="2a78a-170">Im folgenden Beispiel wird das Muster und dessen Ergebnis veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="2a78a-170">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="2a78a-171">Das obige Muster erstellt eine komplexe Struktur in der Ausdrucksbaumstruktur.</span><span class="sxs-lookup"><span data-stu-id="2a78a-171">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="2a78a-172">Daher erfordert EF Core, dass Sie die Gruppierungsergebnisse des GroupJoin-Operators in dem Schritt vereinfachen, der direkt auf den Operator folgt.</span><span class="sxs-lookup"><span data-stu-id="2a78a-172">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="2a78a-173">Selbst wenn GroupJoin-DefaultIfEmpty-SelectMany in einem anderen Muster verwendet wird, wird dies möglicherweise nicht als Left Join identifiziert.</span><span class="sxs-lookup"><span data-stu-id="2a78a-173">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
