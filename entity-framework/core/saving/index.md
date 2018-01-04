---
title: "Speichern von Daten – EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
ms.technology: entity-framework-core
uid: core/saving/index
ms.openlocfilehash: 9280b9c34b41c0319f918488cd7d28eeceef12e7
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2017
---
# <a name="saving-data"></a><span data-ttu-id="2d0d1-102">Speichern von Daten</span><span class="sxs-lookup"><span data-stu-id="2d0d1-102">Saving Data</span></span>

<span data-ttu-id="2d0d1-103">Jede Kontextinstanz verfügt über ein `ChangeTracker`-Objekt, das für die Nachverfolgung von in die Datenbank zu schreibenden Änderungen zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="2d0d1-103">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="2d0d1-104">Wenn Sie Änderungen an Instanzen Ihrer Entitätsklassen vornehmen, werden diese Änderungen in `ChangeTracker` erfasst und dann beim Aufrufen von `SaveChanges` in die Datenbank geschrieben.</span><span class="sxs-lookup"><span data-stu-id="2d0d1-104">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="2d0d1-105">Der Datenbankanbieter ist für die Übersetzung der Änderungen in datenbankspezifische Vorgänge zuständig (z.B. die Befehle `INSERT`, `UPDATE` und `DELETE` für eine relationale Datenbank).</span><span class="sxs-lookup"><span data-stu-id="2d0d1-105">The database provider is responsible for translating the changes into database-specific operations (e.g. `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>