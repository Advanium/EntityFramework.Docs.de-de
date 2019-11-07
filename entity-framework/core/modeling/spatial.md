---
title: Räumliche Daten-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 335d4f3a601624f7c994b7dcacefe4ef6798beb3
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655613"
---
# <a name="spatial-data"></a><span data-ttu-id="74804-102">Räumliche Daten</span><span class="sxs-lookup"><span data-stu-id="74804-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="74804-103">Diese Funktion wurde in EF Core 2,2 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="74804-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="74804-104">Räumliche Daten repräsentieren den physischen Speicherort und die Form von Objekten.</span><span class="sxs-lookup"><span data-stu-id="74804-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="74804-105">Viele Datenbanken unterstützen diese Art von Daten, damit Sie zusammen mit anderen Daten indiziert und abgefragt werden können.</span><span class="sxs-lookup"><span data-stu-id="74804-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="74804-106">Häufige Szenarien umfassen das Abfragen von Objekten in einer bestimmten Entfernung von einem Speicherort oder das Auswählen des Objekts, dessen Rahmen eine bestimmte Position enthält.</span><span class="sxs-lookup"><span data-stu-id="74804-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="74804-107">EF Core unterstützt die Zuordnung räumlicher Datentypen mithilfe der räumlichen Bibliothek [nettopologysuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="74804-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="74804-108">Installation</span><span class="sxs-lookup"><span data-stu-id="74804-108">Installing</span></span>

<span data-ttu-id="74804-109">Um räumliche Daten mit EF Core verwenden zu können, müssen Sie das entsprechende unterstützende nuget-Paket installieren.</span><span class="sxs-lookup"><span data-stu-id="74804-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="74804-110">Welches Paket installiert werden muss, hängt vom verwendeten Anbieter ab.</span><span class="sxs-lookup"><span data-stu-id="74804-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="74804-111">EF Core Anbieter</span><span class="sxs-lookup"><span data-stu-id="74804-111">EF Core Provider</span></span>                        | <span data-ttu-id="74804-112">Räumliches nuget-Paket</span><span class="sxs-lookup"><span data-stu-id="74804-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="74804-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="74804-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="74804-114">Microsoft. entityframeworkcore. SqlServer. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="74804-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="74804-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="74804-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="74804-116">Microsoft. entityframeworkcore. sqlite. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="74804-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="74804-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="74804-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="74804-118">Nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="74804-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="74804-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="74804-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="74804-120">Npgsql. entityframeworkcore. PostgreSQL. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="74804-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="74804-121">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="74804-121">Reverse engineering</span></span>

<span data-ttu-id="74804-122">Die räumlichen nuget-Pakete ermöglichen auch das [Reverse Engineering](../managing-schemas/scaffolding.md) von Modellen mit räumlichen Eigenschaften. Sie müssen das Paket jedoch ***vor*** dem Ausführen `Scaffold-DbContext` oder `dotnet ef dbcontext scaffold`installieren.</span><span class="sxs-lookup"><span data-stu-id="74804-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="74804-123">Wenn Sie dies nicht tun, erhalten Sie Warnungen, wenn Sie keine Typzuordnungen für die Spalten finden und die Spalten übersprungen werden.</span><span class="sxs-lookup"><span data-stu-id="74804-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="74804-124">Nettopologysuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="74804-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="74804-125">Nettopologysuite ist eine räumliche Bibliothek für .net.</span><span class="sxs-lookup"><span data-stu-id="74804-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="74804-126">EF Core ermöglicht die Zuordnung von räumlichen Datentypen in der Datenbank mithilfe von NTS-Typen in Ihrem Modell.</span><span class="sxs-lookup"><span data-stu-id="74804-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="74804-127">Um die Zuordnung räumlicher Typen über NTS zu ermöglichen, müssen Sie die usenettopologysuite-Methode für den dbcontext Options-Generator des Anbieters abrufen.</span><span class="sxs-lookup"><span data-stu-id="74804-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="74804-128">Beispielsweise können Sie mit SQL Server wie folgt bezeichnen.</span><span class="sxs-lookup"><span data-stu-id="74804-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="74804-129">Es gibt mehrere räumliche Datentypen.</span><span class="sxs-lookup"><span data-stu-id="74804-129">There are several spatial data types.</span></span> <span data-ttu-id="74804-130">Welcher Typ Sie verwenden, hängt von den Formen der Formen ab, die Sie zulassen möchten.</span><span class="sxs-lookup"><span data-stu-id="74804-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="74804-131">Hier ist die Hierarchie der NTS-Typen, die Sie für Eigenschaften im Modell verwenden können.</span><span class="sxs-lookup"><span data-stu-id="74804-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="74804-132">Sie befinden sich im `NetTopologySuite.Geometries`-Namespace.</span><span class="sxs-lookup"><span data-stu-id="74804-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="74804-133">Et</span><span class="sxs-lookup"><span data-stu-id="74804-133">Geometry</span></span>
  * <span data-ttu-id="74804-134">Punkt</span><span class="sxs-lookup"><span data-stu-id="74804-134">Point</span></span>
  * <span data-ttu-id="74804-135">LineString</span><span class="sxs-lookup"><span data-stu-id="74804-135">LineString</span></span>
  * <span data-ttu-id="74804-136">Polygon</span><span class="sxs-lookup"><span data-stu-id="74804-136">Polygon</span></span>
  * <span data-ttu-id="74804-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="74804-137">GeometryCollection</span></span>
    * <span data-ttu-id="74804-138">Multipoint</span><span class="sxs-lookup"><span data-stu-id="74804-138">MultiPoint</span></span>
    * <span data-ttu-id="74804-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="74804-139">MultiLineString</span></span>
    * <span data-ttu-id="74804-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="74804-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="74804-141">Circularstring, compoundcurve und curepolygon werden von NTS nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="74804-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="74804-142">Mit dem basisgeometry-Typ kann jeder Typ von Form von der-Eigenschaft angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="74804-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="74804-143">Die folgenden Entitäts Klassen können verwendet werden, um Tabellen in der [Beispieldatenbank Wide World Importern](https://go.microsoft.com/fwlink/?LinkID=800630)zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="74804-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a><span data-ttu-id="74804-144">Erstellen von Werten</span><span class="sxs-lookup"><span data-stu-id="74804-144">Creating values</span></span>

<span data-ttu-id="74804-145">Sie können Konstruktoren zum Erstellen von Geometry-Objekten verwenden. Allerdings empfiehlt es sich, stattdessen eine Geometry-Factory zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="74804-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="74804-146">Auf diese Weise können Sie ein Standardmäßiges SRID angeben (das von den Koordinaten verwendete räumliche Verweis System) und Ihnen die Kontrolle über erweiterte Dinge wie das Genauigkeits Modell (verwendet während der Berechnungen) und die Koordinaten Sequenz (bestimmt, welche ordinates--Dimensionen und Measures--sind verfügbar).</span><span class="sxs-lookup"><span data-stu-id="74804-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="74804-147">4326 bezieht sich auf WGS 84, einen in GPS und anderen geografischen Systemen verwendeten Standard.</span><span class="sxs-lookup"><span data-stu-id="74804-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="74804-148">Längen-und Breitengrad</span><span class="sxs-lookup"><span data-stu-id="74804-148">Longitude and Latitude</span></span>

<span data-ttu-id="74804-149">Die Koordinaten in den NTS sind X-und Y-Werte.</span><span class="sxs-lookup"><span data-stu-id="74804-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="74804-150">Um Längen-und Breitengrad darzustellen, verwenden Sie X für Längengrad und Y für Breitengrad.</span><span class="sxs-lookup"><span data-stu-id="74804-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="74804-151">Beachten Sie, dass **sich diese** Werte nicht im `latitude, longitude` Format befinden, in dem normalerweise diese Werte angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="74804-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="74804-152">SRID wird bei Client Vorgängen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="74804-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="74804-153">NTS ignoriert SRID-Werte während des Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="74804-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="74804-154">Es wird von einem planaren Koordinatensystem ausgegangen.</span><span class="sxs-lookup"><span data-stu-id="74804-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="74804-155">Dies bedeutet Folgendes: Wenn Sie Koordinaten in Bezug auf Längengrad und Breitengrad angeben, werden einige vom Client ausgewertete Werte wie "Distance", "length" und "Area" in Grad und nicht in "Meter" angegeben.</span><span class="sxs-lookup"><span data-stu-id="74804-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="74804-156">Wenn Sie aussagekräftigere Werte benötigen, müssen Sie die Koordinaten zunächst mithilfe einer Bibliothek wie [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) auf ein anderes Koordinatensystem projizieren, bevor Sie diese Werte berechnen.</span><span class="sxs-lookup"><span data-stu-id="74804-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="74804-157">Wenn ein Vorgang vom Server durch EF Core über SQL ausgewertet wird, wird die Einheit des Ergebnisses von der Datenbank bestimmt.</span><span class="sxs-lookup"><span data-stu-id="74804-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="74804-158">Im folgenden finden Sie ein Beispiel für die Verwendung von ProjNet4GeoAPI, um den Abstand zwischen zwei Städten zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="74804-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a><span data-ttu-id="74804-159">Abfrage von Daten</span><span class="sxs-lookup"><span data-stu-id="74804-159">Querying Data</span></span>

<span data-ttu-id="74804-160">In LINQ werden die NTS-Methoden und-Eigenschaften, die als Datenbankfunktionen verfügbar sind, in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="74804-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="74804-161">Beispielsweise werden die Distance-Methode und die-Methode in den folgenden Abfragen übersetzt.</span><span class="sxs-lookup"><span data-stu-id="74804-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="74804-162">Die Tabelle am Ende dieses Artikels zeigt, welche Member von verschiedenen EF Core Anbietern unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="74804-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="74804-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="74804-163">SQL Server</span></span>

<span data-ttu-id="74804-164">Wenn Sie SQL Server verwenden, müssen Sie einige zusätzliche Punkte beachten.</span><span class="sxs-lookup"><span data-stu-id="74804-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="74804-165">Geography oder Geometry</span><span class="sxs-lookup"><span data-stu-id="74804-165">Geography or geometry</span></span>

<span data-ttu-id="74804-166">Standardmäßig werden räumliche Eigenschaften `geography` Spalten in SQL Server zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="74804-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="74804-167">Um `geometry`zu verwenden, [Konfigurieren Sie den Spaltentyp](xref:core/modeling/relational/data-types) in Ihrem Modell.</span><span class="sxs-lookup"><span data-stu-id="74804-167">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="74804-168">Polygon-Polygon Ringe</span><span class="sxs-lookup"><span data-stu-id="74804-168">Geography polygon rings</span></span>

<span data-ttu-id="74804-169">Wenn Sie den Spaltentyp `geography` verwenden, setzt SQL Server zusätzliche Anforderungen für den äußeren Ring (oder die Shell) und die inneren Ringe (oder Löcher).</span><span class="sxs-lookup"><span data-stu-id="74804-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="74804-170">Der äußere Ring muss gegen den Uhrzeigersinn und die inneren Ringe im Uhrzeigersinn ausgerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="74804-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="74804-171">NTS überprüft dies, bevor Werte an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="74804-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="74804-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="74804-172">FullGlobe</span></span>

<span data-ttu-id="74804-173">SQL Server weist einen nicht standardmäßigen Geometry-Typ auf, der den vollständigen Globus darstellt, wenn der `geography` Spaltentyp verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="74804-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="74804-174">Außerdem bietet es eine Möglichkeit, Polygone auf der ganzen Welt (ohne äußeren Ring) darzustellen.</span><span class="sxs-lookup"><span data-stu-id="74804-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="74804-175">Keines dieser beiden wird von NTS unterstützt.</span><span class="sxs-lookup"><span data-stu-id="74804-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="74804-176">Fullglobe und Polygone, die darauf basieren, werden von NTS nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="74804-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="74804-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="74804-177">SQLite</span></span>

<span data-ttu-id="74804-178">Hier finden Sie einige zusätzliche Informationen für diejenigen, die SQLite verwenden.</span><span class="sxs-lookup"><span data-stu-id="74804-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="74804-179">Installieren von spatialite</span><span class="sxs-lookup"><span data-stu-id="74804-179">Installing SpatiaLite</span></span>

<span data-ttu-id="74804-180">Unter Windows wird die native mod_spatialite-Bibliothek als eine nuget-Paketabhängigkeit verteilt.</span><span class="sxs-lookup"><span data-stu-id="74804-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="74804-181">Andere Plattformen müssen Sie separat installieren.</span><span class="sxs-lookup"><span data-stu-id="74804-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="74804-182">Dies erfolgt in der Regel mithilfe eines Softwarepaket-Managers.</span><span class="sxs-lookup"><span data-stu-id="74804-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="74804-183">Beispielsweise können Sie apt unter Ubuntu und Homebrew unter MacOS verwenden.</span><span class="sxs-lookup"><span data-stu-id="74804-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="74804-184">Konfigurieren von SRID</span><span class="sxs-lookup"><span data-stu-id="74804-184">Configuring SRID</span></span>

<span data-ttu-id="74804-185">In spatialite müssen Spalten eine SRID pro Spalte angeben.</span><span class="sxs-lookup"><span data-stu-id="74804-185">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="74804-186">Der Standard-SRID ist `0`.</span><span class="sxs-lookup"><span data-stu-id="74804-186">The default SRID is `0`.</span></span> <span data-ttu-id="74804-187">Geben Sie einen anderen SRID mithilfe der forsqlitehassrid-Methode an.</span><span class="sxs-lookup"><span data-stu-id="74804-187">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="74804-188">Dimension</span><span class="sxs-lookup"><span data-stu-id="74804-188">Dimension</span></span>

<span data-ttu-id="74804-189">Ähnlich wie SRID wird auch die Dimension (oder ordinates) einer Spalte als Teil der Spalte angegeben.</span><span class="sxs-lookup"><span data-stu-id="74804-189">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="74804-190">Die Standard Ordinate sind X und Y. Aktivieren Sie zusätzliche Ordinate (Z und M) mithilfe der forsqlitehasdimension-Methode.</span><span class="sxs-lookup"><span data-stu-id="74804-190">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="74804-191">Übersetzte Vorgänge</span><span class="sxs-lookup"><span data-stu-id="74804-191">Translated Operations</span></span>

<span data-ttu-id="74804-192">Diese Tabelle zeigt, welche NTS-Elemente von den einzelnen EF Core Anbietern in SQL übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="74804-192">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="74804-193">Nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="74804-193">NetTopologySuite</span></span> | <span data-ttu-id="74804-194">SQL Server (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-194">SQL Server (geometry)</span></span> | <span data-ttu-id="74804-195">SQL Server (Geografie)</span><span class="sxs-lookup"><span data-stu-id="74804-195">SQL Server (geography)</span></span> | <span data-ttu-id="74804-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="74804-196">SQLite</span></span> | <span data-ttu-id="74804-197">Npgsql</span><span class="sxs-lookup"><span data-stu-id="74804-197">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="74804-198">Geometry. Area</span><span class="sxs-lookup"><span data-stu-id="74804-198">Geometry.Area</span></span> | <span data-ttu-id="74804-199">✔</span><span class="sxs-lookup"><span data-stu-id="74804-199">✔</span></span> | <span data-ttu-id="74804-200">✔</span><span class="sxs-lookup"><span data-stu-id="74804-200">✔</span></span> | <span data-ttu-id="74804-201">✔</span><span class="sxs-lookup"><span data-stu-id="74804-201">✔</span></span> | <span data-ttu-id="74804-202">✔</span><span class="sxs-lookup"><span data-stu-id="74804-202">✔</span></span>
<span data-ttu-id="74804-203">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="74804-203">Geometry.AsBinary()</span></span> | <span data-ttu-id="74804-204">✔</span><span class="sxs-lookup"><span data-stu-id="74804-204">✔</span></span> | <span data-ttu-id="74804-205">✔</span><span class="sxs-lookup"><span data-stu-id="74804-205">✔</span></span> | <span data-ttu-id="74804-206">✔</span><span class="sxs-lookup"><span data-stu-id="74804-206">✔</span></span> | <span data-ttu-id="74804-207">✔</span><span class="sxs-lookup"><span data-stu-id="74804-207">✔</span></span>
<span data-ttu-id="74804-208">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="74804-208">Geometry.AsText()</span></span> | <span data-ttu-id="74804-209">✔</span><span class="sxs-lookup"><span data-stu-id="74804-209">✔</span></span> | <span data-ttu-id="74804-210">✔</span><span class="sxs-lookup"><span data-stu-id="74804-210">✔</span></span> | <span data-ttu-id="74804-211">✔</span><span class="sxs-lookup"><span data-stu-id="74804-211">✔</span></span> | <span data-ttu-id="74804-212">✔</span><span class="sxs-lookup"><span data-stu-id="74804-212">✔</span></span>
<span data-ttu-id="74804-213">Geometry. Boundary</span><span class="sxs-lookup"><span data-stu-id="74804-213">Geometry.Boundary</span></span> | <span data-ttu-id="74804-214">✔</span><span class="sxs-lookup"><span data-stu-id="74804-214">✔</span></span> | | <span data-ttu-id="74804-215">✔</span><span class="sxs-lookup"><span data-stu-id="74804-215">✔</span></span> | <span data-ttu-id="74804-216">✔</span><span class="sxs-lookup"><span data-stu-id="74804-216">✔</span></span>
<span data-ttu-id="74804-217">Geometry. Buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="74804-217">Geometry.Buffer(double)</span></span> | <span data-ttu-id="74804-218">✔</span><span class="sxs-lookup"><span data-stu-id="74804-218">✔</span></span> | <span data-ttu-id="74804-219">✔</span><span class="sxs-lookup"><span data-stu-id="74804-219">✔</span></span> | <span data-ttu-id="74804-220">✔</span><span class="sxs-lookup"><span data-stu-id="74804-220">✔</span></span> | <span data-ttu-id="74804-221">✔</span><span class="sxs-lookup"><span data-stu-id="74804-221">✔</span></span>
<span data-ttu-id="74804-222">Geometry. Buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="74804-222">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="74804-223">✔</span><span class="sxs-lookup"><span data-stu-id="74804-223">✔</span></span> | <span data-ttu-id="74804-224">✔</span><span class="sxs-lookup"><span data-stu-id="74804-224">✔</span></span>
<span data-ttu-id="74804-225">Geometry. Centroid</span><span class="sxs-lookup"><span data-stu-id="74804-225">Geometry.Centroid</span></span> | <span data-ttu-id="74804-226">✔</span><span class="sxs-lookup"><span data-stu-id="74804-226">✔</span></span> | | <span data-ttu-id="74804-227">✔</span><span class="sxs-lookup"><span data-stu-id="74804-227">✔</span></span> | <span data-ttu-id="74804-228">✔</span><span class="sxs-lookup"><span data-stu-id="74804-228">✔</span></span>
<span data-ttu-id="74804-229">Geometry. enthält (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="74804-230">✔</span><span class="sxs-lookup"><span data-stu-id="74804-230">✔</span></span> | <span data-ttu-id="74804-231">✔</span><span class="sxs-lookup"><span data-stu-id="74804-231">✔</span></span> | <span data-ttu-id="74804-232">✔</span><span class="sxs-lookup"><span data-stu-id="74804-232">✔</span></span> | <span data-ttu-id="74804-233">✔</span><span class="sxs-lookup"><span data-stu-id="74804-233">✔</span></span>
<span data-ttu-id="74804-234">Geometry. config-Hülle ()</span><span class="sxs-lookup"><span data-stu-id="74804-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="74804-235">✔</span><span class="sxs-lookup"><span data-stu-id="74804-235">✔</span></span> | <span data-ttu-id="74804-236">✔</span><span class="sxs-lookup"><span data-stu-id="74804-236">✔</span></span> | <span data-ttu-id="74804-237">✔</span><span class="sxs-lookup"><span data-stu-id="74804-237">✔</span></span> | <span data-ttu-id="74804-238">✔</span><span class="sxs-lookup"><span data-stu-id="74804-238">✔</span></span>
<span data-ttu-id="74804-239">Geometry. coveredby (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="74804-240">✔</span><span class="sxs-lookup"><span data-stu-id="74804-240">✔</span></span> | <span data-ttu-id="74804-241">✔</span><span class="sxs-lookup"><span data-stu-id="74804-241">✔</span></span>
<span data-ttu-id="74804-242">Geometry. Cover (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="74804-243">✔</span><span class="sxs-lookup"><span data-stu-id="74804-243">✔</span></span> | <span data-ttu-id="74804-244">✔</span><span class="sxs-lookup"><span data-stu-id="74804-244">✔</span></span>
<span data-ttu-id="74804-245">Geometry. Kreuze (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="74804-246">✔</span><span class="sxs-lookup"><span data-stu-id="74804-246">✔</span></span> | | <span data-ttu-id="74804-247">✔</span><span class="sxs-lookup"><span data-stu-id="74804-247">✔</span></span> | <span data-ttu-id="74804-248">✔</span><span class="sxs-lookup"><span data-stu-id="74804-248">✔</span></span>
<span data-ttu-id="74804-249">Geometry. Difference (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="74804-250">✔</span><span class="sxs-lookup"><span data-stu-id="74804-250">✔</span></span> | <span data-ttu-id="74804-251">✔</span><span class="sxs-lookup"><span data-stu-id="74804-251">✔</span></span> | <span data-ttu-id="74804-252">✔</span><span class="sxs-lookup"><span data-stu-id="74804-252">✔</span></span> | <span data-ttu-id="74804-253">✔</span><span class="sxs-lookup"><span data-stu-id="74804-253">✔</span></span>
<span data-ttu-id="74804-254">Geometry. Dimension</span><span class="sxs-lookup"><span data-stu-id="74804-254">Geometry.Dimension</span></span> | <span data-ttu-id="74804-255">✔</span><span class="sxs-lookup"><span data-stu-id="74804-255">✔</span></span> | <span data-ttu-id="74804-256">✔</span><span class="sxs-lookup"><span data-stu-id="74804-256">✔</span></span> | <span data-ttu-id="74804-257">✔</span><span class="sxs-lookup"><span data-stu-id="74804-257">✔</span></span> | <span data-ttu-id="74804-258">✔</span><span class="sxs-lookup"><span data-stu-id="74804-258">✔</span></span>
<span data-ttu-id="74804-259">Geometry. disjoint (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="74804-260">✔</span><span class="sxs-lookup"><span data-stu-id="74804-260">✔</span></span> | <span data-ttu-id="74804-261">✔</span><span class="sxs-lookup"><span data-stu-id="74804-261">✔</span></span> | <span data-ttu-id="74804-262">✔</span><span class="sxs-lookup"><span data-stu-id="74804-262">✔</span></span> | <span data-ttu-id="74804-263">✔</span><span class="sxs-lookup"><span data-stu-id="74804-263">✔</span></span>
<span data-ttu-id="74804-264">Geometry. distance (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="74804-265">✔</span><span class="sxs-lookup"><span data-stu-id="74804-265">✔</span></span> | <span data-ttu-id="74804-266">✔</span><span class="sxs-lookup"><span data-stu-id="74804-266">✔</span></span> | <span data-ttu-id="74804-267">✔</span><span class="sxs-lookup"><span data-stu-id="74804-267">✔</span></span> | <span data-ttu-id="74804-268">✔</span><span class="sxs-lookup"><span data-stu-id="74804-268">✔</span></span>
<span data-ttu-id="74804-269">Geometry. Umschlag</span><span class="sxs-lookup"><span data-stu-id="74804-269">Geometry.Envelope</span></span> | <span data-ttu-id="74804-270">✔</span><span class="sxs-lookup"><span data-stu-id="74804-270">✔</span></span> | | <span data-ttu-id="74804-271">✔</span><span class="sxs-lookup"><span data-stu-id="74804-271">✔</span></span> | <span data-ttu-id="74804-272">✔</span><span class="sxs-lookup"><span data-stu-id="74804-272">✔</span></span>
<span data-ttu-id="74804-273">Geometry. EqualsExact (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="74804-274">✔</span><span class="sxs-lookup"><span data-stu-id="74804-274">✔</span></span>
<span data-ttu-id="74804-275">Geometry. equalstopologisch (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="74804-276">✔</span><span class="sxs-lookup"><span data-stu-id="74804-276">✔</span></span> | <span data-ttu-id="74804-277">✔</span><span class="sxs-lookup"><span data-stu-id="74804-277">✔</span></span> | <span data-ttu-id="74804-278">✔</span><span class="sxs-lookup"><span data-stu-id="74804-278">✔</span></span> | <span data-ttu-id="74804-279">✔</span><span class="sxs-lookup"><span data-stu-id="74804-279">✔</span></span>
<span data-ttu-id="74804-280">Geometry. geometrytype</span><span class="sxs-lookup"><span data-stu-id="74804-280">Geometry.GeometryType</span></span> | <span data-ttu-id="74804-281">✔</span><span class="sxs-lookup"><span data-stu-id="74804-281">✔</span></span> | <span data-ttu-id="74804-282">✔</span><span class="sxs-lookup"><span data-stu-id="74804-282">✔</span></span> | <span data-ttu-id="74804-283">✔</span><span class="sxs-lookup"><span data-stu-id="74804-283">✔</span></span> | <span data-ttu-id="74804-284">✔</span><span class="sxs-lookup"><span data-stu-id="74804-284">✔</span></span>
<span data-ttu-id="74804-285">Geometry. getgeometryn (int)</span><span class="sxs-lookup"><span data-stu-id="74804-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="74804-286">✔</span><span class="sxs-lookup"><span data-stu-id="74804-286">✔</span></span> | | <span data-ttu-id="74804-287">✔</span><span class="sxs-lookup"><span data-stu-id="74804-287">✔</span></span> | <span data-ttu-id="74804-288">✔</span><span class="sxs-lookup"><span data-stu-id="74804-288">✔</span></span>
<span data-ttu-id="74804-289">Geometry. interiorpoint</span><span class="sxs-lookup"><span data-stu-id="74804-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="74804-290">✔</span><span class="sxs-lookup"><span data-stu-id="74804-290">✔</span></span> | | <span data-ttu-id="74804-291">✔</span><span class="sxs-lookup"><span data-stu-id="74804-291">✔</span></span> | <span data-ttu-id="74804-292">✔</span><span class="sxs-lookup"><span data-stu-id="74804-292">✔</span></span>
<span data-ttu-id="74804-293">Geometry. Schnittmenge (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-293">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="74804-294">✔</span><span class="sxs-lookup"><span data-stu-id="74804-294">✔</span></span> | <span data-ttu-id="74804-295">✔</span><span class="sxs-lookup"><span data-stu-id="74804-295">✔</span></span> | <span data-ttu-id="74804-296">✔</span><span class="sxs-lookup"><span data-stu-id="74804-296">✔</span></span> | <span data-ttu-id="74804-297">✔</span><span class="sxs-lookup"><span data-stu-id="74804-297">✔</span></span>
<span data-ttu-id="74804-298">Geometry. intersekten (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-298">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="74804-299">✔</span><span class="sxs-lookup"><span data-stu-id="74804-299">✔</span></span> | <span data-ttu-id="74804-300">✔</span><span class="sxs-lookup"><span data-stu-id="74804-300">✔</span></span> | <span data-ttu-id="74804-301">✔</span><span class="sxs-lookup"><span data-stu-id="74804-301">✔</span></span> | <span data-ttu-id="74804-302">✔</span><span class="sxs-lookup"><span data-stu-id="74804-302">✔</span></span>
<span data-ttu-id="74804-303">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="74804-303">Geometry.IsEmpty</span></span> | <span data-ttu-id="74804-304">✔</span><span class="sxs-lookup"><span data-stu-id="74804-304">✔</span></span> | <span data-ttu-id="74804-305">✔</span><span class="sxs-lookup"><span data-stu-id="74804-305">✔</span></span> | <span data-ttu-id="74804-306">✔</span><span class="sxs-lookup"><span data-stu-id="74804-306">✔</span></span> | <span data-ttu-id="74804-307">✔</span><span class="sxs-lookup"><span data-stu-id="74804-307">✔</span></span>
<span data-ttu-id="74804-308">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="74804-308">Geometry.IsSimple</span></span> | <span data-ttu-id="74804-309">✔</span><span class="sxs-lookup"><span data-stu-id="74804-309">✔</span></span> | | <span data-ttu-id="74804-310">✔</span><span class="sxs-lookup"><span data-stu-id="74804-310">✔</span></span> | <span data-ttu-id="74804-311">✔</span><span class="sxs-lookup"><span data-stu-id="74804-311">✔</span></span>
<span data-ttu-id="74804-312">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="74804-312">Geometry.IsValid</span></span> | <span data-ttu-id="74804-313">✔</span><span class="sxs-lookup"><span data-stu-id="74804-313">✔</span></span> | <span data-ttu-id="74804-314">✔</span><span class="sxs-lookup"><span data-stu-id="74804-314">✔</span></span> | <span data-ttu-id="74804-315">✔</span><span class="sxs-lookup"><span data-stu-id="74804-315">✔</span></span> | <span data-ttu-id="74804-316">✔</span><span class="sxs-lookup"><span data-stu-id="74804-316">✔</span></span>
<span data-ttu-id="74804-317">Geometry. iswithindistance (Geometry, Double)</span><span class="sxs-lookup"><span data-stu-id="74804-317">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="74804-318">✔</span><span class="sxs-lookup"><span data-stu-id="74804-318">✔</span></span> | | <span data-ttu-id="74804-319">✔</span><span class="sxs-lookup"><span data-stu-id="74804-319">✔</span></span> | <span data-ttu-id="74804-320">✔</span><span class="sxs-lookup"><span data-stu-id="74804-320">✔</span></span>
<span data-ttu-id="74804-321">Geometry. length</span><span class="sxs-lookup"><span data-stu-id="74804-321">Geometry.Length</span></span> | <span data-ttu-id="74804-322">✔</span><span class="sxs-lookup"><span data-stu-id="74804-322">✔</span></span> | <span data-ttu-id="74804-323">✔</span><span class="sxs-lookup"><span data-stu-id="74804-323">✔</span></span> | <span data-ttu-id="74804-324">✔</span><span class="sxs-lookup"><span data-stu-id="74804-324">✔</span></span> | <span data-ttu-id="74804-325">✔</span><span class="sxs-lookup"><span data-stu-id="74804-325">✔</span></span>
<span data-ttu-id="74804-326">Geometry. numgeometries</span><span class="sxs-lookup"><span data-stu-id="74804-326">Geometry.NumGeometries</span></span> | <span data-ttu-id="74804-327">✔</span><span class="sxs-lookup"><span data-stu-id="74804-327">✔</span></span> | <span data-ttu-id="74804-328">✔</span><span class="sxs-lookup"><span data-stu-id="74804-328">✔</span></span> | <span data-ttu-id="74804-329">✔</span><span class="sxs-lookup"><span data-stu-id="74804-329">✔</span></span> | <span data-ttu-id="74804-330">✔</span><span class="sxs-lookup"><span data-stu-id="74804-330">✔</span></span>
<span data-ttu-id="74804-331">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="74804-331">Geometry.NumPoints</span></span> | <span data-ttu-id="74804-332">✔</span><span class="sxs-lookup"><span data-stu-id="74804-332">✔</span></span> | <span data-ttu-id="74804-333">✔</span><span class="sxs-lookup"><span data-stu-id="74804-333">✔</span></span> | <span data-ttu-id="74804-334">✔</span><span class="sxs-lookup"><span data-stu-id="74804-334">✔</span></span> | <span data-ttu-id="74804-335">✔</span><span class="sxs-lookup"><span data-stu-id="74804-335">✔</span></span>
<span data-ttu-id="74804-336">Geometry. ogcgeometrytype</span><span class="sxs-lookup"><span data-stu-id="74804-336">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="74804-337">✔</span><span class="sxs-lookup"><span data-stu-id="74804-337">✔</span></span> | <span data-ttu-id="74804-338">✔</span><span class="sxs-lookup"><span data-stu-id="74804-338">✔</span></span> | <span data-ttu-id="74804-339">✔</span><span class="sxs-lookup"><span data-stu-id="74804-339">✔</span></span> | <span data-ttu-id="74804-340">✔</span><span class="sxs-lookup"><span data-stu-id="74804-340">✔</span></span>
<span data-ttu-id="74804-341">Geometry. Überlappung (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-341">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="74804-342">✔</span><span class="sxs-lookup"><span data-stu-id="74804-342">✔</span></span> | <span data-ttu-id="74804-343">✔</span><span class="sxs-lookup"><span data-stu-id="74804-343">✔</span></span> | <span data-ttu-id="74804-344">✔</span><span class="sxs-lookup"><span data-stu-id="74804-344">✔</span></span> | <span data-ttu-id="74804-345">✔</span><span class="sxs-lookup"><span data-stu-id="74804-345">✔</span></span>
<span data-ttu-id="74804-346">Geometry. pointonsurface</span><span class="sxs-lookup"><span data-stu-id="74804-346">Geometry.PointOnSurface</span></span> | <span data-ttu-id="74804-347">✔</span><span class="sxs-lookup"><span data-stu-id="74804-347">✔</span></span> | | <span data-ttu-id="74804-348">✔</span><span class="sxs-lookup"><span data-stu-id="74804-348">✔</span></span> | <span data-ttu-id="74804-349">✔</span><span class="sxs-lookup"><span data-stu-id="74804-349">✔</span></span>
<span data-ttu-id="74804-350">Geometry. in Beziehung (Geometrie, Zeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="74804-350">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="74804-351">✔</span><span class="sxs-lookup"><span data-stu-id="74804-351">✔</span></span> | | <span data-ttu-id="74804-352">✔</span><span class="sxs-lookup"><span data-stu-id="74804-352">✔</span></span> | <span data-ttu-id="74804-353">✔</span><span class="sxs-lookup"><span data-stu-id="74804-353">✔</span></span>
<span data-ttu-id="74804-354">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="74804-354">Geometry.Reverse()</span></span> | | | <span data-ttu-id="74804-355">✔</span><span class="sxs-lookup"><span data-stu-id="74804-355">✔</span></span> | <span data-ttu-id="74804-356">✔</span><span class="sxs-lookup"><span data-stu-id="74804-356">✔</span></span>
<span data-ttu-id="74804-357">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="74804-357">Geometry.SRID</span></span> | <span data-ttu-id="74804-358">✔</span><span class="sxs-lookup"><span data-stu-id="74804-358">✔</span></span> | <span data-ttu-id="74804-359">✔</span><span class="sxs-lookup"><span data-stu-id="74804-359">✔</span></span> | <span data-ttu-id="74804-360">✔</span><span class="sxs-lookup"><span data-stu-id="74804-360">✔</span></span> | <span data-ttu-id="74804-361">✔</span><span class="sxs-lookup"><span data-stu-id="74804-361">✔</span></span>
<span data-ttu-id="74804-362">Geometry. symmetricdifference (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-362">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="74804-363">✔</span><span class="sxs-lookup"><span data-stu-id="74804-363">✔</span></span> | <span data-ttu-id="74804-364">✔</span><span class="sxs-lookup"><span data-stu-id="74804-364">✔</span></span> | <span data-ttu-id="74804-365">✔</span><span class="sxs-lookup"><span data-stu-id="74804-365">✔</span></span> | <span data-ttu-id="74804-366">✔</span><span class="sxs-lookup"><span data-stu-id="74804-366">✔</span></span>
<span data-ttu-id="74804-367">Geometry. $ Binary ()</span><span class="sxs-lookup"><span data-stu-id="74804-367">Geometry.ToBinary()</span></span> | <span data-ttu-id="74804-368">✔</span><span class="sxs-lookup"><span data-stu-id="74804-368">✔</span></span> | <span data-ttu-id="74804-369">✔</span><span class="sxs-lookup"><span data-stu-id="74804-369">✔</span></span> | <span data-ttu-id="74804-370">✔</span><span class="sxs-lookup"><span data-stu-id="74804-370">✔</span></span> | <span data-ttu-id="74804-371">✔</span><span class="sxs-lookup"><span data-stu-id="74804-371">✔</span></span>
<span data-ttu-id="74804-372">Geometry.-Text ()</span><span class="sxs-lookup"><span data-stu-id="74804-372">Geometry.ToText()</span></span> | <span data-ttu-id="74804-373">✔</span><span class="sxs-lookup"><span data-stu-id="74804-373">✔</span></span> | <span data-ttu-id="74804-374">✔</span><span class="sxs-lookup"><span data-stu-id="74804-374">✔</span></span> | <span data-ttu-id="74804-375">✔</span><span class="sxs-lookup"><span data-stu-id="74804-375">✔</span></span> | <span data-ttu-id="74804-376">✔</span><span class="sxs-lookup"><span data-stu-id="74804-376">✔</span></span>
<span data-ttu-id="74804-377">Geometry. touches (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-377">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="74804-378">✔</span><span class="sxs-lookup"><span data-stu-id="74804-378">✔</span></span> | | <span data-ttu-id="74804-379">✔</span><span class="sxs-lookup"><span data-stu-id="74804-379">✔</span></span> | <span data-ttu-id="74804-380">✔</span><span class="sxs-lookup"><span data-stu-id="74804-380">✔</span></span>
<span data-ttu-id="74804-381">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="74804-381">Geometry.Union()</span></span> | | | <span data-ttu-id="74804-382">✔</span><span class="sxs-lookup"><span data-stu-id="74804-382">✔</span></span> | <span data-ttu-id="74804-383">✔</span><span class="sxs-lookup"><span data-stu-id="74804-383">✔</span></span>
<span data-ttu-id="74804-384">Geometry. Union (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="74804-384">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="74804-385">✔</span><span class="sxs-lookup"><span data-stu-id="74804-385">✔</span></span> | <span data-ttu-id="74804-386">✔</span><span class="sxs-lookup"><span data-stu-id="74804-386">✔</span></span> | <span data-ttu-id="74804-387">✔</span><span class="sxs-lookup"><span data-stu-id="74804-387">✔</span></span> | <span data-ttu-id="74804-388">✔</span><span class="sxs-lookup"><span data-stu-id="74804-388">✔</span></span>
<span data-ttu-id="74804-389">Geometry. within (Geometry)</span><span class="sxs-lookup"><span data-stu-id="74804-389">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="74804-390">✔</span><span class="sxs-lookup"><span data-stu-id="74804-390">✔</span></span> | <span data-ttu-id="74804-391">✔</span><span class="sxs-lookup"><span data-stu-id="74804-391">✔</span></span> | <span data-ttu-id="74804-392">✔</span><span class="sxs-lookup"><span data-stu-id="74804-392">✔</span></span> | <span data-ttu-id="74804-393">✔</span><span class="sxs-lookup"><span data-stu-id="74804-393">✔</span></span>
<span data-ttu-id="74804-394">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="74804-394">GeometryCollection.Count</span></span> | <span data-ttu-id="74804-395">✔</span><span class="sxs-lookup"><span data-stu-id="74804-395">✔</span></span> | <span data-ttu-id="74804-396">✔</span><span class="sxs-lookup"><span data-stu-id="74804-396">✔</span></span> | <span data-ttu-id="74804-397">✔</span><span class="sxs-lookup"><span data-stu-id="74804-397">✔</span></span> | <span data-ttu-id="74804-398">✔</span><span class="sxs-lookup"><span data-stu-id="74804-398">✔</span></span>
<span data-ttu-id="74804-399">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="74804-399">GeometryCollection[int]</span></span> | <span data-ttu-id="74804-400">✔</span><span class="sxs-lookup"><span data-stu-id="74804-400">✔</span></span> | <span data-ttu-id="74804-401">✔</span><span class="sxs-lookup"><span data-stu-id="74804-401">✔</span></span> | <span data-ttu-id="74804-402">✔</span><span class="sxs-lookup"><span data-stu-id="74804-402">✔</span></span> | <span data-ttu-id="74804-403">✔</span><span class="sxs-lookup"><span data-stu-id="74804-403">✔</span></span>
<span data-ttu-id="74804-404">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="74804-404">LineString.Count</span></span> | <span data-ttu-id="74804-405">✔</span><span class="sxs-lookup"><span data-stu-id="74804-405">✔</span></span> | <span data-ttu-id="74804-406">✔</span><span class="sxs-lookup"><span data-stu-id="74804-406">✔</span></span> | <span data-ttu-id="74804-407">✔</span><span class="sxs-lookup"><span data-stu-id="74804-407">✔</span></span> | <span data-ttu-id="74804-408">✔</span><span class="sxs-lookup"><span data-stu-id="74804-408">✔</span></span>
<span data-ttu-id="74804-409">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="74804-409">LineString.EndPoint</span></span> | <span data-ttu-id="74804-410">✔</span><span class="sxs-lookup"><span data-stu-id="74804-410">✔</span></span> | <span data-ttu-id="74804-411">✔</span><span class="sxs-lookup"><span data-stu-id="74804-411">✔</span></span> | <span data-ttu-id="74804-412">✔</span><span class="sxs-lookup"><span data-stu-id="74804-412">✔</span></span> | <span data-ttu-id="74804-413">✔</span><span class="sxs-lookup"><span data-stu-id="74804-413">✔</span></span>
<span data-ttu-id="74804-414">LineString. getpointn (int)</span><span class="sxs-lookup"><span data-stu-id="74804-414">LineString.GetPointN(int)</span></span> | <span data-ttu-id="74804-415">✔</span><span class="sxs-lookup"><span data-stu-id="74804-415">✔</span></span> | <span data-ttu-id="74804-416">✔</span><span class="sxs-lookup"><span data-stu-id="74804-416">✔</span></span> | <span data-ttu-id="74804-417">✔</span><span class="sxs-lookup"><span data-stu-id="74804-417">✔</span></span> | <span data-ttu-id="74804-418">✔</span><span class="sxs-lookup"><span data-stu-id="74804-418">✔</span></span>
<span data-ttu-id="74804-419">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="74804-419">LineString.IsClosed</span></span> | <span data-ttu-id="74804-420">✔</span><span class="sxs-lookup"><span data-stu-id="74804-420">✔</span></span> | <span data-ttu-id="74804-421">✔</span><span class="sxs-lookup"><span data-stu-id="74804-421">✔</span></span> | <span data-ttu-id="74804-422">✔</span><span class="sxs-lookup"><span data-stu-id="74804-422">✔</span></span> | <span data-ttu-id="74804-423">✔</span><span class="sxs-lookup"><span data-stu-id="74804-423">✔</span></span>
<span data-ttu-id="74804-424">LineString. isring</span><span class="sxs-lookup"><span data-stu-id="74804-424">LineString.IsRing</span></span> | <span data-ttu-id="74804-425">✔</span><span class="sxs-lookup"><span data-stu-id="74804-425">✔</span></span> | | <span data-ttu-id="74804-426">✔</span><span class="sxs-lookup"><span data-stu-id="74804-426">✔</span></span> | <span data-ttu-id="74804-427">✔</span><span class="sxs-lookup"><span data-stu-id="74804-427">✔</span></span>
<span data-ttu-id="74804-428">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="74804-428">LineString.StartPoint</span></span> | <span data-ttu-id="74804-429">✔</span><span class="sxs-lookup"><span data-stu-id="74804-429">✔</span></span> | <span data-ttu-id="74804-430">✔</span><span class="sxs-lookup"><span data-stu-id="74804-430">✔</span></span> | <span data-ttu-id="74804-431">✔</span><span class="sxs-lookup"><span data-stu-id="74804-431">✔</span></span> | <span data-ttu-id="74804-432">✔</span><span class="sxs-lookup"><span data-stu-id="74804-432">✔</span></span>
<span data-ttu-id="74804-433">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="74804-433">MultiLineString.IsClosed</span></span> | <span data-ttu-id="74804-434">✔</span><span class="sxs-lookup"><span data-stu-id="74804-434">✔</span></span> | <span data-ttu-id="74804-435">✔</span><span class="sxs-lookup"><span data-stu-id="74804-435">✔</span></span> | <span data-ttu-id="74804-436">✔</span><span class="sxs-lookup"><span data-stu-id="74804-436">✔</span></span> | <span data-ttu-id="74804-437">✔</span><span class="sxs-lookup"><span data-stu-id="74804-437">✔</span></span>
<span data-ttu-id="74804-438">Punkt. M</span><span class="sxs-lookup"><span data-stu-id="74804-438">Point.M</span></span> | <span data-ttu-id="74804-439">✔</span><span class="sxs-lookup"><span data-stu-id="74804-439">✔</span></span> | <span data-ttu-id="74804-440">✔</span><span class="sxs-lookup"><span data-stu-id="74804-440">✔</span></span> | <span data-ttu-id="74804-441">✔</span><span class="sxs-lookup"><span data-stu-id="74804-441">✔</span></span> | <span data-ttu-id="74804-442">✔</span><span class="sxs-lookup"><span data-stu-id="74804-442">✔</span></span>
<span data-ttu-id="74804-443">Point. X</span><span class="sxs-lookup"><span data-stu-id="74804-443">Point.X</span></span> | <span data-ttu-id="74804-444">✔</span><span class="sxs-lookup"><span data-stu-id="74804-444">✔</span></span> | <span data-ttu-id="74804-445">✔</span><span class="sxs-lookup"><span data-stu-id="74804-445">✔</span></span> | <span data-ttu-id="74804-446">✔</span><span class="sxs-lookup"><span data-stu-id="74804-446">✔</span></span> | <span data-ttu-id="74804-447">✔</span><span class="sxs-lookup"><span data-stu-id="74804-447">✔</span></span>
<span data-ttu-id="74804-448">Punkt. Y</span><span class="sxs-lookup"><span data-stu-id="74804-448">Point.Y</span></span> | <span data-ttu-id="74804-449">✔</span><span class="sxs-lookup"><span data-stu-id="74804-449">✔</span></span> | <span data-ttu-id="74804-450">✔</span><span class="sxs-lookup"><span data-stu-id="74804-450">✔</span></span> | <span data-ttu-id="74804-451">✔</span><span class="sxs-lookup"><span data-stu-id="74804-451">✔</span></span> | <span data-ttu-id="74804-452">✔</span><span class="sxs-lookup"><span data-stu-id="74804-452">✔</span></span>
<span data-ttu-id="74804-453">Punkt. Z</span><span class="sxs-lookup"><span data-stu-id="74804-453">Point.Z</span></span> | <span data-ttu-id="74804-454">✔</span><span class="sxs-lookup"><span data-stu-id="74804-454">✔</span></span> | <span data-ttu-id="74804-455">✔</span><span class="sxs-lookup"><span data-stu-id="74804-455">✔</span></span> | <span data-ttu-id="74804-456">✔</span><span class="sxs-lookup"><span data-stu-id="74804-456">✔</span></span> | <span data-ttu-id="74804-457">✔</span><span class="sxs-lookup"><span data-stu-id="74804-457">✔</span></span>
<span data-ttu-id="74804-458">Polygon. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="74804-458">Polygon.ExteriorRing</span></span> | <span data-ttu-id="74804-459">✔</span><span class="sxs-lookup"><span data-stu-id="74804-459">✔</span></span> | <span data-ttu-id="74804-460">✔</span><span class="sxs-lookup"><span data-stu-id="74804-460">✔</span></span> | <span data-ttu-id="74804-461">✔</span><span class="sxs-lookup"><span data-stu-id="74804-461">✔</span></span> | <span data-ttu-id="74804-462">✔</span><span class="sxs-lookup"><span data-stu-id="74804-462">✔</span></span>
<span data-ttu-id="74804-463">Polygon. getinteriorringn (int)</span><span class="sxs-lookup"><span data-stu-id="74804-463">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="74804-464">✔</span><span class="sxs-lookup"><span data-stu-id="74804-464">✔</span></span> | <span data-ttu-id="74804-465">✔</span><span class="sxs-lookup"><span data-stu-id="74804-465">✔</span></span> | <span data-ttu-id="74804-466">✔</span><span class="sxs-lookup"><span data-stu-id="74804-466">✔</span></span> | <span data-ttu-id="74804-467">✔</span><span class="sxs-lookup"><span data-stu-id="74804-467">✔</span></span>
<span data-ttu-id="74804-468">Polygon. numinteriorrings</span><span class="sxs-lookup"><span data-stu-id="74804-468">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="74804-469">✔</span><span class="sxs-lookup"><span data-stu-id="74804-469">✔</span></span> | <span data-ttu-id="74804-470">✔</span><span class="sxs-lookup"><span data-stu-id="74804-470">✔</span></span> | <span data-ttu-id="74804-471">✔</span><span class="sxs-lookup"><span data-stu-id="74804-471">✔</span></span> | <span data-ttu-id="74804-472">✔</span><span class="sxs-lookup"><span data-stu-id="74804-472">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="74804-473">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="74804-473">Additional resources</span></span>

* [<span data-ttu-id="74804-474">Räumliche Daten in SQL Server</span><span class="sxs-lookup"><span data-stu-id="74804-474">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="74804-475">Spatialite-Homepage</span><span class="sxs-lookup"><span data-stu-id="74804-475">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="74804-476">Räumliche Dokumentation zu npgsql</span><span class="sxs-lookup"><span data-stu-id="74804-476">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="74804-477">PostGIS-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="74804-477">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
