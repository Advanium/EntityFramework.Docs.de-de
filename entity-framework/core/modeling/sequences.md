---
title: Sequenzen-EF Core
author: roji
ms.date: 12/18/2019
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/sequences
ms.openlocfilehash: 6343e58dd79837cc69308a070c9814030505d7dd
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502438"
---
# <a name="sequences"></a><span data-ttu-id="fa436-102">Sequenzen</span><span class="sxs-lookup"><span data-stu-id="fa436-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="fa436-103">Sequenzen werden in der Regel nur von relationalen Datenbanken unterstützt.</span><span class="sxs-lookup"><span data-stu-id="fa436-103">Sequences are a feature typically supported only by relational databases.</span></span> <span data-ttu-id="fa436-104">Wenn Sie eine nicht relationale Datenbank (z. b. Cosmos) verwenden, überprüfen Sie die Datenbankdokumentation, um eindeutige Werte zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="fa436-104">If you're using a non-relational database such as Cosmos, check your database documentation on generating unique values.</span></span>

<span data-ttu-id="fa436-105">Eine Sequenz generiert eindeutige, sequenzielle numerische Werte in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="fa436-105">A sequence generates unique, sequential numeric values in the database.</span></span> <span data-ttu-id="fa436-106">Sequenzen sind keiner bestimmten Tabelle zugeordnet, und mehrere Tabellen können so eingerichtet werden, dass Sie Werte aus derselben Sequenz zeichnen.</span><span class="sxs-lookup"><span data-stu-id="fa436-106">Sequences are not associated with a specific table, and multiple tables can be set up to draw values from the same sequence.</span></span>

## <a name="basic-usage"></a><span data-ttu-id="fa436-107">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="fa436-107">Basic usage</span></span>

<span data-ttu-id="fa436-108">Sie können eine Sequenz im Modell einrichten und Sie dann verwenden, um Werte für Eigenschaften zu generieren:</span><span class="sxs-lookup"><span data-stu-id="fa436-108">You can set up a sequence in the model, and then use it to generate values for properties:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

<span data-ttu-id="fa436-109">Beachten Sie, dass das zum Generieren eines Werts aus einer Sequenz verwendete SQL-Datenbankspezifisch ist. Das obige Beispiel funktioniert auf SQL Server, schlägt jedoch für andere Datenbanken fehl.</span><span class="sxs-lookup"><span data-stu-id="fa436-109">Note that the specific SQL used to generate a value from a sequence is database-specific; the above example works on SQL Server but will fail on other databases.</span></span> <span data-ttu-id="fa436-110">Weitere Informationen finden Sie in der Dokumentation der jeweiligen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="fa436-110">Consult your specific database's documentation for more information.</span></span>

## <a name="configuring-sequence-settings"></a><span data-ttu-id="fa436-111">Konfigurieren von Sequenz Einstellungen</span><span class="sxs-lookup"><span data-stu-id="fa436-111">Configuring sequence settings</span></span>

<span data-ttu-id="fa436-112">Sie können auch weitere Aspekte der Sequenz konfigurieren, z. b. das Schema, den Startwert, das Inkrement usw.:</span><span class="sxs-lookup"><span data-stu-id="fa436-112">You can also configure additional aspects of the sequence, such as its schema, start value, increment, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]