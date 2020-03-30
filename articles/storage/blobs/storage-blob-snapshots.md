---
title: Een blobmomentopname maken en beheren in .NET - Azure Storage
description: Meer informatie over het maken van een alleen-lezen momentopname van een blob om op een bepaald moment een back-up van blobgegevens te maken.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 17cd57fbcf9b1c14fb275a070bdefdd1282c4d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370522"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Een blobmomentopname maken en beheren in .NET

Een momentopname is een alleen-lezen versie van een blob die op een moment is gemaakt. Momentopnamen zijn handig voor het maken van back-ups van blobs. In dit artikel ziet u hoe u blobmomentopnamen maakt en beheert met behulp van de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>Over blobmomentopnamen

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Een momentopname van een blob is identiek aan de basisblob, behalve dat de blob URI een **DateTime-waarde** heeft die is toegevoegd aan de blob URI om aan te geven op welk moment de momentopname is gemaakt. Als een paginablob URI `http://storagesample.core.blob.windows.net/mydrives/myvhd`bijvoorbeeld is, is `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`de momentopname URI vergelijkbaar met .

> [!NOTE]
> Alle momentopnamen delen de URI van de basisblob. Het enige onderscheid tussen de basisblob en de momentopname is de toegevoegde **DatumTijd-waarde.**
>

Een blob kan een willekeurig aantal momentopnamen hebben. Momentopnamen blijven bestaan totdat ze expliciet worden verwijderd, wat betekent dat een momentopname de basisblob niet kan overleven. U de momentopnamen opsommen die zijn gekoppeld aan de basisblob om uw huidige momentopnamen bij te houden.

Wanneer u een momentopname van een blob maakt, worden de systeemeigenschappen van de blob gekopieerd naar de momentopname met dezelfde waarden. De metagegevens van de basisblob worden ook gekopieerd naar de momentopname, tenzij u afzonderlijke metagegevens opgeeft voor de momentopname wanneer u deze maakt. Nadat u een momentopname hebt gemaakt, u deze lezen, kopiëren of verwijderen, maar u deze niet wijzigen.

Eventuele leases die zijn gekoppeld aan de basisblob hebben geen invloed op de momentopname. U geen huurovereenkomst op een momentopname verkrijgen.

Een VHD-bestand wordt gebruikt om de huidige informatie en status voor een VM-schijf op te slaan. U een schijf loskoppelen van de VM of de VM afsluiten en vervolgens een momentopname maken van het VHD-bestand. U dat momentopnamebestand later gebruiken om het VHD-bestand op dat moment op te halen en de VM opnieuw te maken.

## <a name="create-a-snapshot"></a>Een momentopname maken

Als u een momentopname van een blokblob wilt maken, gebruikt u een van de volgende methoden:

- [Momentopname maken](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

In het volgende codevoorbeeld ziet u hoe u een momentopname maakt. In dit voorbeeld worden extra metagegevens voor de momentopname opgegeven wanneer deze wordt gemaakt.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Momentopnamen verwijderen

Als u een blob wilt verwijderen, moet u eerst alle momentopnamen van die blob verwijderen. U een momentopname afzonderlijk verwijderen of opgeven dat alle momentopnamen worden verwijderd wanneer de bronblob wordt verwijderd. Als u een blob met nog steeds momentopnamen probeert te verwijderen, ontstaat er een fout.

Als u blobmomentopnamen wilt verwijderen, gebruikt u een van de volgende blobverwijderingsmethoden en neemt u het enum [van DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) op.

- [Verwijderen](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

In het volgende codevoorbeeld ziet u hoe u een `blockBlob` blob en de momentopnamen ervan verwijdert in .NET, waar een object van het type [CloudBlockBlob][dotnet_CloudBlockBlob]is:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>De absolute URI teruggeven naar een momentopname

In het volgende codevoorbeeld wordt een momentopname gemaakt en wordt de absolute URI voor de primaire locatie uitgeschreven.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Begrijpen hoe momentopnamen kosten genereren

Het maken van een momentopname, een alleen-lezen kopie van een blob, kan leiden tot extra kosten voor gegevensopslag voor uw account. Bij het ontwerpen van uw toepassing is het belangrijk om op de hoogte te zijn van hoe deze kosten kunnen worden gegenereerd, zodat u de kosten minimaliseren.

### <a name="important-billing-considerations"></a>Belangrijke factureringsoverwegingen

De volgende lijst bevat belangrijke punten om rekening mee te houden bij het maken van een momentopname.

- Uw opslagaccount brengt kosten met zich mee voor unieke blokken of pagina's, ongeacht of deze zich in de blob of in de momentopname bevinden. Voor uw account worden geen extra kosten in rekening gebracht voor momentopnamen die zijn gekoppeld aan een blob totdat u de blob bijwerkt waarop deze zijn gebaseerd. Nadat u de basisblob hebt bijgewerkt, wijkt deze af van de momentopnamen. Wanneer dit gebeurt, worden er kosten in rekening gebracht voor de unieke blokken of pagina's in elke blob of momentopname.
- Wanneer u een blok binnen een blokblob vervangt, wordt dat blok vervolgens opgeladen als een uniek blok. Dit geldt zelfs als het blok dezelfde blok-ID en dezelfde gegevens heeft als in de momentopname. Nadat het blok opnieuw is vastgelegd, wijkt het af van zijn tegenhanger in elke momentopname en worden de gegevens ervan in rekening gebracht. Hetzelfde geldt voor een pagina in een paginablob die is bijgewerkt met identieke gegevens.
- Als u een blokblob vervangt door de methode [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]of [UploadFromByteArray][dotnet_UploadFromByteArray] aan te roepen, worden alle blokken in de blob vervangen. Als u een momentopname hebt gekoppeld aan die blob, lopen alle blokken in de basisblob en momentopname nu uiteen en worden alle blokken in beide blobs in rekening gebracht. Dit geldt zelfs als de gegevens in de basisblob en de momentopname identiek blijven.
- De Azure Blob-service heeft geen middel om te bepalen of twee blokken identieke gegevens bevatten. Elk blok dat wordt geüpload en vastgelegd, wordt als uniek behandeld, zelfs als het dezelfde gegevens en dezelfde blok-ID heeft. Omdat er kosten in rekening worden gebracht voor unieke blokken, is het belangrijk om te overwegen dat het bijwerken van een blob met een momentopname resulteert in extra unieke blokken en extra kosten.

### <a name="minimize-cost-with-snapshot-management"></a>Kosten minimaliseren met momentopnamebeheer

We raden u aan uw snapshots zorgvuldig te beheren om extra kosten te voorkomen. U deze aanbevolen procedures volgen om de kosten van de opslag van uw momentopnamen te minimaliseren:

- Verwijder en maak opnieuw momentopnamen die zijn gekoppeld aan een blob wanneer u de blob bijwerkt, zelfs als u met identieke gegevens wordt bijgewerkt, tenzij voor het ontwerp van de toepassing vereist dat u momentopnamen onderhoudt. Door de momentopnamen van de blob te verwijderen en opnieuw te maken, u ervoor zorgen dat de blob en snapshots niet uiteenlopen.
- Als u momentopnamen voor een blob bijhoudt, moet u voorkomen dat [u UploadFromFile,][dotnet_UploadFromFile] [UploadText,][dotnet_UploadText] [UploadFromStream][dotnet_UploadFromStream]of [UploadFromTeArray][dotnet_UploadFromByteArray] aanroept om de blob bij te werken. Deze methoden vervangen alle blokken in de blob, waardoor uw basisblob en de momentopnamen aanzienlijk uiteenlopen. Werk in plaats daarvan het zo min stmogelijke aantal blokken bij met behulp van de [Methoden PutBlock][dotnet_PutBlock] en [PutBlockList.][dotnet_PutBlockList]

### <a name="snapshot-billing-scenarios"></a>Factureringsscenario's voor momentopnamen

In de volgende scenario's wordt uitgelegd hoe kosten worden gegenereerd voor een blokblob en de momentopnamen.

#### <a name="scenario-1"></a>Scenario 1

In scenario 1 is de basisblob niet bijgewerkt nadat de momentopname is gemaakt, dus kosten worden alleen gemaakt voor unieke blokken 1, 2 en 3.

![Azure Storage-bronnen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

In scenario 2 is de basisblob bijgewerkt, maar de momentopname niet. Blok 3 is bijgewerkt en hoewel het dezelfde gegevens en dezelfde ID bevat, is het niet hetzelfde als blok 3 in de momentopname. Als gevolg hiervan wordt de rekening in rekening gebracht voor vier blokken.

![Azure Storage-bronnen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

In scenario 3 is de basisblob bijgewerkt, maar de momentopname niet. Blok 3 is vervangen door blok 4 in de basisblob, maar de momentopname weerspiegelt nog steeds blok 3. Als gevolg hiervan wordt de rekening in rekening gebracht voor vier blokken.

![Azure Storage-bronnen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

In scenario 4 is de basisblob volledig bijgewerkt en bevat het geen van de oorspronkelijke blokken. Als gevolg hiervan wordt het account in rekening gebracht voor alle acht unieke blokken. Dit scenario kan optreden als u een updatemethode gebruikt, zoals [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]of [UploadFromByteArray,][dotnet_UploadFromByteArray]omdat deze methoden alle inhoud van een blob vervangen.

![Azure Storage-bronnen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Volgende stappen

- U vindt meer informatie over het werken met vm-schijfmomentopnamen (virtual machine) in [Back-ups van onbeheerde VM-schijven met incrementele momentopnamen](../../virtual-machines/windows/incremental-snapshots.md)

- Zie Azure Code Samples voor aanvullende codevoorbeelden met [Blob-opslag](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). U een voorbeeldtoepassing downloaden en uitvoeren, of door de code bladeren op GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
