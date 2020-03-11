---
title: Entitäts Typen-EF Core
description: Konfigurieren und Zuordnen von Entitäts Typen mithilfe von Entity Framework Core
author: roji
ms.date: 12/03/2019
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/entity-types
ms.openlocfilehash: b3d9ad753637d021d9aa52965da38091ae690f77
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414578"
---
# <a name="entity-types"></a><span data-ttu-id="4d7f7-103">Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="4d7f7-103">Entity Types</span></span>

<span data-ttu-id="4d7f7-104">Das Einschließen eines dbsets eines Typs in den Kontext bedeutet, dass es in EF Core Modell enthalten ist. Normalerweise wird ein solcher Typ als *Entität*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="4d7f7-105">EF Core können Entitäts Instanzen aus der Datenbank lesen und in diese schreiben. Wenn Sie eine relationale Datenbank verwenden, können EF Core Tabellen für Ihre Entitäten über Migrationen erstellen.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="4d7f7-106">Einschließen von Typen im Modell</span><span class="sxs-lookup"><span data-stu-id="4d7f7-106">Including types in the model</span></span>

<span data-ttu-id="4d7f7-107">Gemäß der Konvention sind Typen, die in dbset-Eigenschaften für den Kontext verfügbar gemacht werden, im Modell als Entitäten enthalten.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="4d7f7-108">In der `OnModelCreating`-Methode angegebene Entitäts Typen werden ebenfalls eingeschlossen, ebenso wie alle Typen, die gefunden werden, indem die Navigations Eigenschaften anderer ermittelter Entitäts Typen rekursiv untersucht werden.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="4d7f7-109">Im folgenden Codebeispiel sind alle Typen enthalten:</span><span class="sxs-lookup"><span data-stu-id="4d7f7-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="4d7f7-110">`Blog` ist enthalten, da es in einer dbset-Eigenschaft im Kontext verfügbar gemacht wird.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="4d7f7-111">`Post` ist enthalten, da es über die `Blog.Posts` Navigations Eigenschaft erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="4d7f7-112">`AuditEntry`, da Sie in `OnModelCreating`angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="4d7f7-113">Ausschließen von Typen aus dem Modell</span><span class="sxs-lookup"><span data-stu-id="4d7f7-113">Excluding types from the model</span></span>

<span data-ttu-id="4d7f7-114">Wenn Sie nicht möchten, dass ein Typ im Modell enthalten ist, können Sie ihn ausschließen:</span><span class="sxs-lookup"><span data-stu-id="4d7f7-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4d7f7-115">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="4d7f7-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4d7f7-116">Fließende API</span><span class="sxs-lookup"><span data-stu-id="4d7f7-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a><span data-ttu-id="4d7f7-117">Tabellenname</span><span class="sxs-lookup"><span data-stu-id="4d7f7-117">Table name</span></span>

<span data-ttu-id="4d7f7-118">Gemäß der Konvention wird jeder Entitätstyp so eingerichtet, dass er einer Datenbanktabelle mit dem gleichen Namen wie die dbset-Eigenschaft zugeordnet wird, die die Entität verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-118">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="4d7f7-119">Wenn kein dbset für die angegebene Entität vorhanden ist, wird der Klassenname verwendet.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-119">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="4d7f7-120">Sie können den Tabellennamen manuell konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="4d7f7-120">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4d7f7-121">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="4d7f7-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4d7f7-122">Fließende API</span><span class="sxs-lookup"><span data-stu-id="4d7f7-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="4d7f7-123">Tabellenschema</span><span class="sxs-lookup"><span data-stu-id="4d7f7-123">Table schema</span></span>

<span data-ttu-id="4d7f7-124">Wenn Sie eine relationale Datenbank verwenden, werden Tabellen gemäß der Konvention im Standardschema Ihrer Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-124">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="4d7f7-125">Beispielsweise wird Microsoft SQL Server das `dbo` Schema verwenden (SQLite unterstützt keine Schemas).</span><span class="sxs-lookup"><span data-stu-id="4d7f7-125">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="4d7f7-126">Tabellen, die in einem bestimmten Schema erstellt werden sollen, können wie folgt konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="4d7f7-126">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4d7f7-127">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="4d7f7-127">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4d7f7-128">Fließende API</span><span class="sxs-lookup"><span data-stu-id="4d7f7-128">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="4d7f7-129">Anstatt das Schema für jede Tabelle anzugeben, können Sie auch das Standardschema auf Modell Ebene mit der fließenden API definieren:</span><span class="sxs-lookup"><span data-stu-id="4d7f7-129">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="4d7f7-130">Beachten Sie, dass das Festlegen des Standard Schemas auch andere Datenbankobjekte, z. b. Sequenzen, beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="4d7f7-130">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>
