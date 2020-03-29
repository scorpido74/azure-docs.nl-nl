---
title: Gegevens overbrengen met de bibliotheek Gegevensverplaatsing voor .NET
titleSuffix: Azure Storage
description: Gebruik de bibliotheek Gegevensverplaatsing om gegevens naar of van blob- en bestandsinhoud te verplaatsen of te kopiëren. Kopieer gegevens naar Azure Storage vanuit lokale bestanden of kopieer gegevens binnen of tussen opslagaccounts. Migreer uw gegevens eenvoudig naar Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5b8654500fd697685b38e4f51ba1069e0cf6ccfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942912"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Gegevens overdragen met de bibliotheek voor gegevensverplaatsing

De Azure Storage Data Movement-bibliotheek is een open source-bibliotheek met meerdere platforms die is ontworpen voor het uploaden, downloaden en kopiëren van blobs en bestanden met hoge prestaties. De bibliotheek Gegevensverplaatsing biedt handige methoden die niet beschikbaar zijn in de Azure Storage-clientbibliotheek voor .NET. Deze methoden bieden de mogelijkheid om het aantal parallelle bewerkingen in te stellen, de voortgang van de overdracht bij te houden, een geannuleerde overdracht eenvoudig te hervatten en nog veel meer.

Deze bibliotheek maakt ook gebruik van .NET Core, wat betekent dat u deze gebruiken bij het bouwen van .NET-apps voor Windows, Linux en macOS. Zie voor meer informatie over .NET Core de [.NET Core-documentatie](https://dotnet.github.io/). Deze bibliotheek werkt ook voor traditionele .NET Framework-apps voor Windows.

In dit document wordt uitgelegd hoe u een .NET Core-consoletoepassing maakt die wordt uitgevoerd op Windows, Linux en macOS en de volgende scenario's uitvoert:

- Upload bestanden en mappen naar Blob Storage.
- Definieer het aantal parallelle bewerkingen bij het overbrengen van gegevens.
- Voortgang van de gegevensoverdracht bijhouden.
- Geannuleerde gegevensoverdracht hervatten.
- Kopieer bestand van URL naar Blob-opslag.
- Kopieer vanuit Blob-opslag naar Blob-opslag.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio-code](https://code.visualstudio.com/)
- Een [Azure-opslagaccount](storage-account-create.md)

## <a name="setup"></a>Instellen

1. Ga naar de [.NET Core Installation Guide](https://www.microsoft.com/net/core) om .NET Core te installeren. Wanneer u uw omgeving selecteert, kiest u de opdrachtregeloptie.
2. Maak op de opdrachtregel een map voor uw project. Navigeer in deze map `dotnet new console -o <sample-project-name>` en typ om een C#-consoleproject te maken.
3. Open deze map in Visual Studio Code. Deze stap kan snel worden gedaan `code .` via de opdrachtregel door in Windows te typen.
4. Installeer de [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) vanuit de Visual Studio Code Marketplace. Start Visual Studio Code opnieuw.
5. Op dit punt ziet u twee aanwijzingen. Een daarvan is voor het toevoegen van "vereiste activa om te bouwen en debuggen." Klik op 'ja'. Een andere prompt is het herstellen van onopgeloste afhankelijkheden. Klik op 'herstellen'.
6. Wijzigen `launch.json` `.vscode` onder om externe terminal als console te gebruiken. Deze instelling moet worden gelezen als`"console": "externalTerminal"`
7. Met Visual Studio Code u .NET Core-toepassingen debuggen. Klik `F5` om uw toepassing uit te voeren en controleer of uw installatie werkt. Je zou "Hello World!" moeten zien. afgedrukt op de console.

## <a name="add-the-data-movement-library-to-your-project"></a>De bibliotheek Gegevensverplaatsing toevoegen aan uw project

1. Voeg de nieuwste versie van de `dependencies` bibliotheek `<project-name>.csproj` Gegevensbeweging toe aan het gedeelte van uw bestand. Op het moment van schrijven zou deze versie`"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Er moet een prompt worden weergegeven om uw project te herstellen. Klik op de knop 'herstellen'. U uw project ook herstellen vanaf `dotnet restore` de opdrachtregel door de opdracht in de hoofdmap van uw projectmap te typen.

Wijzigen `<project-name>.csproj`:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Het skelet van uw toepassing instellen

Het eerste wat we doen is het opzetten van de "skelet" code van onze toepassing. Deze code vraagt ons om een naam van een opslagaccount `CloudStorageAccount` en accountsleutel en gebruikt deze referenties om een object te maken. Dit object wordt gebruikt om te communiceren met ons Opslagaccount in alle transferscenario's. De code vraagt ons ook om het type overdrachtsbewerking te kiezen dat we willen uitvoeren.

Wijzigen `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Een lokaal bestand uploaden naar een blob

Voeg de `GetSourcePath` methoden `GetBlob` `Program.cs`toe en aan :

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Wijzig `TransferLocalFileToAzureBlob` de methode:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Met deze code wordt ons gevraagd naar het pad naar een lokaal bestand, de naam van een nieuwe of bestaande container en de naam van een nieuwe blob. De `TransferManager.UploadAsync` methode voert het uploaden uit met behulp van deze informatie.

Hit `F5` om uw toepassing uit te voeren. U controleren of de upload heeft plaatsgevonden door uw opslagaccount te bekijken met de [Microsoft Azure Storage Explorer.](https://storageexplorer.com/)

## <a name="set-the-number-of-parallel-operations"></a>Het aantal parallelle bewerkingen instellen

Een functie die wordt aangeboden door de databewegingbibliotheek is de mogelijkheid om het aantal parallelle bewerkingen in te stellen om de doorvoer van gegevensoverdracht te verhogen. Standaard stelt de bibliotheek Gegevensbeweging het aantal parallelle bewerkingen in op 8 * het aantal kernen op uw machine.

Houd er rekening mee dat veel parallelle bewerkingen in een omgeving met lage bandbreedte de netwerkverbinding kunnen overweldigen en daadwerkelijk kunnen voorkomen dat bewerkingen volledig worden voltooid. U moet experimenteren met deze instelling om te bepalen wat het beste werkt op basis van uw beschikbare netwerkbandbreedte.

Laten we een code toevoegen waarmee we het aantal parallelle bewerkingen kunnen instellen. Laten we ook code toevoegen die tijden hoe lang het duurt voordat de overdracht is voltooid.

Voeg `SetNumberOfParallelOperations` een `Program.cs`methode toe aan:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Wijzig `ExecuteChoice` de te `SetNumberOfParallelOperations`gebruiken methode:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Wijzig `TransferLocalFileToAzureBlob` de methode om een timer te gebruiken:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Voortgang van de overdracht bijhouden

Weten hoe lang het duurde voordat de gegevens over te dragen is nuttig. De voortgang van de transfer *tijdens* de overdracht zou echter nog beter zijn. Om dit scenario te bereiken, `TransferContext` moeten we een object maken. Het `TransferContext` object komt in `SingleTransferContext` `DirectoryTransferContext`twee vormen: en . De eerste is voor de overdracht van een enkel bestand en de laatste is voor de overdracht van een directory van bestanden.

Voeg de `GetSingleTransferContext` methoden `GetDirectoryTransferContext` `Program.cs`toe en aan :

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Wijzig `TransferLocalFileToAzureBlob` de te `GetSingleTransferContext`gebruiken methode:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Een geannuleerde overdracht hervatten

Een andere handige functie aangeboden door de Data Movement bibliotheek is de mogelijkheid om een geannuleerde overdracht te hervatten. Laten we wat code toevoegen waarmee we de overdracht `c`tijdelijk kunnen annuleren door te typen en de overdracht 3 seconden later hervatten.

Wijzigen `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Tot nu toe `checkpoint` is onze waarde `null`altijd ingesteld op . Als we de overdracht annuleren, halen we het laatste controlepunt van onze overplaatsing op en gebruiken we dit nieuwe controlepunt in onze transfercontext.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Een lokale map overbrengen naar Blob-opslag

Het zou teleurstellend zijn als de Data Movement-bibliotheek slechts één bestand tegelijk kon overdragen. Gelukkig is dit niet het geval. De data-bewegingsbibliotheek biedt de mogelijkheid om een map met bestanden en al zijn submappen over te dragen. Laten we wat code toevoegen waarmee we precies dat kunnen doen.

Voeg eerst de `GetBlobDirectory` `Program.cs`methode toe aan:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Wijzig `TransferLocalDirectoryToAzureBlobDirectory`vervolgens:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Er zijn een paar verschillen tussen deze methode en de methode voor het uploaden van een enkel bestand. We gebruiken `TransferManager.UploadDirectoryAsync` nu en `getDirectoryTransferContext` de methode die we eerder hebben gemaakt. Daarnaast bieden we nu `options` een waarde aan onze uploadoperatie, die ons in staat stelt om aan te geven dat we submappen in onze upload willen opnemen.

## <a name="copy-a-file-from-url-to-a-blob"></a>Een bestand van URL naar een blob kopiëren

Laten we nu code toevoegen waarmee we een bestand van een URL naar een Azure Blob kunnen kopiëren.

Wijzigen `TransferUrlToAzureBlob`:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Een belangrijke use case voor deze functie is wanneer u gegevens van een andere cloudservice (bijvoorbeeld AWS) naar Azure moet verplaatsen. Zolang u een URL hebt die u toegang geeft tot de bron, u `TransferManager.CopyAsync` die bron eenvoudig verplaatsen naar Azure Blobs met behulp van de methode. Deze methode introduceert ook een nieuwe booleaanse parameter. Als u `true` deze parameter instelt, geeft u aan dat we een asynchrone serverkopie willen maken. Deze parameter `false` instellen op een synchrone kopie - wat betekent dat de bron eerst naar onze lokale machine wordt gedownload en vervolgens wordt geüpload naar Azure Blob. Synchrone kopie is momenteel echter alleen beschikbaar voor kopiëren van de ene Azure Storage-bron naar de andere.

## <a name="copy-a-blob"></a>Een blob kopiëren

Een andere functie die uniek is door de bibliotheek Gegevensverplaatsing, is de mogelijkheid om van de ene Azure Storage-bron naar de andere te kopiëren.

Wijzigen `TransferAzureBlobToAzureBlob`:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

In dit voorbeeld stellen we de `TransferManager.CopyAsync` `false` booleaanse parameter in om aan te geven dat we een synchrone kopie willen maken. Dit betekent dat de bron eerst naar onze lokale machine wordt gedownload en vervolgens wordt geüpload naar Azure Blob. De synchrone kopie optie is een geweldige manier om ervoor te zorgen dat uw kopieerbewerking een consistente snelheid heeft. De snelheid van een asynchrone server-side copy is daarentegen afhankelijk van de beschikbare netwerkbandbreedte op de server, die kan fluctueren. Synchrone kopie kan echter extra uitgangskosten genereren in vergelijking met asynchrone kopie. De aanbevolen aanpak is om synchrone kopie te gebruiken in een Azure VM die zich in dezelfde regio bevindt als uw bronopslagaccount om uitgaande kosten te voorkomen.

De toepassing gegevensverplaatsing is nu voltooid. [Het volledige code voorbeeld is beschikbaar op GitHub.](https://github.com/azure-samples/storage-dotnet-data-movement-library-app)

## <a name="next-steps"></a>Volgende stappen

[Referentiedocumentatie voor azure-gegevensverplaatsingsbibliotheek](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
