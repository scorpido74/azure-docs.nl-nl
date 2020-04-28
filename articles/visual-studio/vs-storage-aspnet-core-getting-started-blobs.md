---
title: Aan de slag met Azure Blob-opslag met behulp van Visual Studio (ASP.NET Core)
description: Aan de slag met Azure Blob Storage in een ASP.NET Core-project in Visual Studio, nadat u verbinding hebt gemaakt met een opslag account met behulp van Visual Studio Connected Services
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75980739"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Aan de slag met Azure Blob Storage en Visual Studio Connected Services (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob-opslag is een service waarmee ongestructureerde gegevens in de cloud worden opgeslagen als objecten of blobs. In Blob Storage kan elk type tekst of binaire gegevens, zoals een document, mediabestand of toepassingsinstallatieprogramma, worden opgeslagen. U kunt Blob Storage zien als een vorm van objectopslag. Zie [Inleiding tot Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md)voor meer informatie over Blob Storage.

Deze zelf studie laat zien hoe u ASP.NET Core code kunt schrijven voor een aantal algemene scenario's die gebruikmaken van Blob Storage. Scenario's zijn onder andere het maken van een BLOB-container en het uploaden, weer geven, downloaden en verwijderen van blobs.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Vereisten

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>De ontwikkelomgeving instellen

In deze sectie wordt uitgelegd hoe u de ontwikkel omgeving instelt. Dit omvat het maken van een ASP.NET model-view-controller (MVC), het toevoegen van een verbonden services-verbinding, het toevoegen van een controller en het opgeven van de vereiste naam ruimte-instructies.

### <a name="create-an-aspnet-mvc-app-project"></a>Een ASP.NET MVC-app-project maken

1. Open Visual Studio.

1. Selecteer in het hoofd menu de optie **bestand** > **Nieuw** > **project**.

1. Selecteer in het dialoog venster **Nieuw project** **Web** > **ASP.net core Web Application** > **AspNetCoreStorage**. Selecteer vervolgens **OK**.

    ![Scherm afbeelding van het dialoog venster Nieuw project in Visual Studio](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Selecteer in het dialoog venster **nieuwe ASP.net core webtoepassing** de optie **.net core** > **ASP.net Core 2,0** > **-Webtoepassing (model-view-controller)**. Selecteer vervolgens **OK**.

    ![Scherm afbeelding van het dialoog venster nieuwe ASP.NET Core webtoepassing](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Verbonden services gebruiken om verbinding te maken met een Azure-opslag account

1. Klik in **Solution Explorer**met de rechter muisknop op het project.

2. Selecteer in het context menu de optie**verbonden service** **toevoegen** > .

1. In het dialoog venster **verbonden services** selecteert u **Cloud opslag met Azure Storage**en selecteert **u configureren**.

    ![Scherm opname van het dialoog venster verbonden services](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. Selecteer in het dialoog venster **Azure Storage** het Azure-opslag account dat u wilt gebruiken voor deze zelf studie. Als u een nieuw Azure-opslag account wilt maken, selecteert u **een nieuw opslag account maken**en vult u het formulier in. Nadat u een bestaand opslag account hebt geselecteerd of een nieuwe hebt gemaakt, selecteert u **toevoegen**. Visual Studio installeert het NuGet-pakket voor Azure Storage en een opslag connection string op **appSettings. json**.

> [!TIP]
> Zie [een opslag account maken](../storage/common/storage-account-create.md)voor meer informatie over het maken van een opslag account met de [Azure Portal](https://portal.azure.com).
>
> U kunt ook een opslag account maken met behulp van [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure cli](../storage/common/storage-azure-cli.md)of [Azure Cloud shell](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Een MVC-controller maken 

1. Klik in **Solution Explorer**met de rechter muisknop op **controllers**.

2. Selecteer in het context menu controller **toevoegen** > **Controller**.

    ![Scherm opname van Solution Explorer](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. Selecteer in het dialoog venster **steigers toevoegen** de optie **MVC-controller-leeg**en selecteer **toevoegen**.

    ![Scherm afbeelding van het dialoog venster steigers toevoegen](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. Geef in het dialoog venster **lege MVC-controller toevoegen** de naam van de controller *BlobsController*en selecteer **toevoegen**.

    ![Scherm afbeelding van het dialoog venster lege MVC-controller toevoegen](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Voeg de volgende `using` instructies toe aan `BlobsController.cs` het bestand:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Verbinding maken met een opslag account en een container referentie ophalen

Een BLOB-container is een geneste hiërarchie van blobs en mappen. De rest van de stappen in dit document vereist een verwijzing naar een BLOB-container, zodat de code in zijn eigen methode kan worden gebruikt voor herbruikbaarheid.

Met de volgende stappen maakt u een methode om verbinding te maken met het opslag account met behulp van de connection string in **appSettings. json**. De stappen maken ook een verwijzing naar een container. De instelling connection string in **appSettings. json** krijgt de naam van de `<storageaccountname>_AzureStorageConnectionString`indeling. 

1. Open het `BlobsController.cs`-bestand.

1. Voeg een methode toe met de naam **GetCloudBlobContainer** die een **CloudBlobContainer**retourneert. Zorg ervoor dat u `<storageaccountname>_AzureStorageConnectionString` vervangt door de werkelijke naam van de sleutel in **Web. config**.
    
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
> Hoewel *test-BLOB-container* nog niet bestaat, wordt er door deze code een verwijzing naar gemaakt. Dit is zo dat de container kan worden gemaakt met `CreateIfNotExists` de methode die in de volgende stap wordt weer gegeven.

## <a name="create-a-blob-container"></a>Een blob-container maken

De volgende stappen laten zien hoe u een BLOB-container maakt:

1. Voeg een methode toe `CreateBlobContainer` met de naam `ActionResult`die retourneert een.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Haal een `CloudBlobContainer` object op dat een verwijzing naar de gewenste BLOB-container naam vertegenwoordigt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Roep de `CloudBlobContainer.CreateIfNotExists` methode voor het maken van de container aan, als deze nog niet bestaat. De `CloudBlobContainer.CreateIfNotExists` methode retourneert **True** als de container niet bestaat en is gemaakt. Anders retourneert de methode **False**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. Werk `ViewBag` bij met de naam van de BLOB-container.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Hieronder ziet u de voltooide `CreateBlobContainer` methode:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. Klik in **Solution Explorer**met de rechter muisknop op de map **views** .

2. Selecteer in het context menu de optie**nieuwe map** **toevoegen** > . Noem de nieuwe map- *blobs*. 

1. In **Solution Explorer**vouwt u de map **views** uit en klikt u met de rechter muisknop op **blobs**.

4. Selecteer in het context menu de optie**weer gave** **toevoegen** > .

1. Voer in het dialoog venster **weer gave toevoegen** **CreateBlobContainer** in voor de weergave naam en selecteer **toevoegen**.

1. Open `CreateBlobContainer.cshtml`en wijzig de waarde zodat deze eruitziet als in het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Vouw in **Solution Explorer**de**gedeelde** map **weer gaven** > uit en open `_Layout.cshtml`.

1. Zoek naar de niet-geordende lijst die er als `<ul class="nav navbar-nav">`volgt uitziet:.  Voeg na het `<li>` laatste element in de lijst de volgende HTML toe om een ander navigatie menu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Voer de toepassing uit en selecteer **BLOB-container maken** om de resultaten te zien die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Scherm opname van BLOB-container maken](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Zoals eerder vermeld, retourneert `CloudBlobContainer.CreateIfNotExists` de methode alleen **True** als de container niet bestaat en wordt gemaakt. Als de app wordt uitgevoerd wanneer de container bestaat, retourneert de methode daarom **False**.

## <a name="upload-a-blob-into-a-blob-container"></a>Een BLOB uploaden naar een BLOB-container

Upload bestanden naar die container wanneer de [BLOB-container wordt gemaakt](#create-a-blob-container). In deze sectie wordt uitgelegd hoe u een lokaal bestand naar een BLOB-container uploadt. Bij de stappen wordt ervan uitgegaan dat er een BLOB-container met de naam *test-BLOB-container*is. 

1. Open het `BlobsController.cs`-bestand.

1. Voeg een methode toe `UploadBlob` met de naam die een teken reeks retourneert.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. In de `UploadBlob` -methode haalt u `CloudBlobContainer` een object op dat een verwijzing naar de gewenste BLOB-container naam vertegenwoordigt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure Storage ondersteunt verschillende typen blobs. In deze zelf studie wordt gebruikgemaakt van blok-blobs. Als u een verwijzing naar een blok-BLOB wilt ophalen `CloudBlobContainer.GetBlockBlobReference` , roept u de-methode aan.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > De BLOB-naam maakt deel uit van de URL die wordt gebruikt voor het ophalen van een blob, en kan een wille keurige teken reeks zijn, inclusief de naam van het bestand.

1. Nadat u een BLOB-verwijzing hebt, kunt u er een gegevens stroom naar uploaden door de methode van `UploadFromStream` het BLOB-verwijzings object aan te roepen. De `UploadFromStream` -methode maakt de BLOB als deze niet bestaat, of overschrijft deze als deze wel bestaat. (Wijzig * &lt;file-to-upload>* naar een volledig gekwalificeerd pad naar een bestand dat u wilt uploaden.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    Hieronder ziet u de volledige `UploadBlob` methode (met een volledig gekwalificeerde pad voor het bestand dat moet worden geüpload):

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

1. Vouw in **Solution Explorer**de**gedeelde** map **weer gaven** > uit en open `_Layout.cshtml`.

1. Voeg na het `<li>` laatste element in de lijst de volgende HTML toe om een ander navigatie menu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Voer de toepassing uit en selecteer **BLOB uploaden**. Het woord is *geslaagd.* moet worden weer gegeven.
    
    ![Scherm opname van geslaagde controle](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>De blobs in een BLOB-container weer geven

In deze sectie ziet u hoe u de blobs in een BLOB-container kunt weer geven. De voorbeeld code verwijst naar de *test-BLOB-container* die in de sectie is gemaakt. [Maak een BLOB-container](#create-a-blob-container).

1. Open het `BlobsController.cs`-bestand.

1. Voeg een methode toe `ListBlobs` met de naam `ActionResult`die retourneert een.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. In de `ListBlobs` -methode haalt u `CloudBlobContainer` een object op dat een verwijzing naar de BLOB-container vertegenwoordigt. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Als u de blobs in een BLOB-container wilt weer `CloudBlobContainer.ListBlobsSegmentedAsync` geven, gebruikt u de-methode. De `CloudBlobContainer.ListBlobsSegmentedAsync` methode retourneert een `BlobResultSegment`. Dit object `IListBlobItem` bevat objecten die kunnen worden geconverteerd `CloudBlockBlob`naar `CloudPageBlob`, of `CloudBlobDirectory` objecten. In het volgende code fragment worden alle blobs in een BLOB-container opgesomd. Elke BLOB wordt op basis van het type naar het juiste object gecast. De naam (of URI in het geval van een `CloudBlobDirectory`) wordt toegevoegd aan een lijst.

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
    Hieronder ziet u de voltooide `ListBlobs` methode:

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

1. In **Solution Explorer**vouwt u de map **views** uit en klikt u met de rechter muisknop op **blobs**.

2. Selecteer in het context menu de optie**weer gave** **toevoegen** > .

1. Voer `ListBlobs` in het dialoog venster **weer gave toevoegen** de weergave naam in en selecteer **toevoegen**.

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

1. Vouw in **Solution Explorer**de**gedeelde** map **weer gaven** > uit en open `_Layout.cshtml`.

1. Voeg na het `<li>` laatste element in de lijst de volgende HTML toe om een ander navigatie menu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Voer de toepassing uit en selecteer **lijst-blobs** om de resultaten te zien die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Scherm opname van lijst-blobs](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blobs downloaden

In deze sectie ziet u hoe u een BLOB kunt downloaden. U kunt deze persistent maken voor lokale opslag of de inhoud lezen in een teken reeks. De voorbeeld code verwijst naar de *test-BLOB-container* die in de sectie is gemaakt. [Maak een BLOB-container](#create-a-blob-container).

1. Open het `BlobsController.cs`-bestand.

1. Voeg een methode toe `DownloadBlob` met de naam die een teken reeks retourneert.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. In de `DownloadBlob` -methode haalt u `CloudBlobContainer` een object op dat een verwijzing naar de BLOB-container vertegenwoordigt.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Haal een BLOB-referentie object op door `CloudBlobContainer.GetBlockBlobReference` de methode aan te roepen. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Als u een BLOB wilt downloaden, `CloudBlockBlob.DownloadToStream` gebruikt u de-methode. Met de volgende code wordt de inhoud van een BLOB overgedragen naar een Stream-object. Dit object wordt vervolgens persistent gemaakt naar een lokaal bestand. (Wijzig * &lt;de naam van het lokale bestand>* in de volledig gekwalificeerde bestands naam waarmee wordt aangegeven waar de BLOB moet worden gedownload.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Hieronder ziet u de volledige `ListBlobs` methode (met een volledig gekwalificeerde pad voor het lokale bestand dat wordt gemaakt):
    
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

1. Vouw in **Solution Explorer**de**gedeelde** map **weer gaven** > uit en open `_Layout.cshtml`.

1. Voeg na het `<li>` laatste element in de lijst de volgende HTML toe om een ander navigatie menu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Voer de toepassing uit en selecteer **BLOB downloaden** om de BLOB te downloaden. De blob die is opgegeven `CloudBlobContainer.GetBlockBlobReference` in de methode roept down loads naar de `File.OpenWrite` locatie die is opgegeven in de methode aanroep. De tekst is *voltooid.* moet worden weer gegeven in de browser. 

## <a name="delete-blobs"></a>Blobs verwijderen

De volgende stappen laten zien hoe u een BLOB verwijdert:

1. Open het `BlobsController.cs`-bestand.

1. Voeg een methode toe `DeleteBlob` met de naam die een teken reeks retourneert.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. In de `DeleteBlob` -methode haalt u `CloudBlobContainer` een object op dat een verwijzing naar de BLOB-container vertegenwoordigt.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Haal een BLOB-referentie object op door `CloudBlobContainer.GetBlockBlobReference` de methode aan te roepen. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Als u een BLOB wilt verwijderen, `Delete` gebruikt u de-methode.

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    De voltooide `DeleteBlob` methode moet er als volgt uitzien:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. Vouw in **Solution Explorer**de**gedeelde** map **weer gaven** > uit en open `_Layout.cshtml`.

1. Voeg na het `<li>` laatste element in de lijst de volgende HTML toe om een ander navigatie menu-item toe te voegen:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Voer de toepassing uit en selecteer **BLOB verwijderen** om de BLOB te verwijderen die is `CloudBlobContainer.GetBlockBlobReference` opgegeven in de methode aanroep. De tekst is *voltooid.* moet worden weer gegeven in de browser. Selecteer de knop **terug** in de browser en selecteer vervolgens **lijst-blobs** om te controleren of de BLOB zich niet meer in de container bevindt.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u blobs in Azure Storage kunt opslaan, weer geven en ophalen met behulp van ASP.NET Core. Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.

  * [Aan de slag met Azure Table Storage en Visual Studio Connected Services (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Aan de slag met Azure Queue Storage en Visual Studio Connected Services (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
