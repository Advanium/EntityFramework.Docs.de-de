---
title: Überblick über Entity Framework Core – EF Core
description: Allgemeine Einführungsübersicht für Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/index
ms.openlocfilehash: 1d320ecae06d9c05fe3a14ec955f7151de6a56e5
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071926"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework Core (EF Core) ist eine einfache, erweiterbare und plattformübergreifende [Open Source](https://github.com/aspnet/EntityFrameworkCore)-Version der beliebten Entity Framework-Datenzugriffstechnologie.

EF Core kann als objektrelationaler Mapper (O/RM) eingesetzt werden und bietet .NET-Entwicklern so die Möglichkeit, unter Verwendung von .NET-Objekten mit einer Datenbank zu arbeiten. Auf diese Weise entfällt ein Großteil des Datenzugriffscodes, der üblicherweise geschrieben werden muss.

Einzelheiten zu den von EF Core unterstützten Datenbank-Engines finden Sie unter [Datenbankanbieter](xref:core/providers/index).

## <a name="the-model"></a>Das Modell

Bei EF Core erfolgt der Datenzugriff über ein Modell. Ein Modell setzt sich aus Entitätsklassen und einem Kontextobjekt zusammen, das eine Sitzung mit der Datenbank darstellt und Ihnen das Abfragen und Speichern von Daten ermöglicht. Weitere Informationen finden Sie unter [Erstellen eines Modells](xref:core/modeling/index).

Sie können ein Modell aus einer vorhandenen Datenbank generieren, ein Modell manuell entsprechend Ihrer Datenbank codieren oder mithilfe von [EF-Migrationen](xref:core/managing-schemas/migrations/index) eine Datenbank anhand Ihres Modells erstellen und sie im Laufe der Zeit gemäß Ihres Modells weiterentwickeln.

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>Abfragen

Instanzen Ihrer Entitätsklassen werden mit Language Integrated Query (LINQ) von der Datenbank abgerufen. Weitere Informationen finden Sie unter [Abfragen von Daten](xref:core/querying/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>Speichern von Daten

Daten werden in der Datenbank mithilfe von Instanzen Ihrer Entitätsklassen erstellt, gelöscht und geändert. Weitere Informationen finden Sie unter [Speichern von Daten](xref:core/saving/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a>Nächste Schritte

Einführungstutorials finden Sie unter [Erste Schritte mit Entity Framework Core](xref:core/get-started/index).
