---
title: 'Snelstartgids: Azure Blob-opslag bibliotheek V12-Xamarin'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 met Xamarin kunt gebruiken om een container en een BLOB in Blob-opslag (object) te maken. Vervolgens leert u hoe u de BLOB kunt downloaden naar uw mobiele apparaat en hoe u alle blobs in een container kunt weer geven.
author: codemillmatt
ms.author: masoucou
ms.date: 05/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e0845e7cdc2ce6dc57ed5a18d263f117f0c2005c
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006242"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Snelstartgids: Azure Blob Storage-client bibliotheek V12 met Xamarin

Ga aan de slag met de Azure Blob Storage-client bibliotheek V12 met Xamarin. Azure Blob Storage is Microsoft's oplossing voor opslag van objecten in de cloud. Volg de stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

Gebruik de Azure Blob Storage-client bibliotheek V12 met Xamarin om het volgende te doen:

* Een container maken
* Een BLOB uploaden naar Azure Storage
* Alle blobs in een container weer geven
* De BLOB downloaden naar uw apparaat
* Een container verwijderen

[API reference documentation](/dotnet/api/azure.storage.blobs) | Voor[beeld](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart) van de API-referentie[bibliotheek broncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs) | 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Visual Studio met [Mobile Development voor .net-workloads](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows) geïnstalleerd of [Visual Studio voor Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>Instellen
    
In deze sectie wordt uitgelegd hoe u een project voorbereidt dat werkt met de Azure Blob Storage-client bibliotheek V12 met Xamarin.
    
### <a name="create-the-project"></a>Het project maken

1. Open Visual Studio en maak een lege formulieren-app.
1. Geef deze de naam: BlobQuickstartV12

### <a name="install-the-package"></a>Het pakket installeren

1. Klik met de rechter muisknop op uw oplossing in het deel venster Solution Explorer en selecteer **NuGet-pakketten beheren voor oplossing**.
1. Zoek naar **Azure. storage. blobs** en installeer de meest recente stabiele versie in alle projecten in uw oplossing.

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de **BlobQuickstartV12** -map:

1. Open het bestand *MainPage. xaml* in uw editor
1. Verwijder alles tussen de `<ContentPage></ContentPage>` elementen en vervang door de onderstaande:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Object model

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslag account
* Een container in het opslagaccount
* Een BLOB in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende .NET-klassen om te communiceren met deze resources:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): met `BlobServiceClient` de klasse kunt u Azure storage resources en BLOB-containers bewerken.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): met `BlobContainerClient` de klasse kunt u Azure Storage containers en de bijbehorende blobs bewerken.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): met `BlobClient` de klasse kunt u Azure Storage blobs bewerken.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): de `BlobDownloadInfo` klasse vertegenwoordigt de eigenschappen en inhoud die worden geretourneerd door het downloaden van een blob.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeld code fragmenten ziet u hoe u de volgende taken kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor .NET in een Xamarin. Forms-app:

* [Variabelen op klasseniveau maken](#create-class-level-variables)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container in een lijst weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Een container verwijderen](#delete-a-container)

### <a name="create-class-level-variables"></a>Variabelen op klasseniveau maken

De onderstaande code declareert verschillende klassen niveau variabelen. Ze moesten communiceren met Azure Blob Storage in de rest van dit voor beeld.

Deze zijn naast de connection string voor het opslag account dat is ingesteld in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) .

Voeg deze code toe als variabelen op klasseniveau in het *MainPage.xaml.cs* -bestand:

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Een container maken

Kies een naam voor de nieuwe container. De onderstaande code voegt een GUID-waarde toe aan de container naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Vervolgens roept u de [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) -methode aan om de container in uw opslag account te maken.

Voeg deze code toe aan het *MainPage.xaml.cs* -bestand:

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende code fragment:

1. Maakt een `MemoryStream` tekst.
1. Hiermee wordt de tekst naar een BLOB geüpload door de functie [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync?view=azure-dotnet#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) van de klasse [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) aan te roepen, waarbij deze zowel de bestands naam is die de variabele `MemoryStream` class niveau en de tekst heeft gedefinieerd. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

Voeg deze code toe aan het *MainPage.xaml.cs* -bestand:

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Vermeld de blobs in de container door de methode [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) aan te roepen. In dit geval is er slechts één BLOB aan de container toegevoegd, zodat de vermelding in de lijst alleen die ene BLOB retourneert.

Voeg deze code toe aan het *MainPage.xaml.cs* -bestand:

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Blobs downloaden

Down load de eerder gemaakte BLOB door de methode [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) aan te roepen. Met de voorbeeld code wordt `Stream` de representatie van de BLOB eerst gekopieerd `MemoryStream` naar a en vervolgens `StreamReader` in een zodat de tekst kan worden weer gegeven.

Voeg deze code toe aan het *MainPage.xaml.cs* -bestand:

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de volledige container te verwijderen met behulp van [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync).

De app vraagt eerst om bevestiging voordat de BLOB en de container worden verwijderd. Dit is een goede kans om te controleren of de resources correct zijn gemaakt voordat ze worden verwijderd.

Voeg deze code toe aan het *MainPage.xaml.cs* -bestand:

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>De code uitvoeren

Wanneer de app wordt gestart, wordt eerst de container gemaakt zoals deze wordt weer gegeven. Vervolgens klikt u op de knoppen om de blobs te uploaden, weer te geven, te downloaden en de container te verwijderen.

Druk op F5 om de app uit te voeren op Windows. Als u de app op Mac wilt uitvoeren, drukt u op CMD + Enter.

De app schrijft na elke bewerking naar het scherm. De uitvoer van de app is vergelijkbaar met het voor beeld hieronder:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Controleer voordat u begint met het opschonen of de uitvoer van de inhoud van de BLOB op het scherm overeenkomt met de geüploade waarde.

Nadat u de waarden hebt geverifieerd, bevestigt u de vraag om de container te verwijderen en voltooit u de demo.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u blobs kunt uploaden, downloaden en vermelden met behulp van de Azure Blob Storage-client bibliotheek V12 met Xamarin.

Als u voor beeld-apps voor Blob-opslag wilt zien, gaat u door naar:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Xamarin-voor beeld](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar [Azure voor mobiele ontwikkel aars](/azure/mobile-apps).
* Zie aan de slag [met Xamarin](/xamarin/get-started/)voor meer informatie over Xamarin.