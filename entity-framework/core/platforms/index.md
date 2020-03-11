---
title: Unterstützte .NET-Implementierungen – EF Core
author: bricelam
ms.date: 03/03/2020
uid: core/platforms/index
ms.openlocfilehash: 693d4cae85eddf86d01e17084415147c52a008c7
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413065"
---
# <a name="net-implementations-supported-by-ef-core"></a>Von EF Core unterstützte .NET-Implementierungen

Wir möchten, dass EF Core für Entwickler auf allen modernen.NET-Implementierungen zur Verfügung steht, und wir arbeiten immer noch an diesem Ziel. Obwohl die EF Core-Unterstützung für .NET Core durch automatisierte Tests abgedeckt wird und viele Clientanwendungen diese erwiesenermaßen erfolgreich verwendet haben, treten bei Mono, Xamarin und der UWP einige Probleme auf.

## <a name="overview"></a>Übersicht

Die folgende Tabelle enthält Anweisungen zu den einzelnen .NET-Implementierungen:

| EF Core                       | 2.1 und 3.1 |
|:------------------------------|:------------|
| .NET-Standard                 | 2.0         |
| .NET Core                     | 2.0         |
| .NET Framework<sup>(1)</sup>  | 4.7.2       |
| Mono                          | 5.4         |
| Xamarin.iOS<sup>(2)</sup>     | 10.14       |
| Xamarin.Android<sup>(2)</sup> | 8.0         |
| UWP<sup>(3)</sup>             | 10.0.16299  |
| Unity<sup>(4)</sup>           | 2018.1      |

<sup>1</sup> Weitere Informationen finden Sie unten im Abschnitt [.NET Framework](#net-framework).

<sup>(2)</sup> Bei Xamarin wurden Probleme und bekannte Einschränkungen verzeichnet, die die Funktionsweise einiger mit EF Core entwickelten Anwendungen beeinträchtigen können. Überprüfen Sie die Liste der [aktiven Probleme](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) zur Problemumgehung.

<sup>(3)</sup> EF Core 2.0.1 oder höher wird empfohlen. Installieren Sie das [.NET Core UWP 6.x-Paket](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/). Weitere Informationen finden Sie in diesem Artikel im Abschnitt [Universelle Windows-Plattform](#universal-windows-platform).

<sup>(4)</sup> Es gibt Probleme und bekannte Einschränkungen bei Unity. Überprüfen Sie die Liste der [aktiven Probleme](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).

## <a name="net-framework"></a>.NET Framework

.NET Framework-Anwendungen müssen ggf. angepasst werden, um mit der .NET Standard-Bibliothek zu funktionieren:

Bearbeiten Sie die Projektdatei, und stellen Sie sicher, dass der folgende Eintrag in der initialen Eigenschaftengruppe angezeigt wird:

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

Achten Sie bei Testprojekten auch darauf, dass der folgende Eintrag vorhanden ist:

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

Wenn Sie eine ältere Version von Visual Studio verwenden möchten, stellen Sie sicher, dass Sie ein [Upgrade des NuGet-Clients auf Version 3.6.0 durchführen](https://www.nuget.org/downloads), damit dieser mit .NET Standard 2.0-Bibliotheken funktioniert.

Wir empfehlen außerdem die Migration des NuGet-Pakets „packages.config“ zu „PackageReference“, wenn möglich. Fügen Sie der Projektdatei die folgende Eigenschaft hinzu:

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a>Universelle Windows-Plattform

Bei älteren Versionen der EF Core- und .NET-UWP sind zahlreiche Kompatibilitätsprobleme aufgetreten. Dies galt besonders für Anwendungen, die mit der .NET Native-Toolkette kompiliert wurden. Die neue .NET-UWP-Version bietet Unterstützung für .NET Standard 2.0 und .NET Native 2.0, die einen Großteil der zuvor erwähnten Kompatibilitätsprobleme behebt. EF Core 2.0.1 wurde mit der UWP gründlicher getestet, die Tests sind jedoch nicht automatisiert.

Bei Verwendung von EF Core unter UWP:

* Um die Abfrageleistung zu optimieren, vermeiden Sie anonyme Typen in LINQ-Abfragen. Das Bereitstellen einer UWP-Anwendung im App Store erfordert die Kompilierung einer Anwendung mit .NET Native. Abfragen mit anonymen Typen zeigen in .NET Native eine unzureichende Leistung.

* Zur Optimierung der `SaveChanges()`-Leistung verwenden Sie [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy), und implementieren Sie [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) und [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in Ihren Entitätstypen.

## <a name="report-issues"></a>Melden von Problemen

Für alle Kombinationen, die nicht wie erwartet funktionieren, empfehlen wir, neue Issues in der [EF Core-Problemverfolgung](https://github.com/aspnet/entityframeworkcore/issues/new) zu erstellen. Verwenden Sie bei Xamarin-bezogenen Problemen [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) oder [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).
