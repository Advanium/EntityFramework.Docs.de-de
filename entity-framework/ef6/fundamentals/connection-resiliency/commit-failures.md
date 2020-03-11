---
title: Behandeln von Fehlern bei Transaktionscommit EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: 27e75e6a1919ee2300fe76cfcdf67cceaad887b3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414674"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="457ed-102">Behandeln von transaktionscommitfehlern</span><span class="sxs-lookup"><span data-stu-id="457ed-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="457ed-103">**Nur EF 6.1** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 6,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="457ed-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="457ed-104">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="457ed-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="457ed-105">Als Teil von 6,1 wird ein neues Feature für die Verbindungs Resilienz für EF eingeführt: die Möglichkeit, automatisch zu erkennen und wiederherzustellen, wenn vorübergehende Verbindungsfehler die Bestätigung von Transaktionscommits beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="457ed-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="457ed-106">Die vollständigen Details des Szenarios werden am besten im Blogbeitrag [SQL-Datenbankkonnektivität und das Idempotenz-Problem](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="457ed-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx).</span></span>  <span data-ttu-id="457ed-107">Zusammenfassend gilt: Wenn eine Ausnahme während eines Transaktionscommits ausgelöst wird, gibt es zwei mögliche Ursachen:</span><span class="sxs-lookup"><span data-stu-id="457ed-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="457ed-108">Fehler beim Commit der Transaktion auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="457ed-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="457ed-109">Der Transaktionscommit war auf dem Server erfolgreich, aber aufgrund eines Konnektivitätsproblems wurde die Erfolgs Benachrichtigung an den Client</span><span class="sxs-lookup"><span data-stu-id="457ed-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="457ed-110">Bei der ersten Situation kann die Anwendung oder der Benutzer den Vorgang wiederholen, aber wenn die zweite Situation eintritt, sollten Wiederholungs Versuche vermieden werden, und die Anwendung kann automatisch wieder hergestellt werden.</span><span class="sxs-lookup"><span data-stu-id="457ed-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="457ed-111">Die Herausforderung besteht darin, dass die Anwendung nicht die richtige Vorgehensweise auswählen kann, ohne zu erkennen, was der tatsächliche Grund ist, warum eine Ausnahme während des Commit gemeldet wurde.</span><span class="sxs-lookup"><span data-stu-id="457ed-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="457ed-112">Das neue Feature in EF 6,1 ermöglicht EF, sich mit der Datenbank zu überprüfen, wenn die Transaktion erfolgreich war, und die richtige Vorgehensweise zur transparenten Ausführung zu treffen.</span><span class="sxs-lookup"><span data-stu-id="457ed-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="457ed-113">Verwenden der Funktion</span><span class="sxs-lookup"><span data-stu-id="457ed-113">Using the feature</span></span>  

<span data-ttu-id="457ed-114">Um die Funktion zu aktivieren, die Sie benötigen, müssen Sie im Konstruktor von **dbconfiguration**einen [calltransaktionshandlerbefehl](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) einfügen.</span><span class="sxs-lookup"><span data-stu-id="457ed-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="457ed-115">Wenn Sie mit **dbconfiguration**nicht vertraut sind, finden Sie unter [Code basierte Konfiguration](~/ef6/fundamentals/configuring/code-based.md)Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="457ed-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="457ed-116">Diese Funktion kann in Kombination mit den automatischen Wiederholungen verwendet werden, die wir in EF6 eingeführt haben, was zu einer Situation beiträgt, in der die Transaktion aufgrund eines vorübergehenden Fehlers tatsächlich keinen Commit auf dem Server ausgeführt hat:</span><span class="sxs-lookup"><span data-stu-id="457ed-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="457ed-117">Nachverfolgen von Transaktionen</span><span class="sxs-lookup"><span data-stu-id="457ed-117">How transactions are tracked</span></span>  

<span data-ttu-id="457ed-118">Wenn das Feature aktiviert ist, fügt EF der Datenbank automatisch eine neue Tabelle mit dem Namen **__Transactions**hinzu.</span><span class="sxs-lookup"><span data-stu-id="457ed-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="457ed-119">Wenn eine Transaktion von EF erstellt wird, wird in diese Tabelle eine neue Zeile eingefügt, und diese Zeile wird auf vorhanden sein überprüft, wenn während des Commits ein Transaktionsfehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="457ed-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="457ed-120">Obwohl EF den besten Aufwand zum Löschen von Zeilen aus der Tabelle durchführt, wenn Sie nicht mehr benötigt werden, kann die Tabelle vergrößert werden, wenn die Anwendung vorzeitig beendet wird. aus diesem Grund müssen Sie die Tabelle in einigen Fällen manuell löschen.</span><span class="sxs-lookup"><span data-stu-id="457ed-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="457ed-121">Behandeln von commitfehlern mit früheren Versionen</span><span class="sxs-lookup"><span data-stu-id="457ed-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="457ed-122">Vor EF 6,1 gab es keinen Mechanismus zum Behandeln von commitfehlern im EF-Produkt.</span><span class="sxs-lookup"><span data-stu-id="457ed-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="457ed-123">Es gibt mehrere Möglichkeiten, diese Situation zu behandeln, die auf frühere Versionen von EF6 angewendet werden kann:</span><span class="sxs-lookup"><span data-stu-id="457ed-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="457ed-124">Option 1-nichts tun</span><span class="sxs-lookup"><span data-stu-id="457ed-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="457ed-125">Die Wahrscheinlichkeit eines Verbindungsfehlers bei einem Transaktionscommit ist niedrig, sodass es für Ihre Anwendung möglicherweise nicht zu einem Fehler kommt, wenn diese Bedingung tatsächlich auftritt.</span><span class="sxs-lookup"><span data-stu-id="457ed-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="457ed-126">Option 2: Verwenden der Datenbank zum Zurücksetzen des Zustands</span><span class="sxs-lookup"><span data-stu-id="457ed-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="457ed-127">Aktuellen dbcontext verwerfen</span><span class="sxs-lookup"><span data-stu-id="457ed-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="457ed-128">Erstellen Sie einen neuen dbcontext, und stellen Sie den Status der Anwendung aus der Datenbank wieder her.</span><span class="sxs-lookup"><span data-stu-id="457ed-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="457ed-129">Informieren Sie den Benutzer darüber, dass der letzte Vorgang möglicherweise nicht erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="457ed-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="457ed-130">Option 3: Manuelles Nachverfolgen der Transaktion</span><span class="sxs-lookup"><span data-stu-id="457ed-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="457ed-131">Fügen Sie der Datenbank, die zum Nachverfolgen des Status der Transaktionen verwendet wird, eine nicht nach verfolgte Tabelle hinzu.</span><span class="sxs-lookup"><span data-stu-id="457ed-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="457ed-132">Fügen Sie am Anfang jeder Transaktion eine Zeile in die Tabelle ein.</span><span class="sxs-lookup"><span data-stu-id="457ed-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="457ed-133">Wenn die Verbindung während des Commit fehlschlägt, überprüfen Sie, ob die entsprechende Zeile in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="457ed-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="457ed-134">Wenn die Zeile vorhanden ist, fahren Sie normal fort, da für die Transaktion erfolgreich ein Commit ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="457ed-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="457ed-135">Wenn die Zeile nicht vorhanden ist, verwenden Sie eine Ausführungs Strategie, um den aktuellen Vorgang zu wiederholen.</span><span class="sxs-lookup"><span data-stu-id="457ed-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="457ed-136">Wenn der Commit erfolgreich ausgeführt wurde, löschen Sie die entsprechende Zeile, um das Wachstum der Tabelle zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="457ed-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="457ed-137">[Dieser Blogbeitrag](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) enthält Beispielcode zum Erreichen dieser SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="457ed-137">[This blog post](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) contains sample code for accomplishing this on SQL Azure.</span></span>  
