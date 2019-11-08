---
title: 'Snelstartgids: Azure Blob Storage-client bibliotheek voor .NET'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek voor .NET gebruikt om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ee95ccd30d0f2eddcca7327dc4e5b60a3efe7e64
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825404"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Snelstartgids: Azure Blob Storage-client bibliotheek V11 voor .NET

Ga aan de slag met de Azure Blob Storage-client bibliotheek V11 voor .NET. Azure Blob Storage is de object opslag oplossing van micro soft voor de Cloud. Volg de stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

Gebruik de Azure Blob Storage-client bibliotheek voor .NET voor het volgende:

* Een container maken
* Machtigingen voor een container instellen
* Een BLOB maken in Azure Storage
* De BLOB downloaden naar uw lokale computer
* Alle blobs in een container weer geven
* Een container verwijderen

[API-referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | - [bibliotheek bron code](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | -voor [beelden](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidige [.net core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturings systeem. Zorg ervoor dat u de SDK en niet de runtime ophaalt.

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-client bibliotheek voor .NET.

### <a name="create-the-project"></a>Het project maken

Maak eerst een .NET core-toepassing met de naam *BLOB-Quick*start.

1. Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam *BLOB-Quick*start. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Ga naar de zojuist gemaakte map *BLOB-Quick* start en bouw de app om te controleren of alles goed is.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

De verwachte uitvoer van de build moet er ongeveer als volgt uitzien:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Blob Storage-client bibliotheek voor .NET-pakket met behulp van de `dotnet add package` opdracht.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de projectmap:

1. Open het *Program.cs* -bestand in de editor
2. De `Console.WriteLine`-instructie verwijderen
3. `using`-instructies toevoegen
4. Een `ProcessAsync` methode maken waarbij de hoofd code van het voor beeld wordt opgeslagen
5. De `ProcessAsync` methode asynchroon aanroepen vanuit `Main`

Hier volgt de code:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static void Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            // Run the examples asynchronously, wait for the results before proceeding
            ProcessAsync().GetAwaiter().GetResult();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

Wanneer de voorbeeld toepassing een aanvraag indient om Azure Storage, moet deze worden geautoriseerd. Als u een aanvraag wilt autoriseren, voegt u de referenties van uw opslag account toe aan de toepassing als een connection string. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u de verbindingsreeks hebt gekopieerd, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `<yourconnectionstring>` door de daad werkelijke connection string.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Nadat u de omgevings variabele in Windows hebt toegevoegd, moet u een nieuw exemplaar van het opdracht venster starten.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

Nadat u de omgevings variabele hebt toegevoegd, start u alle actieve Program ma's die moeten worden gelezen van de omgevings variabele opnieuw. Start uw ontwikkel omgeving of editor bijvoorbeeld opnieuw op voordat u doorgaat.

## <a name="object-model"></a>Object model

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslag account.
* Een container in het opslag account
* Een BLOB in een container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-quickstart-blobs-dotnet/blob1.png)

Gebruik de volgende .NET-klassen om te communiceren met deze resources:

* [Cloud Storage account](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): de klasse `CloudStorageAccount` vertegenwoordigt uw Azure-opslag account. Gebruik deze klasse om toegang te verlenen tot Blob Storage met behulp van de toegangs sleutels van uw account.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): de klasse `CloudBlobClient` biedt een toegangs punt voor de BLOB service in uw code.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): de klasse `CloudBlobContainer` vertegenwoordigt een BLOB-container in uw code.
* [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob): het `CloudBlockBlob`-object vertegenwoordigt een blok-Blob in uw code. Blok-blobs bestaan uit blokken met gegevens die afzonderlijk kunnen worden beheerd.

## <a name="code-examples"></a>Code voorbeelden

In deze voorbeeld code fragmenten ziet u hoe u het volgende kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor .NET:

   * [De client verifiëren](#authenticate-the-client)
   * [Een container maken](#create-a-container)
   * [Machtigingen voor een container instellen](#set-permissions-on-a-container)
   * [Blobs uploaden naar een container](#upload-blobs-to-a-container)
   * [De blobs in een container weer geven](#list-the-blobs-in-a-container)
   * [Blobs downloaden](#download-blobs)
   * [Container verwijderen](#delete-a-container)

### <a name="authenticate-the-client"></a>De client verifiëren

Met de onderstaande code wordt gecontroleerd of de omgevings variabele een connection string bevat die kan worden geparseerd voor het maken van een [Cloud Storage account](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) -object dat verwijst naar het opslag account. Gebruik de [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet)-methode om te controleren of de verbindingsreeks geldig is. Als `TryParse` is geslaagd, wordt de `storageAccount`-variabele geïnitialiseerd en wordt `true`geretourneerd.

Voeg deze code toe in de `ProcessAsync` methode:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called CONNECT_STR. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("CONNECT_STR");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'CONNECT_STR' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Als u de rest van de bewerkingen in dit artikel wilt uitvoeren, vervangt u `// ADD OTHER OPERATIONS HERE` in de bovenstaande code door de code fragmenten in de volgende secties.

### <a name="create-a-container"></a>Een container maken

Maak om de container te maken eerst een instantie van het object [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), dat naar de Blob-opslag in uw opslagaccount wijst. Maak vervolgens een instantie van het object [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) en maak de container.

In dit geval roept de code de [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) -methode aan om de container te maken. Er wordt een GUID-waarde toegevoegd aan de containernaam om ervoor te zorgen dat deze uniek is. In een productie omgeving is het vaak beter om de methode [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) te gebruiken om een container te maken als deze nog niet bestaat.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Machtigingen voor een container instellen

Stel machtigingen in voor de container zodat alle blobs in de container openbaar zijn. Als een blob openbaar is, is deze anoniem toegankelijk voor elke client.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende code fragment haalt een verwijzing op naar een `CloudBlockBlob`-object door de methode [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) aan te roepen in de container die in de vorige sectie is gemaakt. Vervolgens wordt het geselecteerde lokale bestand geüpload naar de BLOB door de methode [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) aan te roepen. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Vermeld de blobs in de container met behulp van de methode [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) . In dit geval is er slechts één BLOB aan de container toegevoegd, zodat de vermelding in de lijst alleen die ene BLOB retourneert.

Als er te veel blobs zijn om in één aanroep te retour neren (standaard meer dan 5000), retourneert de methode `ListBlobsSegmentedAsync` een segment van de totale resultatenset en een vervolg token. Om het volgende segment met blobs op te halen, geeft u het vervolgtoken op dat door de vorige aanroep is geretourneerd, enzovoort, totdat het vervolgtoken null is. Een null-vervolgtoken geeft aan dat alle blobs zijn opgehaald. De code laat zien hoe u het vervolg token kunt gebruiken voor de beste prak tijken.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Blobs downloaden

Down load de blob die u eerder hebt gemaakt voor uw lokale bestands systeem met behulp van de methode [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) . De voorbeeld code voegt het achtervoegsel ' _DOWNLOADED ' toe aan de naam van de blob, zodat u beide bestanden in het lokale bestands systeem kunt zien.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code wordt de resources die de app heeft gemaakt opgeschoond door de volledige container te verwijderen met [CloudBlobContainer. DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). U kunt ook de lokale bestanden verwijderen als u dat wilt.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app wordt een test bestand gemaakt in de lokale map *MyDocuments* en geüpload naar Blob Storage. In het voor beeld worden de blobs in de container weer gegeven en wordt het bestand met een nieuwe naam gedownload, zodat u de oude en nieuwe bestanden kunt vergelijken.

Ga naar de toepassingsmap en bouw en voer de toepassing uit.

```console
dotnet build
```

```console
dotnet run
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Wanneer u op **Enter** drukt, worden de opslagcontainer en de bestanden verwijderd. Controleer voordat u doorgaat of de map *Mijn documenten* de twee bestanden bevat. Als u ze opent, ziet u dat ze identiek zijn. Kopieer de URL van de blob in het consolevenster en plak deze in een browser om de inhoud van de blob weer te geven.

Nadat u de bestanden hebt gecontroleerd, drukt u op een willekeurige toets om de demo te voltooien en de testbestanden te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u blobs kunt uploaden, downloaden en er een lijst van maken met behulp van .NET.

Ga verder met het volgende voor informatie over het maken van een web-app die een afbeelding uploadt naar Blob-opslag:

> [!div class="nextstepaction"]
> [Een installatie kopie uploaden en verwerken](storage-upload-process-images.md)

* Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
* Zie [.NET Photo Gallery Web Application Sample with Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/) voor meer informatie over een voorbeeld van een toepassing die u kunt implementeren vanuit Visual Studio voor Windows.
