---
title: "Pomelo-MySQL-Datenbankanbieter – EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d0198c04-d30d-4419-98f8-a54690cea3c8
ms.technology: entity-framework-core
uid: core/providers/pomelo/index
ms.openlocfilehash: 9ddcda1ae7b058c01937867817e2666b97e7f37a
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2017
---
# <a name="pomelo-ef-core-database-provider-for-mysql"></a><span data-ttu-id="3929f-102">Pomelo-EF Core-Datenbankanbieter für MySQL</span><span class="sxs-lookup"><span data-stu-id="3929f-102">Pomelo EF Core Database Provider for MySQL</span></span>

<span data-ttu-id="3929f-103">Dieser Datenbankanbieter ermöglicht die Verwendung von Entity Framework Core mit MySQL.</span><span class="sxs-lookup"><span data-stu-id="3929f-103">This database provider allows Entity Framework Core to be used with MySQL.</span></span> <span data-ttu-id="3929f-104">Der Anbieter wird im Rahmen des [Pomelo Foundation-Projekts](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) verwaltet.</span><span class="sxs-lookup"><span data-stu-id="3929f-104">The provider is maintained as part of the [Pomelo Foundation Project](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql).</span></span>

> [!NOTE]  
>
> <span data-ttu-id="3929f-105">Dieser Anbieter wird nicht im Rahmen des Entity Framework Core-Projekts verwaltet.</span><span class="sxs-lookup"><span data-stu-id="3929f-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="3929f-106">Wenn Sie einen Drittanbieter in Betracht ziehen, sollten Sie Aspekte wie Qualität, Lizenzierung und Support auswerten, um sicherzustellen, dass dieser Ihren Anforderungen entspricht.</span><span class="sxs-lookup"><span data-stu-id="3929f-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="3929f-107">Installieren</span><span class="sxs-lookup"><span data-stu-id="3929f-107">Install</span></span>

<span data-ttu-id="3929f-108">Installieren Sie das [NuGet-Paket „Pomelo.EntityFrameworkCore.MySql“](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql).</span><span class="sxs-lookup"><span data-stu-id="3929f-108">Install the [Pomelo.EntityFrameworkCore.MySql NuGet package](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql).</span></span>

``` powershell
Install-Package Pomelo.EntityFrameworkCore.MySql
```

## <a name="get-started"></a><span data-ttu-id="3929f-109">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="3929f-109">Get Started</span></span>

<span data-ttu-id="3929f-110">Die folgenden Ressourcen unterstützen Sie bei den ersten Schritten mit diesem Anbieter:</span><span class="sxs-lookup"><span data-stu-id="3929f-110">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="3929f-111">Dokumentation zu den ersten Schritten</span><span class="sxs-lookup"><span data-stu-id="3929f-111">Getting started documentation</span></span>](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md#getting-started)

* [<span data-ttu-id="3929f-112">Yuuko Blog-Beispielanwendung</span><span class="sxs-lookup"><span data-stu-id="3929f-112">Yuuko Blog sample application</span></span>](https://github.com/PomeloFoundation/YuukoBlog)

## <a name="supported-database-engines"></a><span data-ttu-id="3929f-113">Unterstützte Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="3929f-113">Supported Database Engines</span></span>

* <span data-ttu-id="3929f-114">MySQL</span><span class="sxs-lookup"><span data-stu-id="3929f-114">MySQL</span></span>
* <span data-ttu-id="3929f-115">MariaDB</span><span class="sxs-lookup"><span data-stu-id="3929f-115">MariaDB</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="3929f-116">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="3929f-116">Supported Platforms</span></span>

* <span data-ttu-id="3929f-117">.NET Framework (4.5.1 oder höher)</span><span class="sxs-lookup"><span data-stu-id="3929f-117">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="3929f-118">.NET Core</span><span class="sxs-lookup"><span data-stu-id="3929f-118">.NET Core</span></span>

* <span data-ttu-id="3929f-119">Mono (4.2.0 oder höher)</span><span class="sxs-lookup"><span data-stu-id="3929f-119">Mono (4.2.0 onwards)</span></span>