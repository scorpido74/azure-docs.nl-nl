---
title: 'Snelstart: Azure Blob-opslagclientbibliotheek voor .NET'
description: In deze snelstart leert u hoe u de Azure Blob-opslagclientbibliotheek voor .NET gebruiken om een container en een blob in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b243d05619642e1dd3ad8dfe2bbe1d0a9661b773
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75351306"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Snelstart: Azure Blob-opslagclientbibliotheek v11 voor .NET

Aan de slag met de Azure Blob Storage-clientbibliotheek v11 voor .NET. Azure Blob Storage is de objectopslagoplossing van Microsoft voor de cloud. Volg stappen om het pakket te installeren en probeer voorbeeldcode uit voor basistaken. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

Gebruik de Azure Blob Storage-clientbibliotheek voor .NET om:

* Een container maken
* Machtigingen instellen voor een container
* Een blob maken in Azure Storage
* De blob downloaden naar uw lokale computer
* Alle blobs in een container weergeven
* Een container verwijderen

[API-naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [Voorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account - [een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidige [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturingssysteem. Zorg ervoor dat u de SDK en niet de runtime.

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Blob Storage-clientbibliotheek voor .NET.

### <a name="create-the-project"></a>Het project maken

Maak eerst een .NET Core-toepassing met de naam *blob-quickstart*.

1. Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app te maken met de naam *blob-quickstart.* Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Schakel over naar de nieuw gemaakte *blob-quickstartmap* en bouw de app om te controleren of alles in orde is.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

De verwachte output van de build moet er ongeveer zo uitzien:

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

Installeer de Azure Blob Storage-clientbibliotheek voor .NET-pakket met `dotnet add package` behulp van de opdracht terwijl u zich nog in de toepassingsmap bevindt.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In de projectmap:

1. Het *Program.cs-bestand* openen in uw editor
2. De `Console.WriteLine` instructie verwijderen
3. Richtlijnen `using` toevoegen
4. Een `ProcessAsync` methode maken waarbij de hoofdcode voor het voorbeeld zich zal bevinden
5. Asynchroon aanroepen `ProcessAsync` van de methode uit`Main`

Hier is de code:

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

Wanneer de voorbeeldtoepassing een aanvraag voor Azure Storage indient, moet deze zijn geautoriseerd. Als u een aanvraag wilt autoriseren, voegt u uw opslagaccountreferenties toe aan de toepassing als verbindingstekenreeks. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u de verbindingsreeks hebt gekopieerd, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `<yourconnectionstring>` door uw werkelijke verbindingstekenreeks.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Nadat u de omgevingsvariabele in Windows hebt toegevoegd, moet u een nieuwe instantie van het opdrachtvenster starten.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u alle lopende programma's opnieuw die de omgevingsvariabele moeten lezen. Start bijvoorbeeld uw ontwikkelomgeving of editor opnieuw voordat u verdergaat.

## <a name="object-model"></a>Objectmodel

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount.
* Een container in het opslagaccount
* Een blob in een container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-quickstart-blobs-dotnet/blob1.png)

Gebruik de volgende .NET-klassen om met deze bronnen te communiceren:

* [CloudStorageAccount:](/dotnet/api/microsoft.azure.storage.cloudstorageaccount) `CloudStorageAccount` de klasse vertegenwoordigt uw Azure-opslagaccount. Gebruik deze klasse om toegang tot Blob-opslag te autoriseren met uw accounttoegangssleutels.
* [CloudBlobClient:](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) `CloudBlobClient` de klasse biedt een toegangspunt tot de Blob-service in uw code.
* [CloudBlobContainer:](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) `CloudBlobContainer` de klasse vertegenwoordigt een blobcontainer in uw code.
* [CloudBlockBlob:](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) `CloudBlockBlob` het object vertegenwoordigt een blokblob in uw code. Blok-blobs bestaan uit blokken met gegevens die afzonderlijk kunnen worden beheerd.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u het volgende uitvoeren met de Azure Blob-opslagclientbibliotheek voor .NET:

   * [De client verifiëren](#authenticate-the-client)
   * [Een container maken](#create-a-container)
   * [Machtigingen instellen voor een container](#set-permissions-on-a-container)
   * [Blobs uploaden naar een container](#upload-blobs-to-a-container)
   * [De blobs in een container in een lijst weergeven](#list-the-blobs-in-a-container)
   * [Blobs downloaden](#download-blobs)
   * [Een container verwijderen](#delete-a-container)

### <a name="authenticate-the-client"></a>De client verifiëren

In de onderstaande code wordt gecontroleerd of de omgevingsvariabele een verbindingstekenreeks bevat die kan worden ontleed om een [CloudStorageAccount-object](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) te maken dat naar het opslagaccount wijst. Gebruik de [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet)-methode om te controleren of de verbindingsreeks geldig is. Als `TryParse` dit lukt, worden `storageAccount` de variabele `true`en het rendement geinitialiseerd.

Voeg deze code `ProcessAsync` toe in de methode:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");

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
        "Add an environment variable named 'AZURE_STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Als u de rest van de `// ADD OTHER OPERATIONS HERE` bewerkingen in dit artikel wilt uitvoeren, vervangt u in de bovenstaande code de codefragmenten in de volgende secties.

### <a name="create-a-container"></a>Een container maken

Maak om de container te maken eerst een instantie van het object [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), dat naar de Blob-opslag in uw opslagaccount wijst. Maak vervolgens een instantie van het object [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) en maak de container.

In dit geval roept de code de [createAsync-methode](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) aan om de container te maken. Er wordt een GUID-waarde toegevoegd aan de containernaam om ervoor te zorgen dat deze uniek is. In een productieomgeving heeft het vaak de voorkeur om de [createIfNotExistsAsync-methode](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) alleen te gebruiken om een container te maken als deze nog niet bestaat.

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

### <a name="set-permissions-on-a-container"></a>Machtigingen instellen voor een container

Machtigingen voor de container instellen, zodat blobs in de container openbaar zijn. Als een blob openbaar is, is deze anoniem toegankelijk voor elke client.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

In het volgende codefragment `CloudBlockBlob` wordt een verwijzing naar een object weergegeven door de [methode GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) aan te roepen op de container die in de vorige sectie is gemaakt. Vervolgens wordt het geselecteerde lokale bestand naar de blob geüpload door de [methode UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) aan te roepen. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

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

Lijst van de blobs in de container met behulp van de [methode ListBlobsSegmentedAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) In dit geval is er slechts één blob aan de container toegevoegd, zodat de lijstbewerking alleen die ene blob retourneert.

Als er te veel blobs zijn om in één aanroep terug te `ListBlobsSegmentedAsync` keren (standaard meer dan 5000), retourneert de methode een segment van de totale resultaatset en een vervolgtoken. Om het volgende segment met blobs op te halen, geeft u het vervolgtoken op dat door de vorige aanroep is geretourneerd, enzovoort, totdat het vervolgtoken null is. Een null-vervolgtoken geeft aan dat alle blobs zijn opgehaald. De code laat zien hoe u het vervolgtoken gebruiken omwille van best practices.

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

Download de blob die eerder is gemaakt naar uw lokale bestandssysteem met behulp van de [methode DownloadToFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) De voorbeeldcode voegt een achtervoegsel van '_DOWNLOADED' toe aan de blobnaam, zodat u beide bestanden in het lokale bestandssysteem zien.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code worden de resources die de app heeft gemaakt door de hele container te verwijderen met [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync), opschonen. U kunt ook de lokale bestanden verwijderen als u dat wilt.

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

Deze app maakt een testbestand in de lokale *Map MyDocuments* en uploadt het naar Blob-opslag. In het voorbeeld worden vervolgens de blobs in de container weergegeven en wordt het bestand gedownload met een nieuwe naam, zodat u de oude en nieuwe bestanden vergelijken.

Navigeer naar uw toepassingsmap en bouw en voer de toepassing uit.

```console
dotnet build
```

```console
dotnet run
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

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

Ga verder met:

> [!div class="nextstepaction"]
> [Een installatiekopie uploaden en verwerken](storage-upload-process-images.md)

* Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
* Zie [.NET Photo Gallery Web Application Sample with Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/) voor meer informatie over een voorbeeld van een toepassing die u kunt implementeren vanuit Visual Studio voor Windows.
