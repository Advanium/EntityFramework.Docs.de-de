---
title: Die Load-Methode-EF6
description: Die Load-Methode in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/load-method
ms.openlocfilehash: 1e6dd172eebdc177dad8ef312c8d14083a409410
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072966"
---
# <a name="the-load-method"></a>Load-Methode
Es gibt mehrere Szenarien, in denen Sie möglicherweise Entitäten aus der Datenbank in den Kontext laden möchten, ohne sofort etwas mit diesen Entitäten zu tun. Ein gutes Beispiel hierfür ist das Laden von Entitäten für die Datenbindung, wie unter [lokale Daten](xref:ef6/querying/local-data)beschrieben. Eine gängige Methode hierfür ist das Schreiben einer LINQ-Abfrage und das anschließende aufzurufen der Liste, um die erstellte Liste sofort zu verwerfen. Die Load-Erweiterungsmethode funktioniert genauso wie die-Liste, mit der Ausnahme, dass Sie die vollständige Erstellung der Liste vermeidet.  

Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

Im folgenden finden Sie zwei Beispiele für die Verwendung von Load. Der erste wird aus einer Windows Forms Daten Bindungs Anwendung übernommen, bei der Load verwendet wird, um Entitäten vor der Bindung an die lokale Auflistung abzufragen, wie unter [lokale Daten](xref:ef6/querying/local-data)beschrieben:  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

Das zweite Beispiel zeigt die Verwendung von Load, um eine gefilterte Auflistung verwandter Entitäten zu laden, wie unter Laden von verknüpften [Entitäten](xref:ef6/querying/related-data)beschrieben:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
