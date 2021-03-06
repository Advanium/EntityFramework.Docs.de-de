---
title: Referenz zu EF Core Tools (Paket-Manager-Konsole)-EF Core
description: Referenzhandbuch für die Entity Framework Core Visual Studio-Paket-Manager-Konsole
author: bricelam
ms.author: bricelam
ms.date: 09/09/2020
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 5dca397978c60c12610d9080caba972a66b079b6
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071861"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Referenz zur Entity Framework Core Tools-Paket-Manager-Konsole in Visual Studio

Die Paket-Manager-Konsole (PMC)-Tools für Entity Framework Core die Entwicklungsaufgaben zur Entwurfszeit ausführen. Beispielsweise erstellen Sie [Migrationen](/aspnet/core/data/ef-mvc/migrations), wenden Migrationen an und generieren Code für ein Modell, das auf einer vorhandenen Datenbank basiert. Die Befehle werden innerhalb von Visual Studio mithilfe der [Paket-Manager-Konsole](/nuget/tools/package-manager-console)ausgeführt. Diese Tools funktionieren sowohl mit .NET Framework- als auch mit .NET Core-Projekten.

Wenn Sie Visual Studio nicht verwenden, empfiehlt es sich, stattdessen die [EF Core Befehlszeilen Tools](xref:core/miscellaneous/cli/dotnet) zu verwenden. Die .net Core-CLI Tools sind plattformübergreifend und werden innerhalb einer Eingabeaufforderung ausgeführt.

## <a name="installing-the-tools"></a>Installieren der Tools

Die Verfahren zum Installieren und Aktualisieren der Tools unterscheiden sich zwischen ASP.net Core 2.1 und früheren Versionen oder anderen Projekttypen.

### <a name="aspnet-core-version-21-and-later"></a>ASP.net Core Version 2,1 und höher

Die Tools werden automatisch in ein ASP.net Core 2.1 +-Projekt eingeschlossen, da das `Microsoft.EntityFrameworkCore.Tools` Paket im [Metapaket Microsoft. aspnetcore. app](/aspnet/core/fundamentals/metapackage-app)enthalten ist.

Daher müssen Sie keine weiteren Schritte ausführen, um die Tools zu installieren, aber Sie müssen folgende Schritte ausführen:

* Stellen Sie Pakete wieder her, bevor Sie die Tools in einem neuen Projekt verwenden.
* Installieren Sie ein Paket, um die Tools auf eine neuere Version zu aktualisieren.

Um sicherzustellen, dass Sie die neueste Version der Tools erhalten, empfiehlt es sich, auch den folgenden Schritt durchzuführen:

* Bearbeiten Sie die *csproj* -Datei, und fügen Sie eine Zeile hinzu, die die neueste Version des [Microsoft. entityframeworkcore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) -Pakets angibt. Die *csproj* -Datei kann beispielsweise einen enthalten, der `ItemGroup` wie folgt aussieht:

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

Aktualisieren Sie die Tools, wenn Sie eine Meldung wie im folgenden Beispiel erhalten:

> Die EF Core Tools-Version "2.1.1-RTM-30846" ist älter als die der Runtime "2.1.3-RTM-32065". Aktualisieren Sie die Tools für die neuesten Features und Fehlerbehebungen.

So aktualisieren Sie die Tools:

* Installieren Sie das neueste .NET Core SDK.
* Aktualisieren Sie Visual Studio auf die neuste Version.
* Bearbeiten Sie die *csproj* -Datei so, dass Sie einen Paket Verweis auf das aktuellste Tool Paket enthält, wie zuvor gezeigt.

### <a name="other-versions-and-project-types"></a>Andere Versionen und Projekttypen

Installieren Sie die Paket-Manager-Konsole, indem Sie den folgenden Befehl in der **Paket-Manager-Konsole**ausführen:

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Aktualisieren Sie die Tools, indem Sie den folgenden Befehl in der **Paket-Manager-Konsole**ausführen.

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>Überprüfen der Installation

Stellen Sie sicher, dass die Tools installiert sind, indem Sie folgenden Befehl ausführen:

``` powershell
Get-Help about_EntityFrameworkCore
```

Die Ausgabe sieht wie folgt aus (Sie gibt nicht an, welche Version der Tools Sie verwenden):

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a>Verwenden der Tools

Vor der Verwendung der Tools:

* Verstehen Sie den Unterschied Zwischenziel und Startprojekt.
* Erfahren Sie, wie Sie die Tools mit .NET Standard-Klassenbibliotheken verwenden.
* Legen Sie für ASP.net Core Projekte die Umgebung fest.

### <a name="target-and-startup-project"></a>Ziel-und Startprojekt

Die Befehle verweisen auf ein *Projekt* und ein *Startprojekt*.

* Das *Projekt* wird auch als *Ziel Projekt* bezeichnet, weil die Befehle Dateien hinzufügen oder entfernen. Standardmäßig ist das **Standard Projekt** , das in der **Paket-Manager-Konsole** ausgewählt ist, das Ziel Projekt. Sie können ein anderes Projekt als Ziel Projekt angeben, indem Sie die <nobr>`--project`</nobr> Option verwenden.

* Das *Startprojekt ist das Startprojekt* , das von den Tools erstellt und ausgeführt wird. Die Tools müssen Anwendungscode zur Entwurfszeit ausführen, um Informationen zum Projekt zu erhalten, z. b. die Daten bankverbindungs Zeichenfolge und die Konfiguration des Modells. Standardmäßig ist das **Startprojekt** in **Projektmappen-Explorer** das Startprojekt. Sie können ein anderes Projekt als Startprojekt angeben, indem Sie die <nobr>`--startup-project`</nobr> Option verwenden.

Das Startprojekt und das Ziel Projekt sind häufig das gleiche Projekt. Ein typisches Szenario, bei dem es sich um separate Projekte handelt, sind folgende:

* Die EF Core Kontext-und Entitäts Klassen befinden sich in einer .net Core-Klassenbibliothek.
* Eine .net Core-Konsolen-APP oder-Web-App verweist auf die Klassenbibliothek.

Es ist auch möglich, [Migrations Code in einer Klassenbibliothek zu platzieren, getrennt vom EF Core Kontext](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Andere Ziel-Frameworks

Die Paket-Manager-Konsolen Tools können mit .net Core-oder .NET Framework-Projekten verwendet werden. Apps, die über das EF Core Modell in einer .NET Standard-Klassenbibliothek verfügen, verfügen möglicherweise nicht über ein .net Core-oder .NET Framework-Projekt. Dies gilt z. b. für xamarin-und universelle Windows-Plattform-apps. In solchen Fällen können Sie ein .net Core-oder .NET Framework Konsolen-App-Projekt erstellen, dessen einziger Zweck darin besteht, als Startprojekt für die Tools zu fungieren. Das Projekt kann ein Dummyprojekt ohne echten Code sein &mdash; . es ist nur erforderlich, um ein Ziel für die Tools bereitzustellen.

Warum ist ein Dummyprojekt erforderlich? Wie bereits erwähnt, müssen die Tools Anwendungscode zur Entwurfszeit ausführen. Hierzu müssen Sie die .net Core-oder .NET Framework-Laufzeit verwenden. Wenn sich das EF Core Modell in einem Projekt befindet, das .net Core oder .NET Framework als Ziel hat, wird die Laufzeit von den EF Core Tools aus dem Projekt ausgeliehen. Dies ist nicht möglich, wenn sich das EF Core Modell in einer .NET Standard Klassenbibliothek befindet. Der .NET Standard ist keine tatsächliche .NET-Implementierung. Es handelt sich um eine Spezifikation für eine Reihe von APIs, die von .net-Implementierungen unterstützt werden müssen. Daher ist .NET Standard für die EF Core Tools nicht ausreichend, um Anwendungscode auszuführen. Das Dummyprojekt, das Sie als Startprojekt verwenden, stellt eine konkrete Zielplattform bereit, in die die Tools die .NET Standard Klassenbibliothek laden können.

### <a name="aspnet-core-environment"></a>ASP.net Core Umgebung

Wenn Sie die Umgebung für ASP.net Core Projekte angeben möchten, legen Sie **ENV: ASPNETCORE_ENVIRONMENT** vor dem Ausführen von Befehlen fest.

## <a name="common-parameters"></a>Allgemeine Parameter

In der folgenden Tabelle sind die Parameter aufgeführt, die für alle EF Core-Befehle gelten:

| Parameter                 | BESCHREIBUNG                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Kontext \<String>        | Die `DbContext`-Klasse, die verwendet werden soll. Der Klassenname oder voll qualifiziert mit Namespaces.  Wenn dieser Parameter ausgelassen wird, wird EF Core die Kontext Klasse findet. Wenn mehrere Kontext Klassen vorhanden sind, ist dieser Parameter erforderlich. |
| -Projekt \<String>        | Das Ziel Projekt. Wenn dieser Parameter ausgelassen wird, wird das **Standard Projekt** für die **Paket-Manager-Konsole** als Ziel Projekt verwendet.                                                                             |
| <nobr>-StartupProject</nobr>\<String> | Das Startprojekt. Wenn dieser Parameter ausgelassen wird, wird das **Startprojekt** in Projektmappeneigenschaften als Ziel Projekt verwendet. **Solution properties**                                                                                 |
| -Args \<String>           | Argumente, die an die Anwendung übermittelt werden. In EF Core 5,0 hinzugefügt.                                                                                                                                                           |
| -Verbose                  | Zeigt eine ausführliche Ausgabe an.                                                                                                                                                                                                 |

Verwenden Sie den PowerShell-Befehl, um Hilfe Informationen zu einem Befehl anzuzeigen `Get-Help` .

> [!TIP]
> Die Parameter "Context", "Project" und "StartupProject" unterstützen die Tab-Erweiterung.

## <a name="add-migration"></a>Migration hinzufügen

Fügt eine neue Migration hinzu.

Parameter:

| Parameter                         | BESCHREIBUNG                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name \<String><nobr>       | Der Name der Migration. Dies ist ein Positions Parameter, der erforderlich ist.                                              |
| <nobr>-OutputDir \<String></nobr> | Das Verzeichnis, mit dem die Dateien ausgegeben werden. Pfade sind relativ zum Ziel Projektverzeichnis. Der Standardwert ist "Migrationen". |
| <nobr>-Namespace \<String></nobr> | Der Namespace, der für die generierten Klassen verwendet werden soll. Wird standardmäßig aus dem Ausgabeverzeichnis generiert. In EF Core 5,0 hinzugefügt.  |

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

## <a name="drop-database"></a>Drop-Database

Löscht die Datenbank.

Parameter:

| Parameter | BESCHREIBUNG                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | Zeigen Sie an, welche Datenbank gelöscht werden soll, aber löschen Sie Sie nicht. |

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

## <a name="get-dbcontext"></a>Get-dbcontext

Listet Informationen zu verfügbaren Typen auf und ruft Sie ab `DbContext` .

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

## <a name="get-migration"></a>Get-Migration

Listet verfügbare Migrationen auf. In EF Core 5,0 hinzugefügt.

Parameter:

| Parameter                          | BESCHREIBUNG                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <nobr>-Verbindung \<String></nobr> | Die Verbindungszeichenfolge für die Datenbank. Der Standardwert ist der in "adddbcontext" oder "onkonfiguration" angegebene. |
| -Noconnect                         | Stellen Sie keine Verbindung mit der Datenbank her.                                                                         |

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

## <a name="remove-migration"></a>Remove-Migration

Entfernt die letzte Migration (führt einen Rollback für die Codeänderungen aus, die für die Migration durchgeführt wurden).

Parameter:

| Parameter | BESCHREIBUNG                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | Setzen Sie die Migration zurück (führen Sie ein Rollback der Änderungen aus, die auf die Datenbank angewendet wurden). |

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

## <a name="scaffold-dbcontext"></a>Gerüst-dbcontext

Generiert Code für einen `DbContext` und Entitäts Typen für eine Datenbank. Damit `Scaffold-DbContext` ein Entitätstyp generiert werden kann, muss die Datenbanktabelle über einen Primärschlüssel verfügen.

Parameter:

| Parameter                          | BESCHREIBUNG                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Verbindung \<String></nobr> | Die Verbindungszeichenfolge für die Datenbank. Bei ASP.net Core 2. x-Projekten kann der Wert " *Name = \<name of connection string> *" lauten. In diesem Fall stammt der Name aus den Konfigurations Quellen, die für das Projekt eingerichtet sind. Dies ist ein Positions Parameter, der erforderlich ist. |
| <nobr>-Anbieter \<String></nobr>   | Der zu verwendende Anbieter. In der Regel ist dies der Name des nuget-Pakets, z `Microsoft.EntityFrameworkCore.SqlServer` . b.:. Dies ist ein Positions Parameter, der erforderlich ist.                                                                                           |
| -OutputDir \<String>               | Das Verzeichnis, in dem Dateien abgelegt werden sollen. Pfade sind relativ zum Projektverzeichnis.                                                                                                                                                                                             |
| -Contextdir \<String>              | Das Verzeichnis, in das die Datei eingefügt werden soll `DbContext` . Pfade sind relativ zum Projektverzeichnis.                                                                                                                                                               |
| -Namespace \<String>               | Der Namespace, der für alle generierten Klassen verwendet werden soll. Wird standardmäßig aus dem Stamm Namespace und dem Ausgabeverzeichnis generiert. In EF Core 5,0 hinzugefügt.                                                                                                                           |
| -Contextnamespace \<String>        | Der Namespace, der für die generierte Klasse verwendet werden soll `DbContext` . Hinweis: überschreibt `-Namespace` . In EF Core 5,0 hinzugefügt.                                                                                                                                                           |
| -Kontext \<String>                 | Der Name der `DbContext` zu generierenden Klasse.                                                                                                                                                                                                                          |
| -Schemas \<String[]>               | Die Schemas von Tabellen, für die Entitäts Typen generiert werden sollen. Wenn dieser Parameter ausgelassen wird, werden alle Schemas eingeschlossen.                                                                                                                                                             |
| -Tabellen \<String[]>                | Die Tabellen, für die Entitäts Typen generiert werden sollen. Wenn dieser Parameter ausgelassen wird, werden alle Tabellen eingeschlossen.                                                                                                                                                                         |
| -DataAnnotations                   | Verwenden Sie Attribute, um das Modell zu konfigurieren (sofern möglich). Wenn dieser Parameter ausgelassen wird, wird nur die fließende API verwendet.                                                                                                                                                      |
| -Usedatabasenames                  | Verwenden Sie Tabellen-und Spaltennamen genau so, wie Sie in der Datenbank angezeigt werden. Wenn dieser Parameter ausgelassen wird, werden Datenbanknamen geändert, sodass Sie den c#-namens Stil Konventionen genauer entsprechen.                                                                                       |
| -Force                             | Überschreibt vorhandene Dateien.                                                                                                                                                                                                                                               |
| -Noonkonfigurierung                   | Nicht generieren `DbContext.OnConfiguring` . In EF Core 5,0 hinzugefügt.                                                                                                                                                                                                         |
| -Nopluralize                       | Verwenden Sie nicht das pluralizer-Element. In EF Core 5,0 hinzugefügt.                                                                                                                                                                                                                         |

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

Beispiel:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

Beispiel, bei dem nur ausgewählte Tabellen gerüziert werden und der Kontext in einem separaten Ordner mit einem angegebenen Namen und Namespace erstellt wird:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-dbcontext"></a>Skript-dbcontext

Generiert ein SQL-Skript aus dem dbcontext. Umgeht alle Migrationen. In EF Core 3,0 hinzugefügt.

Parameter:

| Parameter                      | BESCHREIBUNG                      |
| ------------------------------ | -------------------------------- |
| <nobr>-Ausgabe \<String></nobr> | Die Datei, in die das Ergebnis geschrieben werden soll. |

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

## <a name="script-migration"></a>Skript-Migration

Generiert ein SQL-Skript, mit dem alle Änderungen von einer ausgewählten Migration zu einer anderen ausgewählten Migration übernommen werden.

Parameter:

| Parameter                    | BESCHREIBUNG                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-Von*\<String>            | Die Migration wird gestartet. Migrationen können anhand des Namens oder der ID identifiziert werden. Die Zahl 0 (null) ist ein Sonderfall, der *vor der ersten Migration*liegt. Der Standardwert ist 0.                                                              |
| *Bis*\<String>              | Die Beendigung der Migration. Standardmäßig wird die letzte Migration verwendet.                                                                                                                                                                      |
| -Idempotent                  | Generieren Sie ein Skript, das bei jeder Migration in einer Datenbank verwendet werden kann.                                                                                                                                                         |
| <nobr>-Notransactions</nobr> | Generieren Sie keine SQL-Transaktions Anweisungen. In EF Core 5,0 hinzugefügt.                                                                                                                                                           |
| -Ausgabe \<String>            | Die Datei, in die das Ergebnis geschrieben werden soll. Wenn dieser Parameter ausgelassen wird, wird die Datei mit einem generierten Namen im selben Ordner erstellt, in dem die Laufzeitdateien der App erstellt werden, z. b.: */obj/Debug/netcoreapp2.1/ghbkztfz.SQL/*. |

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

> [!TIP]
> Die Parameter to, from und Output unterstützen die Tab-Erweiterung.

Im folgenden Beispiel wird ein Skript für die InitialCreate-Migration mit dem Namen der Migration erstellt.

```powershell
Script-Migration -To InitialCreate
```

Im folgenden Beispiel wird ein Skript für alle Migrationen nach der InitialCreate-Migration mit der Migrations-ID erstellt.

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>Update-Database

Aktualisiert die Datenbank auf die letzte Migration oder eine angegebene Migration.

| Parameter                           | BESCHREIBUNG                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-Migration*\<String></nobr> | Die Ziel Migration. Migrationen können anhand des Namens oder der ID identifiziert werden. Die Zahl 0 (null) ist ein Sonderfall, der *vor der ersten Migration* steht und bewirkt, dass alle Migrationen rückgängig gemacht werden. Wenn keine Migration angegeben ist, wird für den Befehl standardmäßig die letzte Migration verwendet. |
| <nobr>-Verbindung \<String></nobr>  | Die Verbindungszeichenfolge für die Datenbank. Der Standardwert ist der in `AddDbContext` oder angegebene `OnConfiguring` . In EF Core 5,0 hinzugefügt.                                                                                                                                |

Die [allgemeinen Parameter](#common-parameters) sind oben aufgeführt.

> [!TIP]
> Der Migrations Parameter unterstützt die Erweiterung der Registerkarte.

Im folgenden Beispiel werden alle Migrationen wieder hergestellt.

```powershell
Update-Database -Migration 0
```

In den folgenden Beispielen wird die-Datenbank auf eine angegebene Migration aktualisiert. Der erste verwendet den Migrations Namen, der zweite verwendet die Migrations-ID und eine angegebene Verbindung:

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Migrationen](xref:core/managing-schemas/migrations/index)
* [Reverse Engineering](xref:core/managing-schemas/scaffolding)
