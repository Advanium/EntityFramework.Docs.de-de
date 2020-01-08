---
title: Indizes-EF Core
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 810fccc0c6b035f515107601b245811f7b4118a6
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502135"
---
# <a name="indexes"></a>Indizes

Indizes sind ein gängiges Konzept in vielen Daten speichern. Während Ihre Implementierung im Datenspeicher variieren kann, werden Sie verwendet, um Suchvorgänge auf Grundlage einer Spalte (oder einer Gruppe von Spalten) effizienter zu gestalten.

Indizes können nicht mithilfe von Daten Anmerkungen erstellt werden. Sie können die fließende API verwenden, um einen Index für eine einzelne Spalte wie folgt anzugeben:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

Sie können einen Index auch über mehr als eine Spalte angeben:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> Gemäß der Konvention wird ein Index in jeder Eigenschaft (oder einem Satz von Eigenschaften) erstellt, die als Fremdschlüssel verwendet wird.
>
> EF Core unterstützt nur einen Index pro eindeutigem Satz von Eigenschaften. Wenn Sie die fließende API verwenden, um einen Index für eine Gruppe von Eigenschaften zu konfigurieren, für die bereits ein Index definiert wurde (entweder durch Konvention oder vorherige Konfiguration), ändern Sie die Definition des Indexes. Dies ist hilfreich, wenn Sie einen von der Konvention erstellten Index weiter konfigurieren möchten.

## <a name="index-uniqueness"></a>Index Eindeutigkeit

Standardmäßig sind Indizes nicht eindeutig: mehrere Zeilen dürfen für den Spalten Satz des Indexes die gleichen Werte aufweisen. Sie können einen Index wie folgt eindeutig gestalten:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

Wenn Sie versuchen, mehr als eine Entität mit denselben Werten für den Spalten Satz des Indexes einzufügen, wird eine Ausnahme ausgelöst.

## <a name="index-name"></a>Indexname

Gemäß der Konvention werden Indizes, die in einer relationalen Datenbank erstellt werden, `IX_<type name>_<property name>`benannt. Bei zusammengesetzten Indizes wird `<property name>` zu einer durch Trennzeichen getrennten Liste mit Eigenschaftsnamen.

Mit der flüssigen API können Sie den Namen des Indexes festlegen, der in der Datenbank erstellt wurde:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a>Index Filter

Einige relationale Datenbanken ermöglichen es Ihnen, einen gefilterten oder partiellen Index anzugeben. Auf diese Weise können Sie nur eine Teilmenge der Spaltenwerte indizieren, die Größe des Indexes verringern und sowohl die Leistung als auch die Speicherplatz Auslastung verbessern. Weitere Informationen zu SQL Server gefilterten Indizes finden Sie in [der Dokumentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes)zu.

Sie können die fließende API verwenden, um einen Filter für einen Index anzugeben, der als SQL-Ausdruck bereitgestellt wird:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

Bei Verwendung des SQL Server Anbieters fügt EF einen `'IS NOT NULL'` Filter für alle Spalten hinzu, die NULL-Werte zulassen, die Teil eines eindeutigen Indexes sind. Um diese Konvention zu überschreiben, können Sie einen `null` Wert angeben.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>Eingeschlossene Spalten

Mit einigen relationalen Datenbanken können Sie eine Gruppe von Spalten konfigurieren, die in den Index aufgenommen werden, aber nicht Teil Ihres "Schlüssels" sind. Dies kann die Abfrageleistung erheblich verbessern, wenn alle Spalten in der Abfrage in den Index als Schlüssel-oder nicht Schlüssel Spalten eingeschlossen werden, da auf die Tabelle selbst nicht zugegriffen werden muss. Weitere Informationen zu SQL Server enthaltenen Spalten finden Sie in [der Dokumentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)zu.

Im folgenden Beispiel ist die Spalte `Url` Teil des Index Schlüssels, sodass jede Abfrage Filterung für diese Spalte den Index verwenden kann. Außerdem müssen Abfragen, die nur auf die `Title`-und `PublishedOn` Spalten zugreifen, nicht auf die Tabelle zugreifen und effizienter ausgeführt werden:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
