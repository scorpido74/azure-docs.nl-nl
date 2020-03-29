---
title: Ontwikkelen voor Azure Files met .NET | Microsoft Docs
description: Informatie over het ontwikkelen van .NET-toepassingen en -services die gebruikmaken van Azure Files voor het opslaan van gegevens uit een bestand.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4d8be13a75e276d5be6ec71141a13f95601869f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301434"
---
# <a name="develop-for-azure-files-with-net"></a>Ontwikkelen voor Azure Files met .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

In deze zelfstudie worden de basisbeginselen uitgelegd van het gebruik van .NET voor het ontwikkelen van toepassingen of services die [Azure Files](storage-files-introduction.md) gebruiken voor het opslaan van bestandsgegevens. Met deze zelfstudie wordt een eenvoudige consoletoepassing gemaakt om basisacties uit te voeren met .NET- en Azure-bestanden:

* Haal de inhoud van een bestand op.
* Stel de maximale grootte of *het quotum* in voor de bestandsshare.
* Maak een SAS-sleutel (Shared Access Signature) voor een bestand dat gebruikmaakt van een opgeslagen toegangsbeleid dat is gedefinieerd op het aandeel.
* Een bestand kopiëren naar een ander bestand in hetzelfde opslagaccount.
* Een bestand kopiëren naar een blob in hetzelfde opslagaccount.
* Gebruik Azure Storage Metrics voor het oplossen van problemen.

Zie Wat is Azure-bestanden voor meer informatie over [Azure-bestanden?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>De .NET-API's

Azure Files biedt twee veelzijdige methoden voor het maken van clienttoepassingen: Server Message Block (SMB) en REST. Binnen .NET `System.IO` abstraheren de API's `WindowsAzure.Storage` en API's deze benaderingen.

API | Wanneer gebruikt u dit? | Opmerkingen
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Uw toepassing: <ul><li>Moet bestanden lezen/schrijven met behulp van SMB</li><li>Wordt uitgevoerd op een apparaat met toegang tot uw Azure Files-account via poort 445</li><li>Hoeft geen beheerinstellingen van de bestandsshare te beheren</li></ul> | Bestand I/O geïmplementeerd met Azure Files via SMB is over het algemeen hetzelfde als I/O met een netwerkbestandsshare of lokaal opslagapparaat. Zie de zelfstudie [consoletoepassing](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) voor een inleiding tot een aantal functies in .NET, waaronder de i/o-bestand.
[Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | Uw toepassing: <ul><li>Geen toegang tot Azure Files met SMB op poort 445 vanwege firewall- of ISP-beperkingen</li><li>Vereist beheerfunctionaliteit, zoals de mogelijkheid tot het instellen van een quotum voor een bestandsshare of het maken van een Shared Access Signature (gedeelde-toegangshandtekening)</li></ul> | Dit artikel toont het `Microsoft.Azure.Storage.File` gebruik van voor bestand I /O met behulp van REST in plaats van SMB en het beheer van het bestand te delen.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>De consoletoepassing maken en de assembly verkrijgen

Maak in Visual Studio een nieuwe Windows-consoletoepassing. In de volgende stappen ziet u hoe u een consoletoepassing maakt in Visual Studio 2019. De stappen zijn nagenoeg gelijk in andere versies van Visual Studio.

1. Start Visual Studio en selecteer **Een nieuw project maken**.
1. Kies in **Een nieuw project maken**de optie Console App **(.NET Framework)** voor C#en selecteer **Volgende**.
1. Voer in **Uw nieuwe project configureren**een naam voor de app in en selecteer **Maken**.

U alle codevoorbeelden in deze `Main()` zelfstudie toevoegen aan `Program.cs` de methode van het bestand van uw consoletoepassing.

U de Azure Storage-clientbibliotheek gebruiken in elk type .NET-toepassing. Deze typen omvatten een Azure-cloudservice of web-app en desktop- en mobiele toepassingen. In deze gids gebruiken we een consoletoepassing voor de eenvoud.

## <a name="use-nuget-to-install-the-required-packages"></a>NuGet gebruiken om de vereiste pakketten te installeren

Raadpleeg deze pakketten in uw project om deze zelfstudie te voltooien:

* [Algemene microsoft Azure Storage-bibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Dit pakket biedt programmatische toegang tot algemene bronnen in uw opslagaccount.
* [Microsoft Azure Storage Blob-bibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Dit pakket biedt programmatische toegang tot blobbronnen in uw opslagaccount.
* [Microsoft Azure Storage File library for .NET Microsoft Azure Storage File library for .NET Microsoft Azure Storage File library for .NET Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Dit pakket biedt programmatische toegang tot bestandsbronnen in uw opslagaccount.
* [Microsoft Azure Configuration Manager-bibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Dit pakket biedt een klasse voor het ontzeggen van een verbindingstekenreeks in een configuratiebestand, waar uw toepassing ook wordt uitgevoerd.

Met NuGet kunt u beide pakketten verkrijgen. Volg deze stappen:

1. Klik in **Solution Explorer**met de rechtermuisknop op uw project en kies **NuGet-pakketten beheren.**
1. Selecteer **Bladeren**in **NuGet Package Manager**. Zoek en kies **vervolgens Microsoft.Azure.Storage.Blob**en selecteer **Installeren**.

   Met deze stap worden het pakket en de afhankelijkheden ervan geïnstalleerd.
1. Zoek naar en installeer deze pakketten:

   * **Microsoft.Azure.Storage.Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Uw opslagaccountreferenties opslaan in het app.config-bestand

Sla vervolgens uw referenties op in `App.config` het bestand van uw project. Dubbelklik **Solution Explorer** `App.config` in Solution Explorer en bewerk het bestand zodat het vergelijkbaar is met het volgende voorbeeld. Vervang `myaccount` door de naam `mykey` van uw opslagaccount en uw opslagaccountsleutel.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> De nieuwste versie van de Azure-opslagemulator biedt geen ondersteuning voor Azure Files. De verbindingsreeks werkt alleen met Azure Files als de reeks verwijst naar een Azure Storage-account in de cloud.

## <a name="add-using-directives"></a>Using-instructies toevoegen

Open **het** `Program.cs` bestand in Solution Explorer en voeg de volgende gebruiksrichtlijnen toe aan de bovenkant van het bestand.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Via een programma toegang krijgen tot de bestandsshare

Voeg vervolgens de volgende `Main()` inhoud toe aan de methode, na de bovenstaande code, om de verbindingstekenreeks op te halen. Deze code krijgt een verwijzing naar het bestand dat we eerder hebben gemaakt en de inhoud ervan wordt uitgevoerd.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Voer de consoletoepassing uit om de uitvoer te zien.

## <a name="set-the-maximum-size-for-a-file-share"></a>De maximale grootte voor een bestandsshare instellen

Vanaf versie 5.x van de Azure Storage Client Library u het quotum (maximale grootte) instellen voor een bestandsshare. U kunt ook controleren hoeveel gegevens er momenteel zijn opgeslagen in de share.

Als u het quotum voor een aandeel instelt, wordt de totale grootte van de bestanden die op het aandeel zijn opgeslagen, beperkt. Als de totale grootte van bestanden op het aandeel groter is dan het quotum dat is ingesteld op het aandeel, kunnen clients de grootte van bestaande bestanden niet vergroten. Clients kunnen geen nieuwe bestanden maken, tenzij deze bestanden leeg zijn.

In onderstaand voorbeeld ziet u hoe u het huidige gebruik voor een share controleert en een quotum voor de share instelt.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Een Shared Access Signature genereren voor een bestand of bestandsshare

Vanaf versie 5.x van de Azure Storage-clientbibliotheek kunt u een Shared Access Signature (SAS) genereren voor een bestandsshare of voor een afzonderlijk bestand. U ook een opgeslagen toegangsbeleid maken voor een bestandsshare om handtekeningen voor gedeelde toegang te beheren. We raden u aan een opgeslagen toegangsbeleid te maken, omdat u de SAS hiermee intrekken als deze wordt gecompromitteerd.

In het volgende voorbeeld wordt een beleid voor opgeslagen toegang voor een share gezoend. In het voorbeeld wordt dat beleid gebruikt om de beperkingen voor een SAS in een bestand in het aandeel op te geven.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Zie [Hoe een handtekening voor gedeelde toegang werkt](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)voor meer informatie over het maken en gebruiken van handtekeningen voor gedeelde toegang.

## <a name="copy-files"></a>Bestanden kopiëren

Vanaf versie 5.x van de Azure Storage-clientbibliotheek kunt u een bestand kopiëren naar een ander bestand, een bestand naar een blob of een blob naar een bestand. In de volgende secties laten we zien hoe we deze kopieerbewerkingen programmatisch kunnen uitvoeren.

U AzCopy ook gebruiken om het ene bestand naar het andere te kopiëren of om een blob naar een bestand of andersom te kopiëren. Zie [Aan de slag met AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Als u een blob kopieert naar een bestand of een bestand naar een blob, moet u een Shared Access Signature (SAS) gebruiken om toegang tot het bronobject toe te staan, zelfs als u binnen hetzelfde opslagaccount kopieert.
>

### <a name="copy-a-file-to-another-file"></a>Een bestand kopiëren naar een ander bestand

In het volgende voorbeeld wordt een bestand gekopieerd naar een ander bestand in dezelfde share. Omdat deze kopieerbewerking kopieën maakt tussen bestanden in hetzelfde opslagaccount, u verificatie van gedeelde sleutels gebruiken om de kopie uit te voeren.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>Een bestand kopiëren naar een blob

In het volgende voorbeeld wordt een bestand gemaakt en vervolgens gekopieerd naar een blob in hetzelfde opslagaccount. In het voorbeeld wordt een SAS voor het bronbestand gemaakt, die tijdens de kopieerbewerking door de service wordt gebruikt om toegang tot het bronbestand toe te staan.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Op dezelfde manier kunt u een blob naar een bestand kopiëren. Als het bronobject een blob is, maakt u een SAS om tijdens de kopieerbewerking de toegang tot de blob toe te staan.

## <a name="share-snapshots"></a>Momentopnamen van shares

Vanaf versie 8.5 van de Azure Storage-clientbibliotheek kunt u een momentopname van een share maken. U kunt ook de lijst met momentopnamen van shares weergeven, door een lijst met momentopnamen van shares bladeren en momentopnamen van shares verwijderen. Momentopnamen van shares zijn alleen-lezen. Schrijfbewerkingen zijn dus niet toegestaan voor momentopnamen van shares.

### <a name="create-share-snapshots"></a>Momentopnamen van shares maken

In het volgende voorbeeld wordt een momentopname van een bestandsshare gemaakt.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Momentopnamen van shares opvragen

In het volgende voorbeeld worden de momentopnamen van shares op een share weergegeven.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Bladeren door bestanden en mappen binnen momentopnamen van shares

In het volgende voorbeeld wordt door bestanden en mappen binnen momentopnamen van shares gebladerd.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Shares en momentopnamen van shares weergeven en bestandsshares of bestanden uit momentopnamen van shares herstellen

Door een momentopname van een bestandsshare te maken, kunt u afzonderlijke bestanden of de gehele bestandsshare in de toekomst herstellen.

U kunt een bestand vanuit een momentopname van de bestandsshare herstellen door de momentopnamen van shares van een bestandsshare op te vragen. U vervolgens een bestand ophalen dat bij een bepaalde momentopname van delen hoort. Gebruik die versie om direct te lezen en te vergelijken of te herstellen.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>Momentopnamen van shares verwijderen

In het volgende voorbeeld wordt een momentopname van een bestandsshare verwijderd.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Azure-bestanden oplossen met behulp van metrische gegevens<a name="troubleshooting-azure-files-using-metrics"></a>

Azure Opslaganalyse ondersteunt nu metrische gegevens voor Azure Files. Met metrische gegevens kunt u aanvragen volgen en problemen diagnosticeren.

U metrische gegevens voor Azure-bestanden inschakelen via de [Azure-portal.](https://portal.azure.com) U ook programmatisch statistieken inschakelen door de bewerking Bestandsserviceeigenschappen instellen aan te roepen met de REST-API of een van de analogen in de opslagclientbibliotheek.

In het volgende codevoorbeeld ziet u hoe u de Storage-clientbibliotheek voor .NET gebruikt om metrische gegevens in te schakelen voor Azure Files.

Voeg eerst de `using` volgende `Program.cs` richtlijnen toe aan uw bestand, samen met de richtlijnen die u hierboven hebt toegevoegd:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Hoewel Azure Blobs, Azure Tables en `ServiceProperties` Azure Queues `Microsoft.Azure.Storage.Shared.Protocol` het gedeelde type in de `FileServiceProperties` naamruimte `Microsoft.Azure.Storage.File.Protocol` gebruiken, gebruikt Azure Files zijn eigen type, het type in de naamruimte. U moet echter naar beide naamruimten van uw code verwijzen om de volgende code samen te stellen.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Als u problemen ondervindt, u verwijzen naar [Problemen met Azure Files oplossen in Windows.](storage-troubleshoot-windows-file-connection-problems.md)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Files:

### <a name="conceptual-articles-and-videos"></a>Conceptuele artikelen en video's

* [Azure Files: een naadloos SMB-bestandssysteem voor Windows en Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Azure Files gebruiken met Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Hulpprogramma-ondersteuning voor File Storage

* [Aan de slag met AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Problemen met Azure Files in Windows oplossen](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Naslaginformatie

* [Azure Storage-API's voor .NET](/dotnet/api/overview/azure/storage)
* [Bestandsservice REST API](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Blogberichten

* [Azure-bestandsopslag, nu algemeen beschikbaar](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Een kijkje achter de schermen van Azure File Storage)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introductie van Microsoft Azure Files Service](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (Permanente verbindingen met Microsoft Azure-bestanden)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
