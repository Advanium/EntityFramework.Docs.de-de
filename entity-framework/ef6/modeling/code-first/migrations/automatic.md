---
title: Automatisches Code First-Migrationen-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 2713afaf09707b7696e90464aac9945c2d82d274
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415700"
---
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="ff19f-102">Automatisches Code First-Migrationen</span><span class="sxs-lookup"><span data-stu-id="ff19f-102">Automatic Code First Migrations</span></span>
<span data-ttu-id="ff19f-103">Automatische Migrationen ermöglichen es Ihnen, Code First-Migrationen zu verwenden, ohne dass eine Codedatei in Ihrem Projekt für jede von Ihnen vornimmt.</span><span class="sxs-lookup"><span data-stu-id="ff19f-103">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="ff19f-104">Nicht alle Änderungen können automatisch angewendet werden, z. b. für Spalten Umbenennungen ist die Verwendung einer Code basierten Migration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ff19f-104">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="ff19f-105">In diesem Artikel wird davon ausgegangen, dass Sie wissen, wie Code First-Migrationen in einfachen Szenarien verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ff19f-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="ff19f-106">Andernfalls müssen Sie [Code First-Migrationen](~/ef6/modeling/code-first/migrations/index.md) lesen, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="ff19f-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="ff19f-107">Empfehlung für Team Umgebungen</span><span class="sxs-lookup"><span data-stu-id="ff19f-107">Recommendation for Team Environments</span></span>

<span data-ttu-id="ff19f-108">Sie können automatische und Code basierte Migrationen interagieren, dies wird jedoch in Team Entwicklungsszenarien nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-108">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="ff19f-109">Wenn Sie Teil eines Teams von Entwicklern sind, das die Quell Code Verwaltung verwendet, sollten Sie entweder reine automatische Migrationen oder rein Code basierte Migrationen verwenden.</span><span class="sxs-lookup"><span data-stu-id="ff19f-109">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="ff19f-110">Aufgrund der Einschränkungen für automatische Migrationen empfiehlt sich die Verwendung von Code basierten Migrationen in Team Umgebungen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-110">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="ff19f-111">Erstellen eines Ausgangsmodells und einer Ausgangsdatenbank</span><span class="sxs-lookup"><span data-stu-id="ff19f-111">Building an Initial Model & Database</span></span>

<span data-ttu-id="ff19f-112">Bevor Sie Migrationen verwenden können, benötigen Sie ein Projekt und ein Code First-Modell.</span><span class="sxs-lookup"><span data-stu-id="ff19f-112">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="ff19f-113">In dieser exemplarischen Vorgehensweise werden die kanonischen Modelle **Blog** und **Post** verwendet.</span><span class="sxs-lookup"><span data-stu-id="ff19f-113">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="ff19f-114">Erstellen einer neuen **migrationsautomaticdemo** -Konsolenanwendung</span><span class="sxs-lookup"><span data-stu-id="ff19f-114">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="ff19f-115">Fügen Sie dem Projekt die neueste Version des NuGet-Pakets **EntityFramework** hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff19f-115">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="ff19f-116">Wählen Sie \*\*Tools > Bibliotheks-Paket-Manger > Paket-Manager-Konsole&gt; aus.</span><span class="sxs-lookup"><span data-stu-id="ff19f-116">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="ff19f-117">Führen Sie den Befehl **Install-Package EntityFramework** aus.</span><span class="sxs-lookup"><span data-stu-id="ff19f-117">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="ff19f-118">Fügen Sie eine **Model.cs**-Datei mit dem unten aufgeführten Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff19f-118">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="ff19f-119">Mit diesem Code wird eine einzelne **Blog**-Klasse, die das Domänenmodell bildet, und eine **BlogContext**-Klasse, die den EF Code First-Kontext darstellt, definiert.</span><span class="sxs-lookup"><span data-stu-id="ff19f-119">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   <span data-ttu-id="ff19f-120">Mit dem Modell können Sie nun auf die Daten zugreifen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-120">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="ff19f-121">Aktualisieren Sie die **Program.cs**-Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ff19f-121">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsAutomaticDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   <span data-ttu-id="ff19f-122">Führen Sie die Anwendung aus, und Sie werden feststellen, dass eine **migrationsautomaticcodedemo. blogcontext** -Datenbank für Sie erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="ff19f-122">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![Localdb-Datenbank](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="ff19f-124">Aktivieren von Migrationen</span><span class="sxs-lookup"><span data-stu-id="ff19f-124">Enabling Migrations</span></span>

<span data-ttu-id="ff19f-125">Im Folgenden erfahren Sie, wie Sie weitere Änderungen am Modell vornehmen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-125">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="ff19f-126">Fügen Sie der Blog-Klasse eine URL-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff19f-126">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="ff19f-127">Wenn Sie die Anwendung erneut ausführen, erhalten Sie eine InvalidOperationException, die besagt, dass *das Modell, das den Kontext "blogcontext" unterstützt, seit der Erstellung der Datenbank geändert wurde. Verwenden Sie Code First-Migrationen, um die Datenbank zu aktualisieren (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*</span><span class="sxs-lookup"><span data-stu-id="ff19f-127">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="ff19f-128">Beginnen Sie daher mit der Verwendung von Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-128">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="ff19f-129">Da wir automatische Migrationen verwenden möchten, geben wir den Schalter **– enableautomaticmigrationen** an.</span><span class="sxs-lookup"><span data-stu-id="ff19f-129">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="ff19f-130">Führen Sie den Befehl **enable-Migrationen – enableautomaticmigrationen** in der Paket-Manager-Konsole aus. dieser Befehl hat dem Projekt einen **Migrations** Ordner hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ff19f-130">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="ff19f-131">Dieser neue Ordner enthält eine Datei:</span><span class="sxs-lookup"><span data-stu-id="ff19f-131">This new folder contains one file:</span></span>

-   <span data-ttu-id="ff19f-132">**Die Dateikonfigurationsklasse:**</span><span class="sxs-lookup"><span data-stu-id="ff19f-132">**The Configuration class.**</span></span> <span data-ttu-id="ff19f-133">Mit dieser Klasse können Sie das Verhalten von Migrationen für den Kontext konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ff19f-133">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="ff19f-134">Verwenden Sie für diese exemplarische Vorgehensweise die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="ff19f-134">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="ff19f-135">*Da in Ihrem Projekt nur ein einzelner Code First-Kontext vorhanden ist, wurde der Kontexttyp, der für diese Konfiguration gilt, automatisch von „Enable-Migrations“ ausgefüllt.*</span><span class="sxs-lookup"><span data-stu-id="ff19f-135">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="ff19f-136">Ihre erste automatische Migration</span><span class="sxs-lookup"><span data-stu-id="ff19f-136">Your First Automatic Migration</span></span>

<span data-ttu-id="ff19f-137">Code First-Migrationen verfügt über zwei primäre Befehle, die Sie im Folgenden kennenlernen werden:</span><span class="sxs-lookup"><span data-stu-id="ff19f-137">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="ff19f-138">**Add-Migration** erstellt den Entwurf für die nächste Migration basierend auf Änderungen, die Sie seit dem Erstellen der letzten Migration an Ihrem Modell vorgenommen haben.</span><span class="sxs-lookup"><span data-stu-id="ff19f-138">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="ff19f-139">**Update-Database** übernimmt alle ausstehenden Migrationen für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ff19f-139">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="ff19f-140">Wir werden die Verwendung der Add-Migration vermeiden (es sei denn, wir brauchen dies wirklich) und konzentrieren uns darauf, dass Code First-Migrationen die Änderungen automatisch berechnen und anwenden.</span><span class="sxs-lookup"><span data-stu-id="ff19f-140">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="ff19f-141">Verwenden Sie " **Update-Database** ", um Code First-Migrationen, die Änderungen an unser Modell (die neue Eigenschaft " **Blog. ur**l") an die Datenbank zu überführen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-141">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="ff19f-142">Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="ff19f-142">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="ff19f-143">Die **migrationsautomaticdemo. blogcontext** -Datenbank wurde nun aktualisiert und enthält nun die **URL** -Spalte in der **Blogs** -Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff19f-143">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="ff19f-144">Ihre zweite automatische Migration</span><span class="sxs-lookup"><span data-stu-id="ff19f-144">Your Second Automatic Migration</span></span>

<span data-ttu-id="ff19f-145">Nehmen wir eine weitere Änderung vor, und lassen Sie Code First-Migrationen die Änderungen für uns automatisch per Push an die Datenbank überführen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-145">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="ff19f-146">Fügen Sie zudem eine neue **Post**-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff19f-146">Let's also add a new **Post** class</span></span>

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   <span data-ttu-id="ff19f-147">Fügen Sie anschließend der **Blog**-Klasse eine **Posts**-Sammlung hinzu, die das andere Ende der Beziehung zwischen **Blog** und **Post** bilden soll.</span><span class="sxs-lookup"><span data-stu-id="ff19f-147">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="ff19f-148">Verwenden Sie jetzt **Update-Database** , um die Datenbank auf den neuesten Stand zu bringen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-148">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="ff19f-149">Geben Sie dieses Mal das Flag **–Verbose** an, damit das SQL angezeigt wird, mit dem Code First-Migrationen ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ff19f-149">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="ff19f-150">Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –Verbose** aus.</span><span class="sxs-lookup"><span data-stu-id="ff19f-150">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="ff19f-151">Hinzufügen einer Code basierten Migration</span><span class="sxs-lookup"><span data-stu-id="ff19f-151">Adding a Code Based Migration</span></span>

<span data-ttu-id="ff19f-152">Sehen wir uns nun an, wie wir eine Code basierte Migration für verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="ff19f-152">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="ff19f-153">Fügen Sie der **Blog** -Klasse eine **Bewertungs** Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff19f-153">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="ff19f-154">Wir könnten einfach " **Update-Database** " ausführen, um diese Änderungen per Push in die Datenbank zu überführen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-154">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="ff19f-155">Allerdings fügen wir eine **Blogs. Rating** -Spalte hinzu, die keine NULL-Werte zulässt. Wenn in der Tabelle vorhandene Daten vorhanden sind, wird ihr die CLR-Standardeinstellung des Datentyps für die neue Spalte zugewiesen (die Bewertung ist Integer, d. h. **0**).</span><span class="sxs-lookup"><span data-stu-id="ff19f-155">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="ff19f-156">Damit die vorhandenen Spalten der **Blogs**-Tabelle mit einem vernünftigen Rating-Wert beginnen, geben Sie den Standardwert **3** an.</span><span class="sxs-lookup"><span data-stu-id="ff19f-156">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="ff19f-157">Verwenden Sie den Befehl "Add-Migration", um diese Änderung in eine Code basierte Migration zu schreiben, damit wir Sie bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="ff19f-157">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="ff19f-158">Der Befehl " **Add-Migration** " ermöglicht es uns, diesen Migrationen einen Namen zu geben. wir nennen einfach "unsere **addblograting**".</span><span class="sxs-lookup"><span data-stu-id="ff19f-158">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="ff19f-159">Führen Sie den Befehl **Add-Migration addblograting** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="ff19f-159">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="ff19f-160">Im **Migrations** Ordner verfügen wir nun über eine neue Migration von **addblograting** .</span><span class="sxs-lookup"><span data-stu-id="ff19f-160">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="ff19f-161">Der Dateiname der Migration ist mit einem Zeitstempel versehen, um die Reihenfolge zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-161">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="ff19f-162">Wir bearbeiten den generierten Code, um den Standardwert 3 für Blog. Rating (Zeile 10 im folgenden Code) anzugeben.</span><span class="sxs-lookup"><span data-stu-id="ff19f-162">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="ff19f-163">*Die Migration umfasst auch eine Code Behind-Datei, in der einige Metadaten erfasst werden. Diese Metadaten ermöglichen es Code First-Migrationen, die automatischen Migrationen zu replizieren, die wir vor dieser Code basierten Migration durchgeführt haben. Dies ist wichtig, wenn ein anderer Entwickler unsere Migrationen ausführen möchte oder wenn es an der Zeit ist, die Anwendung bereitzustellen.*</span><span class="sxs-lookup"><span data-stu-id="ff19f-163">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

<span data-ttu-id="ff19f-164">Die Migration ist nun fertig bearbeitet. Aktualisieren Sie daher die Datenbank mit **Update-Database**.</span><span class="sxs-lookup"><span data-stu-id="ff19f-164">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="ff19f-165">Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="ff19f-165">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="ff19f-166">Zurück zu automatischen Migrationen</span><span class="sxs-lookup"><span data-stu-id="ff19f-166">Back to Automatic Migrations</span></span>

<span data-ttu-id="ff19f-167">Wir können nun für unsere einfacheren Änderungen wieder zu automatischen Migrationen wechseln.</span><span class="sxs-lookup"><span data-stu-id="ff19f-167">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="ff19f-168">Code First-Migrationen übernimmt die automatische und Code basierte Migration in der richtigen Reihenfolge basierend auf den Metadaten, die Sie in der Code-Behind-Datei für jede Code basierte Migration speichert.</span><span class="sxs-lookup"><span data-stu-id="ff19f-168">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="ff19f-169">Fügen wir dem Modell eine Post. Abstract-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff19f-169">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="ff19f-170">Nun können wir mit **Update-Database** Code First-Migrationen, diese Änderung mithilfe einer automatischen Migration an die Datenbank zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-170">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="ff19f-171">Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="ff19f-171">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="ff19f-172">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="ff19f-172">Summary</span></span>

<span data-ttu-id="ff19f-173">In dieser exemplarischen Vorgehensweise wurde erläutert, wie automatische Migrationen verwendet werden, um Modelländerungen per Push an die Datenbank zu überführen</span><span class="sxs-lookup"><span data-stu-id="ff19f-173">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="ff19f-174">Sie haben auch gesehen, wie Sie Code basierte Migrationen zwischen automatischen Migrationen aufbauen und ausführen können, wenn Sie mehr Kontrolle benötigen.</span><span class="sxs-lookup"><span data-stu-id="ff19f-174">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>
