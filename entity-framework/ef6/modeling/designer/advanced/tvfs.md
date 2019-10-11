---
title: Tabellenwert Funktionen (TVFs)-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: f019c97b-87b0-4e93-98f4-2c539f77b2dc
ms.openlocfilehash: 35684196dcd7b708a8feeb1eca3096e8d4e555ec
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182535"
---
# <a name="table-valued-functions-tvfs"></a>Tabellenwert Funktionen (TVFs)
> [!NOTE]
> **Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

Das Video und die schrittweise exemplarische Vorgehensweise zeigen, wie Sie Tabellenwert Funktionen (TVFs) mithilfe der Entity Framework Designer zuordnen. Außerdem wird veranschaulicht, wie eine TVF aus einer LINQ-Abfrage aufgerufen wird.

TVFs werden derzeit nur im Database First Workflow unterstützt.

Die TVF-Unterstützung wurde in Entity Framework Version 5 eingeführt. Beachten Sie, dass Sie für die Verwendung der neuen Features wie Tabellenwert Funktionen, Enumerationstypen und räumlichen Typen auf .NET Framework 4,5 abzielen müssen. Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.

TVFs ähneln gespeicherten Prozeduren mit einem wichtigen Unterschied: das Ergebnis einer TVF ist zusammensetzbar. Dies bedeutet, dass die Ergebnisse einer TVF in einer LINQ-Abfrage verwendet werden können, während die Ergebnisse einer gespeicherten Prozedur dies nicht möglich sind.

## <a name="watch-the-video"></a>Video ansehen

**Präsentiert von**: Julia kornich

[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)

## <a name="pre-requisites"></a>Voraussetzungen

Um diese exemplarische Vorgehensweise durchführen zu können, benötigen Sie Folgendes:

- Installieren Sie die [Datenbank "School](~/ef6/resources/school-database.md)".

- Aktuelle Version von Visual Studio

## <a name="set-up-the-project"></a>Einrichten des Projekts

1.  Öffnen Sie Visual Studio.
2.  Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .
3.  Klicken Sie im linken Bereich auf **Visual C @ no__t-1**, und wählen Sie dann die **Konsolen** Vorlage aus.
4.  Geben Sie **TVF** als Namen für das Projekt ein, und klicken Sie auf **OK** .

## <a name="add-a-tvf-to-the-database"></a>Hinzufügen einer TVF zur Datenbank

-   Wählen Sie **View-&gt; SQL Server-Objekt-Explorer**
-   Wenn localdb nicht in der Liste der Server enthalten ist: Klicken Sie mit der rechten Maustaste auf **SQL Server** , und wählen Sie **Hinzufügen** aus **, um eine Verbindung mit dem** localdb-Server herzustellen SQL Server
-   Erweitern Sie den Knoten localdb.
-   Klicken Sie unter dem Knoten Datenbanken mit der rechten Maustaste auf den Datenbankknoten School, und wählen Sie **neue Abfrage aus.**
-   Fügen Sie im T-SQL-Editor die folgende TVF-Definition ein.

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   Klicken Sie mit der rechten Maustaste auf den T-SQL-Editor, und wählen Sie **Ausführen** aus.
-   Die Funktion "getstudentgradesforcourse" wird der Datenbank "School" hinzugefügt.

 

## <a name="create-a-model"></a>Erstellen eines Modells

1.  Klicken Sie mit der rechten Maustaste auf den Projektnamen Projektmappen-Explorer, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**
2.  Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich **Vorlagen** die Option **ADO.NET Entity Data Model** aus.
3.  Geben Sie als Dateiname **tvfmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen** .
4.  Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **weiter** .
5.  Klicken Sie auf **neue Verbindung** eingeben **(localdb) \\mssqllocaldb** im Textfeld Server Name geben Sie **School** Für den Datenbanknamen ein, klicken Sie auf **OK** .
6.  Wählen Sie im Dialogfeld Datenbankobjekte auswählen unter dem Knoten **Tabellen** Den Knoten **Person**, **StudentGrade**und **Course** tables aus.
7.  Wählen Sie die Funktion **getstudentgradesforcourse** aus, die sich unter dem  node-Hinweis für **gespeicherte Prozeduren und Funktionen**befindet. ab Visual Studio 2012 können Sie mit der Entity Designer einen Batch Import gespeicherter Prozeduren und Funktionen ausführen.
8.  Klicken auf **Fertig** stellen
9.  Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt. Alle Objekte, die Sie im Dialogfeld **Wählen Sie Ihre Datenbankobjekte**  ausgewählt haben, werden dem Modell hinzugefügt.
10. Standardmäßig wird die Ergebnis Form der einzelnen importierten gespeicherten Prozeduren oder Funktionen automatisch zu einem neuen komplexen Typ im Entitäts Modell. Wir möchten jedoch die Ergebnisse der Funktion "getstudentgradesforcourse" der Entität "StudentGrade" zuordnen: Klicken Sie mit der rechten Maustaste auf die Entwurfs Oberfläche, und wählen Sie im Modell Browser **Modell Browser** aus, wählen Sie **Funktions Importe**aus, und doppelklicken Sie dann im Dialogfeld Funktions Import bearbeiten auf die Funktion **getstudentgradesforcourse** , wählen Sie **Entitäten** aus.  und wählen Sie **StudentGrade** aus.

## <a name="persist-and-retrieve-data"></a>Persistenz und Abrufen von Daten

Öffnen Sie die Datei, in der die Main-Methode definiert ist. Fügen Sie der Main-Funktion den folgenden Code hinzu.

Der folgende Code veranschaulicht, wie eine Abfrage erstellt wird, die eine Tabellenwert Funktion verwendet. Die Abfrage projiziert die Ergebnisse in einen anonymen Typ, der den zugehörigen Kurs Titel und verwandte Studenten mit einem Wert größer oder gleich 3,5 enthält.

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

Kompilieren Sie die Anwendung, und führen Sie sie aus. Das Programm erzeugt die folgende Ausgabe:

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise wird erläutert, wie Tabellenwert Funktionen (TVFs) mithilfe der Entity Framework Designer zugeordnet werden. Außerdem wurde veranschaulicht, wie eine TVF aus einer LINQ-Abfrage aufgerufen wird.
