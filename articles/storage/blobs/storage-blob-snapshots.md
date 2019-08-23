---
title: Een alleen-lezen momentopname van een BLOB maken in Azure Storage | Microsoft Docs
description: Leer hoe u een moment opname van een BLOB maakt om op een bepaald moment een back-up te maken van BLOB-gegevens. Meer informatie over hoe moment opnamen worden gefactureerd en hoe u deze kunt gebruiken om de capaciteits kosten te minimaliseren.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 0da3373ba2c13bd6a00a92a6b38bead86fc9a5ea
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897015"
---
# <a name="create-a-blob-snapshot"></a>Een blob-momentopname maken

Een moment opname is een alleen-lezen versie van een blob die op een bepaald moment wordt uitgevoerd. Moment opnamen zijn handig voor het maken van back-ups van blobs. Nadat u een moment opname hebt gemaakt, kunt u deze lezen, kopiëren of verwijderen, maar u kunt deze niet wijzigen.

Een moment opname van een blob is identiek aan de basis-blob, behalve dat de BLOB-URI een **datum/tijd** -waarde heeft toegevoegd aan de BLOB-URI om het tijdstip aan te geven waarop de moment opname is gemaakt. Als de URI van een pagina-BLOB bijvoorbeeld `http://storagesample.core.blob.windows.net/mydrives/myvhd`is, is de moment opname- `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`URI vergelijkbaar met.

> [!NOTE]
> Alle moment opnamen delen de URI van de basis-blob. Het enige verschil tussen de basis-Blob en de moment opname is de toegevoegde **DateTime** -waarde.
>

Een BLOB kan een wille keurig aantal moment opnamen hebben. Moment opnamen blijven behouden totdat ze expliciet worden verwijderd. Een moment opname kan de basis-BLOB niet onactief maken. U kunt de moment opnamen die zijn gekoppeld aan de basis-BLOB opsommen om uw huidige moment opnamen bij te houden.

Wanneer u een moment opname van een BLOB maakt, worden de systeem eigenschappen van de BLOB gekopieerd naar de moment opname met dezelfde waarden. De meta gegevens van de basis-BLOB worden ook gekopieerd naar de moment opname, tenzij u afzonderlijke meta gegevens voor de moment opname opgeeft wanneer u deze maakt.

Alle leases die zijn gekoppeld aan de basis-blob, hebben geen invloed op de moment opname. U kunt geen lease verkrijgen voor een moment opname.

Een VHD-bestand wordt gebruikt voor het opslaan van de huidige informatie en status voor een VM-schijf. U kunt een schijf loskoppelen van in de VM of de virtuele machine afsluiten en vervolgens een moment opname maken van het VHD-bestand. U kunt dit momentopname bestand later gebruiken om het VHD-bestand op dat moment op te halen en de virtuele machine opnieuw te maken.

## <a name="create-a-snapshot"></a>Een momentopname maken
In het volgende code voorbeeld ziet u hoe u een moment opname maakt met behulp van de [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage/client). In dit voor beeld worden aanvullende meta gegevens voor de moment opname opgegeven wanneer deze wordt gemaakt.

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
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
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

## <a name="copy-snapshots"></a>Moment opnamen kopiëren
Voor het kopiëren van bewerkingen met blobs en moment opnamen gelden de volgende regels:

* U kunt een moment opname kopiëren via de bijbehorende basis-blob. Door een moment opname te promo veren tot de positie van de basis-blob, kunt u een eerdere versie van een BLOB herstellen. De moment opname blijft, maar de basis-BLOB wordt overschreven met een Beschrijf bare kopie van de moment opname.
* U kunt een moment opname kopiëren naar een doel-blob met een andere naam. De resulterende doel-blob is een Beschrijf bare Blob en geen moment opname.
* Wanneer een bron-BLOB wordt gekopieerd, worden alle moment opnamen van de bron-BLOB niet gekopieerd naar de bestemming. Wanneer een bestemmings-BLOB wordt overschreven met een kopie, blijven de moment opnamen die zijn gekoppeld aan de oorspronkelijke doel-BLOB intact.
* Wanneer u een moment opname van een blok-BLOB maakt, wordt de lijst vastgelegde blokken van de BLOB ook gekopieerd naar de moment opname. Eventuele niet-doorgevoerde blokken worden niet gekopieerd.

## <a name="specify-an-access-condition"></a>Een toegangs voorwaarde opgeven
Wanneer u [CreateSnapshotAsync][dotnet_CreateSnapshotAsync]aanroept, kunt u een toegangs voorwaarde opgeven zodat de moment opname alleen wordt gemaakt als aan een voor waarde wordt voldaan. Als u een toegangs voorwaarde wilt opgeven, gebruikt u de para meter [AccessCondition][dotnet_AccessCondition] . Als niet aan de opgegeven voor waarde wordt voldaan, wordt de moment opname niet gemaakt en retourneert de Blob service status code [HTTP status code][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Moment opnamen verwijderen
U kunt een blob met moment opnamen alleen verwijderen als de moment opnamen ook worden verwijderd. U kunt een moment opname afzonderlijk verwijderen of opgeven dat alle moment opnamen moeten worden verwijderd wanneer de bron-BLOB wordt verwijderd. Als u probeert een BLOB te verwijderen die nog moment opnamen bevat, treedt er een fout op.

In het volgende code voorbeeld ziet u hoe u een BLOB en de bijbehorende moment opnamen in .net `blockBlob` verwijdert, waarbij een object van het type [CloudBlockBlob][dotnet_CloudBlockBlob]is:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Moment opnamen met Azure Premium Storage
Bij het gebruik van moment opnamen met Premium Storage gelden de volgende regels:

* Het maximum aantal moment opnamen per pagina-Blob in een Premium Storage-account is 100. Als deze limiet wordt overschreden, wordt fout code 409 (`SnapshotCountExceeded`) geretourneerd door de BLOB-bewerking van de moment opname.
* U kunt een moment opname maken van een pagina-Blob in een Premium Storage-account om de 10 minuten. Als dat aantal wordt overschreden, retourneert de BLOB-bewerking van de moment opname`SnapshotOperationRateExceeded`fout code 409 ().
* Als u een moment opname wilt lezen, kunt u de bewerking BLOB kopiëren gebruiken om een moment opname te kopiëren naar een andere pagina-Blob in het account. De doel-BLOB voor de Kopieer bewerking mag geen bestaande moment opnamen hebben. Als de doel-BLOB moment opnamen heeft, wordt de fout code 409 (`SnapshotsPresent`) door de bewerking BLOB kopiëren geretourneerd.

## <a name="return-the-absolute-uri-to-a-snapshot"></a>De absolute URI retour neren naar een moment opname
In C# dit code voorbeeld maakt u een moment opname en schrijft u de absolute URI voor de primaire locatie.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Meer informatie over hoe moment opnamen kosten samen voegen
Het maken van een moment opname, een alleen-lezen kopie van een blob, kan leiden tot extra kosten voor gegevens opslag voor uw account. Bij het ontwerpen van uw toepassing is het belang rijk om te weten hoe deze kosten kunnen toenemen, zodat u de kosten kunt minimaliseren.

### <a name="important-billing-considerations"></a>Belang rijke overwegingen met betrekking tot facturering
De volgende lijst bevat belang rijke punten waarmee u rekening moet houden bij het maken van een moment opname.

* In uw opslag account worden kosten in rekening gebracht voor unieke blokken of pagina's, ongeacht of deze zich in de BLOB of in de moment opname bevinden. Voor uw account worden geen extra kosten in rekening gebracht voor moment opnamen die zijn gekoppeld aan een BLOB totdat u de BLOB bijwerkt waarop ze zijn gebaseerd. Nadat u de basis-BLOB hebt bijgewerkt, is deze afwijkend van de moment opnamen. Als dit gebeurt, worden er kosten in rekening gebracht voor de unieke blokken of pagina's in elke BLOB of moment opname.
* Wanneer u een blok in een blok-BLOB vervangt, wordt dat blok vervolgens als een uniek blok in rekening gebracht. Dit geldt ook als het blok dezelfde blok-ID en dezelfde gegevens bevat als in de moment opname. Nadat het blok opnieuw is doorgevoerd, is het afwijkend van de tegen hanger in een moment opname en worden de gegevens in rekening gebracht. Hetzelfde geldt voor een pagina in een pagina-blob die wordt bijgewerkt met identieke gegevens.
* Als u een blok-BLOB vervangt door de [UploadFromFile][dotnet_UploadFromFile]-, [UploadText][dotnet_UploadText]-, [UploadFromStream][dotnet_UploadFromStream]-of [UploadFromByteArray][dotnet_UploadFromByteArray] -methode aan te roepen, worden alle blokken in de BLOB vervangen. Als u een moment opname hebt die is gekoppeld aan die blob, zijn alle blokken in de basis-Blob en snap shot nu afwijkend en worden er kosten in rekening gebracht voor alle blokken in beide blobs. Dit geldt ook als de gegevens in de basis-Blob en de moment opname identiek blijven.
* De Azure-Blob service heeft geen manier om te bepalen of twee blokken identieke gegevens bevatten. Elk blok dat wordt geüpload en doorgevoerd, wordt behandeld als uniek, zelfs als het dezelfde gegevens en dezelfde blok-ID heeft. Omdat kosten toenemen voor unieke blokken, is het belang rijk om te overwegen dat het bijwerken van een blob met een moment opname resulteert in extra unieke blokken en extra kosten.

### <a name="minimize-cost-with-snapshot-management"></a>Kosten minimaliseren met snap shot Management

We raden u aan uw moment opnamen zorgvuldig te beheren om extra kosten te voor komen. U kunt deze aanbevolen procedures volgen om de kosten te minimaliseren die worden gemaakt door de opslag van uw moment opnamen:

* U kunt moment opnamen die zijn gekoppeld aan een blob, verwijderen en opnieuw maken wanneer u de BLOB bijwerkt, zelfs als u een update uitvoert met identieke gegevens, tenzij uw toepassings ontwerp vereist dat u moment opnamen bijhoudt. Door de moment opnamen van de BLOB te verwijderen en opnieuw te maken, kunt u ervoor zorgen dat de BLOB en de moment opnamen niet afwijken.
* Als u moment opnamen voor een BLOB onderhoudt, vermijdt u het aanroepen van [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]of [UploadFromByteArray][dotnet_UploadFromByteArray] om de BLOB bij te werken. Deze methoden vervangen alle blokken in de blob, waardoor uw basis-Blob en de bijbehorende moment opnamen aanzienlijk afwijken. Werk in plaats daarvan het minste mogelijke aantal blokken bij met behulp van de methoden [PutBlock][dotnet_PutBlock] en [putblock List][dotnet_PutBlockList] .

### <a name="snapshot-billing-scenarios"></a>Facturerings scenario's voor moment opnamen
De volgende scenario's laten zien hoe kosten toenemen voor een blok-Blob en de bijbehorende moment opnamen.

**Scenario 1**

In scenario 1 is de basis-BLOB niet bijgewerkt nadat de moment opname is gemaakt, dus worden er alleen kosten in rekening gebracht voor unieke blokken 1, 2 en 3.

![Azure Storage resources](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Scenario 2**

In scenario 2 is de basis-BLOB bijgewerkt, maar niet de moment opname. Blok 3 is bijgewerkt en hoewel het dezelfde gegevens en dezelfde ID bevat, is dit niet hetzelfde als blok 3 in de moment opname. Als gevolg hiervan wordt het account in rekening gebracht voor vier blokken.

![Azure Storage resources](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Scenario 3**

In scenario 3 is de basis-BLOB bijgewerkt, maar niet de moment opname. Blok 3 is vervangen door blok 4 in de basis-blob, maar de moment opname komt nog steeds overeen met blok 3. Als gevolg hiervan wordt het account in rekening gebracht voor vier blokken.

![Azure Storage resources](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Scenario 4**

In scenario 4 is de basis-BLOB volledig bijgewerkt en bevat deze geen van de oorspronkelijke blokken. Als gevolg hiervan wordt het account in rekening gebracht voor alle acht unieke blokken. Dit scenario kan zich voordoen als u een update methode gebruikt, zoals [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]of [UploadFromByteArray][dotnet_UploadFromByteArray], omdat deze methoden alle inhoud van een BLOB vervangen.

![Azure Storage resources](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het werken met moment opnamen van virtuele machines (VM) vindt u in [back-ups van Azure unmanaged VM-schijven met incrementele moment opnamen](../../virtual-machines/windows/incremental-snapshots.md)

* Zie [Azure-code voorbeelden](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)voor aanvullende code voorbeelden met behulp van Blob Storage. U kunt een voorbeeld toepassing downloaden en uitvoeren, of door de code bladeren op GitHub.

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