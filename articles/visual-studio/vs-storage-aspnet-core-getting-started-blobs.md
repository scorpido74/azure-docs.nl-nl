---
title: Aan de slag met Azure Blob-opslag met Visual Studio (ASP.NET Core)
description: Aan de slag met Azure Blob-opslag in een ASP.NET Core-project in Visual Studio, nadat u verbinding hebt gemaakt met een opslagaccount met behulp van visual studio-verbonden services
services: storage
documentationcenter: ''
author: ghogen
manager: jillfra
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff221a32ff6c995d019b13f20ca2c3f9e2027f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980739"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Aan de slag met Azure Blob-opslag en Visual Studio connected services (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob-opslag is een service die ongestructureerde gegevens in de cloud opslaat als objecten of blobs. In Blob Storage kan elk type tekst of binaire gegevens, zoals een document, mediabestand of toepassingsinstallatieprogramma, worden opgeslagen. U kunt Blob Storage zien als een vorm van objectopslag. Zie Inleiding tot Azure [Blob-opslag voor](../storage/blobs/storage-blobs-introduction.md)meer informatie over Blob-opslag.

In deze zelfstudie ziet u hoe u ASP.NET Kerncode schrijft voor een aantal veelvoorkomende scenario's die Blob-opslag gebruiken. Scenario's omvatten het maken van een blobcontainer en het uploaden, aanbieden, downloaden en verwijderen van blobs.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Vereisten

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>De ontwikkelomgeving instellen

Deze sectie loopt door het opzetten van de ontwikkelomgeving. Dit omvat het maken van een ASP.NET MVC-app (Model-View-Controller), het toevoegen van een verbinding met verbonden services, het toevoegen van een controller en het opgeven van de vereiste naamruimterichtlijnen.

### <a name="create-an-aspnet-mvc-app-project"></a>Een ASP.NET MVC-appproject maken

1. Open Visual Studio.

1. Selecteer **Bestand** > **nieuw** > **project**in het hoofdmenu .

1. Selecteer in het dialoogvenster **Nieuw project** de optie **Web** > ASP.NET**Core-webtoepassing** > **AspNetCoreStorage**. Selecteer vervolgens **OK**.

    ![Schermafbeelding van het dialoogvenster Nieuw project van Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Selecteer in het dialoogvenster **Nieuwe ASP.NET Kernwebtoepassing** de optie **.NET Core** > **ASP.NET Core 2.0-webtoepassing** > **(Model-View-Controller).** Selecteer vervolgens **OK**.

    ![Schermafbeelding van het dialoogvenster Nieuwe ASP.NET Kernwebtoepassing](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Verbonden services gebruiken om verbinding te maken met een Azure-opslagaccount

1. Klik in **Solution Explorer**met de rechtermuisknop op het project.

2. Selecteer**Verbonden service** **toevoegen** > in het contextmenu .

1. Selecteer in het dialoogvenster **Verbonden services** de optie **Cloudopslag met Azure Storage**en selecteer Vervolgens **Configureren**.

    ![Schermafbeelding van het dialoogvenster Verbonden services](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Selecteer in het dialoogvenster **Azure Storage** het Azure-opslagaccount dat voor deze zelfstudie moet worden gebruikt. Als u een nieuw Azure-opslagaccount wilt maken, selecteert u **Een nieuw opslagaccount maken**en vult u het formulier in. Nadat u een bestaand opslagaccount hebt geselecteerd of een nieuw opslagaccount hebt gemaakt, selecteert u **Toevoegen**. Visual Studio installeert het NuGet-pakket voor Azure Storage en een tekenreeks voor opslagverbindingen met **appsettings.json**.

> [!TIP]
> Zie [Een opslagaccount maken](../storage/common/storage-account-create.md)voor meer informatie over het maken van een opslagaccount met de [Azure-portal.](https://portal.azure.com)
>
> U ook een opslagaccount maken met [Azure PowerShell,](../storage/common/storage-powershell-guide-full.md) [Azure CLI](../storage/common/storage-azure-cli.md)of Azure [Cloud Shell.](../cloud-shell/overview.md)


### <a name="create-an-mvc-controller"></a>Een MVC-controller maken 

1. Klik in **Solution Explorer**met de rechtermuisknop op **Controllers**.

2. Selecteer**Controller** **toevoegen** > in het contextmenu .

    ![Schermafbeelding van Solution Explorer](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Selecteer **mvc-controller - Leeg**en selecteer **Toevoegen**in het dialoogvenster **Steiger toevoegen** .

    ![Schermafbeelding van het dialoogvenster Steiger toevoegen](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Geef in het dialoogvenster **Lege mvc-controller toevoegen** de naam van de controller *BlobsController*en selecteer **Toevoegen**.

    ![Schermafbeelding van het dialoogvenster Lege mvc-controller toevoegen](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Voeg de `using` volgende `BlobsController.cs` richtlijnen toe aan het bestand:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Verbinding maken met een opslagaccount en een containerreferentie krijgen

Een blobcontainer is een geneste hiërarchie van blobs en mappen. De rest van de stappen in dit document vereisen een verwijzing naar een blobcontainer, zodat code in een eigen methode voor herbruikbaarheid moet worden geplaatst.

Met de volgende stappen u verbinding maken met het opslagaccount met behulp van de verbindingstekenreeks in **appsettings.json**. De stappen maken ook een verwijzing naar een container. De instelling voor verbindingstekenreeksen in **appsettings.json** wordt benoemd met de indeling `<storageaccountname>_AzureStorageConnectionString`. 

1. Open het `BlobsController.cs`-bestand.

1. Voeg een methode met de naam **GetCloudBlobContainer** toe die een **CloudBlobContainer**retourneert. Zorg ervoor `<storageaccountname>_AzureStorageConnectionString` dat u de werkelijke naam van de sleutel in **Web.config**vervangt.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Hoewel *test-blob-container* nog niet bestaat, maakt deze code er een verwijzing naar. Dit is zodat de container `CreateIfNotExists` kan worden gemaakt met de methode die in de volgende stap wordt weergegeven.

## <a name="create-a-blob-container"></a>Een blob-container maken

In de volgende stappen wordt uitgelegd hoe u een blobcontainer maakt:

1. Voeg een `CreateBlobContainer` methode toe `ActionResult`die wordt aangeroepen als een .

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Download `CloudBlobContainer` een object dat een verwijzing naar de gewenste blobcontainernaam vertegenwoordigt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Roep `CloudBlobContainer.CreateIfNotExists` de methode aan om de container te maken, als deze nog niet bestaat. De `CloudBlobContainer.CreateIfNotExists` methode retourneert **true** als de container niet bestaat en wordt gemaakt. Anders retourneert de methode **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Bijwerken `ViewBag` met de naam van de blobcontainer.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    De volgende gegevens `CreateBlobContainer` worden weergegeven:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Klik in **Solution Explorer**met de rechtermuisknop op de map **Weergaven.**

2. Selecteer**Nieuwe map** **toevoegen** > in het contextmenu. Geef de nieuwe map *Blobs*een naam . 

1. Vouw in **Solution Explorer**de map **Weergaven** uit en klik met de rechtermuisknop op **Blobs**.

4. Selecteer**Weergave** **toevoegen** > in het contextmenu .

1. Typ **CreateBlobContainer** voor de weergavenaam in het dialoogvenster **Weergave toevoegen** en selecteer **Toevoegen**.

1. Open `CreateBlobContainer.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Vouw in **Solution Explorer**de**map** `_Layout.cshtml`Gedeelde **weergaven** > uit en open .

1. Kijk voor de ongeordende lijst `<ul class="nav navbar-nav">`die er als volgt uitziet: .  Voeg na `<li>` het laatste element in de lijst de volgende HTML toe om een ander navigatiemenu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Voer de toepassing uit en selecteer **Blobcontainer maken** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Schermafbeelding van Blob-container maken](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Zoals eerder vermeld, retourneert de `CloudBlobContainer.CreateIfNotExists` methode alleen **true** wanneer de container niet bestaat en wordt gemaakt. Als de app wordt uitgevoerd wanneer de container bestaat, retourneert de methode **dus false.**

## <a name="upload-a-blob-into-a-blob-container"></a>Een blob uploaden naar een blobcontainer

Wanneer de [blobcontainer wordt gemaakt,](#create-a-blob-container)uploadt u bestanden naar die container. In deze sectie wordt een lokaal bestand geüpload naar een blobcontainer. De stappen gaan ervan uit dat er een blobcontainer is met de naam *test-blob-container.* 

1. Open het `BlobsController.cs`-bestand.

1. Voeg een `UploadBlob` methode toe die een tekenreeks retourneert.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Download `UploadBlob` binnen de `CloudBlobContainer` methode een object dat een verwijzing naar de gewenste blobcontainernaam vertegenwoordigt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure-opslag ondersteunt verschillende blobtypen. Deze zelfstudie maakt gebruik van blokblobs. Als u een verwijzing naar een `CloudBlobContainer.GetBlockBlobReference` blokblob wilt ophalen, roept u de methode aan.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > De blobnaam maakt deel uit van de URL die wordt gebruikt om een blob op te halen en kan elke tekenreeks zijn, inclusief de naam van het bestand.

1. Nadat er een blobverwijzing is, u elke gegevensstroom naar `UploadFromStream` deze uploaden door de methode van het blob-verwijzingsobject aan te roepen. De `UploadFromStream` methode maakt de blob als deze niet bestaat of overschrijft deze als deze bestaat. (Wijzig * &lt;het>van bestand naar upload* en naar een volledig gekwalificeerd pad naar een bestand dat moet worden geüpload.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Als volgt ziet `UploadBlob` u de voltooide methode (met een volledig gekwalificeerd pad voor het bestand dat moet worden geüpload):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. Vouw in **Solution Explorer**de**map** `_Layout.cshtml`Gedeelde **weergaven** > uit en open .

1. Voeg na `<li>` het laatste element in de lijst de volgende HTML toe om een ander navigatiemenu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Voer de toepassing uit en selecteer **Blob uploaden**. Het woord *succes!* moet verschijnen.
    
    ![Schermafbeelding van succesverificatie](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>De blobs in een blobcontainer weergeven

In deze sectie ziet u hoe u de blobs in een blobcontainer weergeeft. De voorbeeldcode verwijst naar de *test-blob-container* die is gemaakt in de [sectie, Een blobcontainer maken.](#create-a-blob-container)

1. Open het `BlobsController.cs`-bestand.

1. Voeg een `ListBlobs` methode toe `ActionResult`die wordt aangeroepen als een .

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. Download `ListBlobs` binnen de `CloudBlobContainer` methode een object dat een verwijzing naar de blobcontainer vertegenwoordigt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Als u de blobs in een `CloudBlobContainer.ListBlobsSegmentedAsync` blobcontainer wilt weergeven, gebruikt u de methode. De `CloudBlobContainer.ListBlobsSegmentedAsync` methode `BlobResultSegment`geeft een . Dit `IListBlobItem` bevat objecten die `CloudBlockBlob`kunnen `CloudPageBlob`worden `CloudBlobDirectory` gegoten naar , of objecten. In het volgende codefragment worden alle blobs in een blobcontainer opgesomd. Elke blob wordt gegoten naar het juiste object, op basis van het type. De naam (of URI in `CloudBlobDirectory`het geval van a) wordt toegevoegd aan een lijst.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```
    De volgende gegevens `ListBlobs` worden weergegeven:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. Vouw in **Solution Explorer**de map **Weergaven** uit en klik met de rechtermuisknop op **Blobs**.

2. Selecteer**Weergave** **toevoegen** > in het contextmenu .

1. Voer in het dialoogvenster `ListBlobs` Weergave **toevoegen** de optie voor de weergavenaam in en selecteer **Toevoegen**.

1. Open `ListBlobs.cshtml`en vervang de inhoud door de volgende code:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
            <li>@item</li>
        }
    </ul>
    ```

1. Vouw in **Solution Explorer**de**map** `_Layout.cshtml`Gedeelde **weergaven** > uit en open .

1. Voeg na `<li>` het laatste element in de lijst de volgende HTML toe om een ander navigatiemenu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Voer de toepassing uit en selecteer **Blobs lijst** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Schermafbeelding van Lijstblobs](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blobs downloaden

In deze sectie wordt uitgelegd hoe u een blob downloaden. U het aan lokale opslag voortduren of de inhoud in een tekenreeks lezen. De voorbeeldcode verwijst naar de *test-blob-container* die is gemaakt in de [sectie, Een blobcontainer maken.](#create-a-blob-container)

1. Open het `BlobsController.cs`-bestand.

1. Voeg een `DownloadBlob` methode toe die een tekenreeks retourneert.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. Download `DownloadBlob` binnen de `CloudBlobContainer` methode een object dat een verwijzing naar de blobcontainer vertegenwoordigt.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Een blob-referentieobject krijgen `CloudBlobContainer.GetBlockBlobReference` door de methode aan te roepen. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Als u een blob `CloudBlockBlob.DownloadToStream` wilt downloaden, gebruikt u de methode. Met de volgende code wordt de inhoud van een blob overgebracht naar een streamobject. Dat object wordt vervolgens aaneengehouden in een lokaal bestand. (Wijzig * &lt;de naam van het lokale bestand>* in de volledig gekwalificeerde bestandsnaam die aangeeft waar de blob moet worden gedownload.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Als volgt ziet `ListBlobs` u de voltooide methode (met een volledig gekwalificeerd pad voor het lokale bestand dat wordt gemaakt):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. Vouw in **Solution Explorer**de**map** `_Layout.cshtml`Gedeelde **weergaven** > uit en open .

1. Voeg na `<li>` het laatste element in de lijst de volgende HTML toe om een ander navigatiemenu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Voer de toepassing uit en selecteer **Blob downloaden** om de blob te downloaden. De blob die `CloudBlobContainer.GetBlockBlobReference` is opgegeven in de `File.OpenWrite` methodeaanroep, wordt gedownload naar de locatie die is opgegeven in de methodeaanroep. De tekst *succes!* moet worden weergegeven in de browser. 

## <a name="delete-blobs"></a>Blobs verwijderen

In de volgende stappen wordt uitgelegd hoe u een blob verwijdert:

1. Open het `BlobsController.cs`-bestand.

1. Voeg een `DeleteBlob` methode toe die een tekenreeks retourneert.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. Download `DeleteBlob` binnen de `CloudBlobContainer` methode een object dat een verwijzing naar de blobcontainer vertegenwoordigt.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Een blob-referentieobject krijgen `CloudBlobContainer.GetBlockBlobReference` door de methode aan te roepen. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Als u een blob `Delete` wilt verwijderen, gebruikt u de methode.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    De `DeleteBlob` voltooide methode moet als volgt worden weergegeven:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. Vouw in **Solution Explorer**de**map** `_Layout.cshtml`Gedeelde **weergaven** > uit en open .

1. Voeg na `<li>` het laatste element in de lijst de volgende HTML toe om een ander navigatiemenu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Voer de toepassing uit en selecteer **Blob** verwijderen `CloudBlobContainer.GetBlockBlobReference` om de blob te verwijderen die is opgegeven in de methodeaanroep. De tekst *succes!* moet worden weergegeven in de browser. Selecteer de knop **Vorige van** de browser en selecteer **Blobs lijst** om te controleren of de blob niet meer in de container zit.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u blobs in Azure Storage opslaan, aanbieden en ophalen met behulp van ASP.NET Core. Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.

  * [Aan de slag met Azure Table storage en Visual Studio connected services (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Aan de slag met Azure Queue-opslag en Visual Studio connected services (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
