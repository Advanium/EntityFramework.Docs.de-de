---
title: Die Load-Methode – EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
caps.latest.revision: 3
ms.openlocfilehash: 83af79220b52de6e3063868fd9bdac56867d49cb
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121144"
---
# <a name="the-load-method"></a><span data-ttu-id="10322-102">Load-Methode</span><span class="sxs-lookup"><span data-stu-id="10322-102">The Load Method</span></span>
<span data-ttu-id="10322-103">Es gibt mehrere Szenarien, in denen Sie Entitäten aus der Datenbank in den Kontext zu laden, ohne direkt mit diesen Entitäten übernehmen möchten.</span><span class="sxs-lookup"><span data-stu-id="10322-103">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="10322-104">Ein gutes Beispiel hierfür ist Laden von Entitäten für die Datenbindung unter [lokale Daten](~/ef6/querying/local-data.md).</span><span class="sxs-lookup"><span data-stu-id="10322-104">A good example of this is loading entities for data binding as described in [Local Data](~/ef6/querying/local-data.md).</span></span> <span data-ttu-id="10322-105">Eine allgemeine Möglichkeit hierzu ist eine LINQ-Abfrage schreiben, und rufen Sie dann darauf, nur für die erstellte Liste verwirft sofort "ToList".</span><span class="sxs-lookup"><span data-stu-id="10322-105">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="10322-106">Die Load-Erweiterungsmethode funktioniert genauso wie "ToList", außer dass es die Erstellung der Liste der vollständig vermeidet.</span><span class="sxs-lookup"><span data-stu-id="10322-106">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="10322-107">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="10322-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="10322-108">Hier sind zwei Beispiele für die Verwendung der Last.</span><span class="sxs-lookup"><span data-stu-id="10322-108">Here are two examples of using Load.</span></span> <span data-ttu-id="10322-109">Die erste stammt aus einer Windows Forms-datenbindungsanwendung, in der Last verwendet für Entitäten, die vor der Bindung an eine lokale Sammlung, Abfragen, wie in beschrieben [lokale Daten](~/ef6/querying/local-data.md):</span><span class="sxs-lookup"><span data-stu-id="10322-109">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](~/ef6/querying/local-data.md):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="10322-110">Im zweiten Beispiel wird mithilfe von Load Laden Sie eine gefilterte Auflistung von verknüpften Entitäten in beschriebenen [Laden von verknüpften Entitäten](~/ef6/querying/related-data.md):</span><span class="sxs-lookup"><span data-stu-id="10322-110">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](~/ef6/querying/related-data.md):</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();
}
```  