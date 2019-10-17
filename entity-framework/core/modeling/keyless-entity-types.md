---
title: Typen von Keyless-Entitäten-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 3dbc2700fc9bb277eb90885dfc2506c250ae21f1
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445933"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="4ff25-102">Schlüssellose Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="4ff25-102">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="4ff25-103">Diese Funktion wurde in EF Core 2,1 unter dem Namen der Abfrage Typen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4ff25-103">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="4ff25-104">In EF Core 3,0 wurde das Konzept in schlüssellose Entitäts Typen umbenannt.</span><span class="sxs-lookup"><span data-stu-id="4ff25-104">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="4ff25-105">Zusätzlich zu regulären Entitäts Typen kann ein EF Core Modell _Schlüssel-Entitäts Typen_enthalten, die verwendet werden können, um Datenbankabfragen für Daten auszuführen, die keine Schlüsselwerte enthalten.</span><span class="sxs-lookup"><span data-stu-id="4ff25-105">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="4ff25-106">Merkmale von Keyless-Entitäts Typen</span><span class="sxs-lookup"><span data-stu-id="4ff25-106">Keyless entity types characteristics</span></span>

<span data-ttu-id="4ff25-107">Schlüssellose Entitäts Typen unterstützen viele der gleichen Zuordnungs Funktionen wie reguläre Entitäts Typen wie Vererbungs Zuordnung und Navigations Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="4ff25-107">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="4ff25-108">In relationalen speichern können Sie die Ziel Datenbankobjekte und-Spalten überfließende API-Methoden oder Daten Anmerkungen konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="4ff25-108">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="4ff25-109">Sie unterscheiden sich jedoch von regulären Entitäts Typen darin, dass Sie:</span><span class="sxs-lookup"><span data-stu-id="4ff25-109">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="4ff25-110">Ein Schlüssel kann nicht definiert werden.</span><span class="sxs-lookup"><span data-stu-id="4ff25-110">Cannot have a key defined.</span></span>
- <span data-ttu-id="4ff25-111">Werden nie nach Änderungen im _dbcontext_ nachverfolgt und daher nie in der Datenbank eingefügt, aktualisiert oder gelöscht.</span><span class="sxs-lookup"><span data-stu-id="4ff25-111">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="4ff25-112">Werden nie nach Konvention ermittelt.</span><span class="sxs-lookup"><span data-stu-id="4ff25-112">Are never discovered by convention.</span></span>
- <span data-ttu-id="4ff25-113">Unterstützt nur eine Teilmenge der Navigations Zuordnungsfunktionen, insbesondere:</span><span class="sxs-lookup"><span data-stu-id="4ff25-113">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="4ff25-114">Sie werden möglicherweise nie als Prinzipal Ende einer Beziehung fungieren.</span><span class="sxs-lookup"><span data-stu-id="4ff25-114">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="4ff25-115">Sie haben möglicherweise keine Navigation zu besitzende Entitäten.</span><span class="sxs-lookup"><span data-stu-id="4ff25-115">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="4ff25-116">Sie können nur Verweis Navigations Eigenschaften enthalten, die auf reguläre Entitäten verweisen.</span><span class="sxs-lookup"><span data-stu-id="4ff25-116">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="4ff25-117">Entitäten können keine Navigations Eigenschaften für Entitäts Typen ohne Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="4ff25-117">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="4ff25-118">Muss mit `.HasNoKey()`-Methodenaufrufe konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="4ff25-118">Need to be configured with `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="4ff25-119">Kann einer _definierenden Abfrage_zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="4ff25-119">May be mapped to a _defining query_.</span></span> <span data-ttu-id="4ff25-120">Eine definierende Abfrage ist eine im Modell deklarierte Abfrage, die als Datenquelle für einen schlüssellosen Entitätstyp fungiert.</span><span class="sxs-lookup"><span data-stu-id="4ff25-120">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="4ff25-121">Verwendungs Szenarien</span><span class="sxs-lookup"><span data-stu-id="4ff25-121">Usage scenarios</span></span>

<span data-ttu-id="4ff25-122">Einige der wichtigsten Verwendungs Szenarien für Typen von schlüssellosen Entitäten sind:</span><span class="sxs-lookup"><span data-stu-id="4ff25-122">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="4ff25-123">Fungieren als Rückgabetyp für unformatierte [SQL-Abfragen](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="4ff25-123">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="4ff25-124">Zuordnung zu Daten Bank Sichten, die keinen Primärschlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="4ff25-124">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="4ff25-125">Zuordnung zu Tabellen, für die kein Primärschlüssel definiert ist.</span><span class="sxs-lookup"><span data-stu-id="4ff25-125">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="4ff25-126">Zuordnung zu Abfragen, die im Modell definiert sind.</span><span class="sxs-lookup"><span data-stu-id="4ff25-126">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="4ff25-127">Zuordnung zu Datenbankobjekten</span><span class="sxs-lookup"><span data-stu-id="4ff25-127">Mapping to database objects</span></span>

<span data-ttu-id="4ff25-128">Die Zuordnung eines Datentyps ohne Tastatur zu einem Datenbankobjekt wird mithilfe der `ToTable`-oder `ToView`-API erreicht.</span><span class="sxs-lookup"><span data-stu-id="4ff25-128">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="4ff25-129">Aus Sicht der EF Core ist das in dieser Methode angegebene Datenbankobjekt eine _Sicht_. Dies bedeutet, dass es als schreibgeschützte Abfrage Quelle behandelt wird und nicht das Ziel von Aktualisierungs-, Einfüge-oder Lösch Vorgängen sein kann.</span><span class="sxs-lookup"><span data-stu-id="4ff25-129">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="4ff25-130">Dies bedeutet jedoch nicht, dass das Datenbankobjekt tatsächlich eine Daten Bank Sicht sein muss.</span><span class="sxs-lookup"><span data-stu-id="4ff25-130">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="4ff25-131">Alternativ kann es sich um eine Datenbanktabelle handeln, die als schreibgeschützt behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="4ff25-131">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="4ff25-132">Im Gegensatz dazu geht EF Core bei regulären Entitäts Typen davon aus, dass ein in der `ToTable`-Methode festgelegtes Datenbankobjekt als _Tabelle_behandelt werden kann. Dies bedeutet, dass es als Abfrage Quelle, aber auch für Update-, DELETE-und INSERT-Vorgänge verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="4ff25-132">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="4ff25-133">In der Tat können Sie den Namen einer Daten Bank Sicht in `ToTable` angeben, und alles sollte einwandfrei funktionieren, solange die Sicht so konfiguriert ist, dass Sie für die Datenbank aktualisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="4ff25-133">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="4ff25-134">`ToView` geht davon aus, dass das Objekt bereits in der Datenbank vorhanden ist, und wird nicht durch Migrationen erstellt.</span><span class="sxs-lookup"><span data-stu-id="4ff25-134">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="4ff25-135">Beispiel</span><span class="sxs-lookup"><span data-stu-id="4ff25-135">Example</span></span>

<span data-ttu-id="4ff25-136">Das folgende Beispiel zeigt, wie Sie schlüssellose Entitäts Typen verwenden, um eine Daten Bank Sicht abzufragen.</span><span class="sxs-lookup"><span data-stu-id="4ff25-136">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="4ff25-137">Das in diesem Artikel verwendete [Beispiel](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="4ff25-137">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="4ff25-138">Zunächst definieren wir einen einfachen Blog und ein Post-Modell:</span><span class="sxs-lookup"><span data-stu-id="4ff25-138">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="4ff25-139">Als nächstes definieren wir eine einfache Daten Bank Ansicht, die es uns ermöglicht, die Anzahl von Beiträgen abzufragen, die mit den einzelnen Blogs verknüpft sind:</span><span class="sxs-lookup"><span data-stu-id="4ff25-139">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="4ff25-140">Als nächstes definieren wir eine Klasse, die das Ergebnis aus der Daten Bank Sicht enthält:</span><span class="sxs-lookup"><span data-stu-id="4ff25-140">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="4ff25-141">Als Nächstes konfigurieren wir den Entitätstyp "Schlüssel" in " _onmodelcreating_ " mithilfe der `HasNoKey`-API.</span><span class="sxs-lookup"><span data-stu-id="4ff25-141">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="4ff25-142">Wir verwenden die fließende Konfigurations-API, um die Zuordnung für den Entitätstyp "Schlüssel" zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="4ff25-142">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="4ff25-143">Als Nächstes konfigurieren wir den `DbContext`, um den `DbSet<T>` einzuschließen:</span><span class="sxs-lookup"><span data-stu-id="4ff25-143">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="4ff25-144">Schließlich können wir die Daten Bank Sicht standardmäßig Abfragen:</span><span class="sxs-lookup"><span data-stu-id="4ff25-144">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="4ff25-145">Beachten Sie, dass wir auch eine Kontext Stufen-Abfrage Eigenschaft (dbset) definiert haben, die als Stamm für Abfragen dieses Typs fungiert.</span><span class="sxs-lookup"><span data-stu-id="4ff25-145">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
