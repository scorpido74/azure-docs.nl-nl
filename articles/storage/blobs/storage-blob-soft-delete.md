---
title: Soft delete voor Azure Storage blobs | Microsoft Documenten
description: Azure Storage biedt nu soft delete voor blobobjecten, zodat u uw gegevens gemakkelijker herstellen wanneer deze ten onrechte worden gewijzigd of verwijderd door een toepassing of andere gebruiker van een opslagaccount.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4deae235ed15d02874ab5cb3470c62e934324364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234300"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Voorlopig verwijderen voor Azure Storage-blobs

Azure Storage biedt nu soft delete voor blobobjecten, zodat u uw gegevens gemakkelijker herstellen wanneer deze ten onrechte worden gewijzigd of verwijderd door een toepassing of andere gebruiker van een opslagaccount.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Hoe soft delete werkt

Wanneer dit is ingeschakeld, u met soft delete uw gegevens opslaan en herstellen wanneer blobs of blobmomentopnamen worden verwijderd. Deze bescherming strekt zich uit tot blobgegevens die worden gewist als gevolg van een overschrijven.

Wanneer gegevens worden verwijderd, wordt deze overgezet naar een zachte verwijderde status in plaats van permanent te worden gewist. Wanneer soft delete is ingeschakeld en u gegevens overschrijft, wordt een zachte verwijderde momentopname gegenereerd om de status van de overschreven gegevens op te slaan. Zachte verwijderde objecten zijn onzichtbaar, tenzij expliciet vermeld. U kunt configureren hoelang voorlopig verwijderde gegevens nog moeten kunnen worden hersteld voordat ze definitief verlopen.

Soft delete is backwards compatible, dus u hoeft geen wijzigingen aan te brengen in uw toepassingen om te profiteren van de beveiligingen die deze functie biedt. [Gegevensherstel](#recovery) introduceert echter een nieuwe **Undelete Blob** API.

### <a name="configuration-settings"></a>Configuratie-instellingen

Wanneer u een nieuw account maakt, is soft delete standaard uitgeschakeld. Soft delete is ook standaard uitgeschakeld voor bestaande opslagaccounts. U de functie op elk gewenst moment tijdens de levensduur van een opslagaccount in- en uitschakelen.

U nog steeds zachte verwijderde gegevens openen en herstellen wanneer de functie is uitgeschakeld, ervan uitgaande dat zachte verwijderde gegevens zijn opgeslagen toen de functie eerder was ingeschakeld. Wanneer u soft delete inschakelt, moet u ook de bewaartermijn configureren.

De bewaartermijn geeft aan hoeveel tijd zachte verwijderde gegevens worden opgeslagen en beschikbaar zijn voor herstel. Voor blobs en blobmomentopnamen die expliciet worden verwijderd, wordt de bewaarperiodeklok gestart wanneer de gegevens worden verwijderd. Voor zachte verwijderde momentopnamen die worden gegenereerd door de functie soft delete wanneer gegevens worden overschreven, wordt de klok gestart wanneer de momentopname wordt gegenereerd. Momenteel u zachte verwijderde gegevens tussen 1 en 365 dagen bewaren.

U de bewaartermijn voor zachte verwijdering op elk gewenst moment wijzigen. Een bijgewerkte bewaartermijn is alleen van toepassing op nieuw verwijderde gegevens. Eerder verwijderde gegevens verlopen op basis van de bewaartermijn die is geconfigureerd toen die gegevens zijn verwijderd. Een poging om een zacht verwijderd object te verwijderen heeft geen invloed op de vervaldatum.

### <a name="saving-deleted-data"></a>Verwijderde gegevens opslaan

Soft delete behoudt uw gegevens in veel gevallen waarin blobs of blobsnapshots worden verwijderd of overschreven.

Wanneer een blob wordt overschreven met **Put Blob**, **Put Block**, Put Block **List**of **Copy Blob** wordt een momentopname van de status van de blob voorafgaand aan de schrijfbewerking automatisch gegenereerd. Deze momentopname is een zachte verwijderde momentopname; het is onzichtbaar, tenzij zachte verwijderde objecten expliciet worden vermeld. Zie de sectie [Herstel](#recovery) voor meer informatie over het weergeven van zachte verwijderde objecten.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Zachte verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden weergegeven onder oudere gegevens. Wanneer B0 wordt overschreven met B1, wordt een zachte verwijderde momentopname van B0 gegenereerd. Wanneer B1 wordt overschreven met B2, wordt een zachte verwijderde momentopname van B1 gegenereerd.*

> [!NOTE]  
> Soft delete biedt alleen overschrijfbescherming voor kopieerbewerkingen wanneer deze is ingeschakeld voor het account van de doelblob.

> [!NOTE]  
> Soft delete biedt geen overschrijfbescherming voor blobs in de archieflaag. Als een blob in het archief wordt overschreven met een nieuwe blob in een bepaalde laag, is de overschreven blob definitief verlopen.

Wanneer **Blob verwijderen** wordt aangeroepen op een momentopname, wordt die momentopname gemarkeerd als soft verwijderd. Er wordt geen nieuwe momentopname gegenereerd.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Zachte verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden weergegeven onder oudere gegevens. Wanneer **Momentopnameblob** wordt aangeroepen, wordt B0 een momentopname en B1 de actieve status van de blob. Wanneer de B0-momentopname wordt verwijderd, wordt deze gemarkeerd als soft verwijderd.*

Wanneer **Blob verwijderen** wordt aangeroepen op een basisblob (een blob die zelf geen momentopname is), wordt die blob gemarkeerd als soft-verwijderd. In overeenstemming met eerder gedrag geeft u **Blob verwijderen** aan op een blob met actieve momentopnamen als een fout. Als **u Blob verwijderen** aanroept op een blob met zachte verwijderde momentopnamen, wordt er geen fout weergegeven. U nog steeds een blob verwijderen en alle momentopnamen in één bewerking wanneer soft delete is ingeschakeld. Hierdoor worden de basisblob en momentopnamen gemarkeerd als zacht verwijderd.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Zachte verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden weergegeven onder oudere gegevens. Hier wordt een **Blob-aanroep verwijderen** om B2 en alle bijbehorende momentopnamen te verwijderen. De actieve blob, B2 en alle bijbehorende momentopnamen worden gemarkeerd als soft verwijderd.*

> [!NOTE]  
> Wanneer een zachte verwijderde blob wordt overschreven, wordt automatisch een zachte verwijderde momentopname van de status van de blob gegenereerd voordat de schrijfbewerking wordt uitgevoerd. De nieuwe blob erft de laag van de overschreven blob.

Soft delete slaat uw gegevens niet op in gevallen van container- of accountverwijderingen, noch wanneer blobmetagegevens en blob-eigenschappen worden overschreven. Als u een opslagaccount wilt beschermen tegen onjuiste verwijdering, u een vergrendeling configureren met Azure Resource Manager. Zie het artikel Bronnen voor Azure Resource Manager [vergrendelen om onverwachte wijzigingen te voorkomen](../../azure-resource-manager/management/lock-resources.md) voor meer informatie.

In de volgende tabel wordt het verwachte gedrag beschreven wanneer soft delete is ingeschakeld:

| REST API-bewerking | Resourcetype | Beschrijving | Gedragsverandering |
|--------------------|---------------|-------------|--------------------|
| [Verwijderen](/rest/api/storagerp/StorageAccounts/Delete) | Account | Hiermee verwijdert u het opslagaccount, inclusief alle containers en blobs die het bevat.                           | Geen verandering. Containers en blobs in het verwijderde account kunnen niet worden hersteld. |
| [Container verwijderen](/rest/api/storageservices/delete-container) | Container | Hiermee verwijdert u de container, inclusief alle blobs die deze bevat. | Geen verandering. Blobs in de verwijderde container kunnen niet worden hersteld. |
| [Blob plaatsen](/rest/api/storageservices/put-blob) | Blobs blokkeren, toevoegen en pagina's | Hiermee maakt u een nieuwe blob of vervangt u een bestaande blob in een container | Als deze wordt gebruikt om een bestaande blob te vervangen, wordt automatisch een momentopname van de status van de blob voorafgaand aan de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde blob als en alleen als deze wordt vervangen door een blob van hetzelfde type (Blokkeren, Toevoegen of Pagina). Als deze wordt vervangen door een blob van een ander type, worden alle bestaande zachte verwijderde gegevens definitief verlopen. |
| [Blob verwijderen](/rest/api/storageservices/delete-blob) | Blobs blokkeren, toevoegen en pagina's | Hiermee markeert u een blob- of blobmomentopname voor verwijdering. De blob of momentopname wordt later verwijderd tijdens het ophalen van afval | Als deze wordt gebruikt om een blobmomentopname te verwijderen, wordt die momentopname gemarkeerd als soft-verwijderd. Als deze wordt gebruikt om een blob te verwijderen, wordt die blob gemarkeerd als soft delete. |
| [Blob kopiëren](/rest/api/storageservices/copy-blob) | Blobs blokkeren, toevoegen en pagina's | Hiermee kopieert u een bronblob naar een doelblob in hetzelfde opslagaccount of in een ander opslagaccount. | Als deze wordt gebruikt om een bestaande blob te vervangen, wordt automatisch een momentopname van de status van de blob voorafgaand aan de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde blob als en alleen als deze wordt vervangen door een blob van hetzelfde type (Blokkeren, Toevoegen of Pagina). Als deze wordt vervangen door een blob van een ander type, worden alle bestaande zachte verwijderde gegevens definitief verlopen. |
| [Zet blok](/rest/api/storageservices/put-block) | Blok-blobs | Hiermee maakt u een nieuw blok dat moet worden toegewezen als onderdeel van een blokblob. | Als wordt gebruikt om een blok te verbinden aan een blob die actief is, is er geen verandering. Als wordt gebruikt om een blok te verbinden aan een blob die zacht wordt verwijderd, wordt er een nieuwe blob gemaakt en wordt er automatisch een momentopname gegenereerd om de status van de zachte verwijderde blob vast te leggen. |
| [Lijst met blokkeringplaatsen plaatsen](/rest/api/storageservices/put-block-list) | Blok-blobs | Commits een blob door de set blok-id's op te geven die de blokblob bevatten. | Als deze wordt gebruikt om een bestaande blob te vervangen, wordt automatisch een momentopname van de status van de blob voorafgaand aan de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde blob als en alleen als het een Block Blob. Als deze wordt vervangen door een blob van een ander type, worden alle bestaande zachte verwijderde gegevens definitief verlopen. |
| [Pagina plaatsen](/rest/api/storageservices/put-page) | Pagina-blobs | Hiermee schrijft u een reeks pagina's naar een paginablob. | Geen verandering. PaginaBlob-gegevens die met deze bewerking worden overschreven of gewist, worden niet opgeslagen en kunnen niet worden hersteld. |
| [Blok toevoegen](/rest/api/storageservices/append-block) | Toevoeg-blobs | Schrijft een blok gegevens aan het einde van een toevoegenblob | Geen verandering. |
| [Blob-eigenschappen instellen](/rest/api/storageservices/set-blob-properties) | Blobs blokkeren, toevoegen en pagina's | Hiermee stelt u waarden in voor systeemeigenschappen die zijn gedefinieerd voor een blob. | Geen verandering. Overschreven blob-eigenschappen kunnen niet worden hersteld. |
| [Blob-metagegevens instellen](/rest/api/storageservices/set-blob-metadata) | Blobs blokkeren, toevoegen en pagina's | Hiermee stelt u door de gebruiker gedefinieerde metagegevens in voor de opgegeven blob als een of meer naamwaardeparen. | Geen verandering. Overschreven blobmetagegevens kunnen niet worden hersteld. |

Het is belangrijk op te merken dat het aanroepen van 'Pagina zetten' om bereiken van een paginablob te overschrijven of te wissen, niet automatisch momentopnamen genereert. Virtuele machineschijven worden ondersteund door Paginablobs en gebruiken **Put Page** om gegevens te schrijven.

### <a name="recovery"></a>Herstel

Als u de [blob-bewerking verwijderen](/rest/api/storageservices/undelete-blob) aanroept op een zachte verwijderde basisblob, wordt deze hersteld en worden alle bijbehorende zachte verwijderde momentopnamen actief. Als `Undelete Blob` u de bewerking aanroept op een actieve basisblob, worden alle bijbehorende zachte verwijderde momentopnamen als actief hersteld. Wanneer momentopnamen als actief worden hersteld, zien ze eruit als door gebruikers gegenereerde momentopnamen; ze overschrijven de basisblob niet.

Als u een blob wilt herstellen naar een `Undelete Blob` specifieke zachte verwijderde momentopname, u een beroep doen op de basisblob. Vervolgens u de momentopname kopiëren via de nu actieve blob. U de momentopname ook kopiëren naar een nieuwe blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Zachte verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden weergegeven onder oudere gegevens. Hier wordt **Blob verwijderen** aangeroepen op blob B, waardoor de basisblob, B1 en alle bijbehorende momentopnamen, hier alleen B0, zo actief worden hersteld. In de tweede stap wordt B0 gekopieerd over de basisblob. Deze kopieerbewerking genereert een zachte verwijderde momentopname van B1.*

Als u zachte verwijderde blobs en blobmomentopnamen wilt weergeven, u ervoor kiezen verwijderde gegevens op te nemen in **Lijstblobs.** U ervoor kiezen om alleen zachte verwijderde basisblobs weer te geven of om ook zachte verwijderde blob-snapshots op te nemen. Voor alle zachte verwijderde gegevens u het tijdstip waarop de gegevens zijn verwijderd en het aantal dagen voordat de gegevens definitief verlopen, bekijken.

### <a name="example"></a>Voorbeeld

Het volgende is de console-uitvoer van een .NET-script dat een blob met de naam *HelloWorld* uploadt, overschrijft, overschrijft, verwijdert en herstelt wanneer soft delete is ingeschakeld:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Zie de sectie [Volgende stappen](#next-steps) voor een aanwijzer naar de toepassing die deze uitvoer heeft geproduceerd.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Alle zachte verwijderde gegevens worden in dezelfde snelheid gefactureerd als actieve gegevens. Er worden geen kosten in rekening gebracht voor gegevens die permanent worden verwijderd na de geconfigureerde bewaarperiode. Zie [Inzicht in hoe momentopnamen kosten genereren](storage-blob-snapshots.md)voor een diepere duik in momentopnamen en hoe deze kosten genereren.

U wordt niet gefactureerd voor de transacties met betrekking tot het automatisch genereren van momentopnamen. Er worden kosten in rekening gebracht voor **Blob-transacties verwijderen** tegen het tarief voor schrijfbewerkingen.

Ga voor meer informatie over de prijzen voor Azure Blob Storage in het algemeen naar de [pagina Azure Blob Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/).

Wanneer u in eerste instantie soft delete inschakelt, raden we u aan een kleine bewaartermijn te gebruiken om beter te begrijpen hoe de functie uw factuur zal beïnvloeden.

## <a name="get-started"></a>Aan de slag

In de volgende stappen wordt uitgelegd hoe u aan de slag met soft delete.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Schakel soft delete in voor blobs op uw opslagaccount met azure portal:

1. Selecteer uw opslagaccount in de [Azure-portal.](https://portal.azure.com/) 

2. Navigeer naar de optie **Gegevensbescherming** onder **Blob-service**.

3. Klik op **Ingeschakeld** onder **Klodder zachte verwijderen**

4. Voer het aantal dagen in waarvoor u wilt *behouden* onder **Bewaarbeleid**

5. Kies de knop **Opslaan** om de instellingen voor gegevensbescherming te bevestigen

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Als u zachte verwijderde blobs wilt weergeven, schakelt u het selectievakje **Verwijderde blobs weergeven** in.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Als u zachte verwijderde momentopnamen voor een bepaalde blob wilt weergeven, selecteert u de blob en klikt u op **Momentopnamen weergeven**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Controleer of het selectievakje **Verwijderde momentopnamen weergeven** is ingeschakeld.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Wanneer u op een zachte verwijderde blob of momentopname klikt, ziet u de nieuwe blob-eigenschappen. Ze geven aan wanneer het object is verwijderd en hoeveel dagen er nog over zijn totdat de blob- of blobmomentopname definitief is verlopen. Als het zachte verwijderde object geen momentopname is, hebt u ook de optie om het te verwijderen.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Vergeet niet dat het verwijderen van een blob ook alle bijbehorende momentopnamen ongedaan maakt. Als u zachte verwijderde momentopnamen voor een actieve blob wilt verwijderen, klikt u op de blob en selecteert u **Alle momentopnamen verwijderen.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Zodra u de momentopnamen van een blob verwijdert, u op **Promoten** klikken om een momentopname over de hoofdblob te kopiëren, waardoor de blob naar de momentopname wordt hersteld.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u soft delete wilt inschakelen, werkt u de serviceeigenschappen van een blobclient bij. In het volgende voorbeeld wordt soft delete voor een subset van accounts in een abonnement mogelijk:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
U controleren of soft delete is ingeschakeld met de volgende opdracht:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Als u blobs wilt herstellen die per ongeluk zijn verwijderd, roept u Verwijderen op die blobs aan. Houd er rekening mee dat het aanroepen **van Undelete Blob**, zowel op actieve als zachte verwijderde blobs, alle bijbehorende zachte verwijderde momentopnamen als actief wordt hersteld. In het volgende voorbeeld wordt Delete verwijderd op alle zachte verwijderde en actieve blobs in een container:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Als u het huidige bewaarbeleid voor zachte verwijderen wilt vinden, gebruikt u de volgende opdracht:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Als u soft delete wilt inschakelen, werkt u de serviceeigenschappen van een blobclient bij:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Als u wilt controleren of soft delete is ingeschakeld, gebruikt u de volgende opdracht: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Als u soft delete wilt inschakelen, werkt u de serviceeigenschappen van een blobclient bij:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net"></a>[.NET](#tab/net)

Als u soft delete wilt inschakelen, werkt u de serviceeigenschappen van een blobclient bij:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Als u blobs wilt herstellen die per ongeluk zijn verwijderd, roept u Verwijderen op die blobs aan. Houd er rekening mee dat het aanroepen **van Undelete Blob**, zowel op actieve als zachte verwijderde blobs, alle bijbehorende zachte verwijderde momentopnamen als actief wordt hersteld. In het volgende voorbeeld wordt Delete verwijderd op alle zachte verwijderde en actieve blobs in een container:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Als u wilt herstellen naar een specifieke blobversie, belt u eerst Verwijderen op een blob en kopieert u vervolgens de gewenste momentopname over de blob. In het volgende voorbeeld wordt een blokblob hersteld naar de meest recent gegenereerde momentopname:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

## <a name="special-considerations"></a>Bijzondere overwegingen

Als er een kans bestaat dat uw gegevens per ongeluk worden gewijzigd of verwijderd door een toepassing of een andere gebruiker van een opslagaccount, wordt het inschakelen van soft delete aanbevolen. Het inschakelen van soft delete voor vaak overschreven gegevens kan leiden tot hogere kosten voor de opslagcapaciteit en een verhoogde latentie bij het aanbieden van blobs. U deze extra kosten en latentie beperken door de vaak overschreven gegevens op te slaan in een afzonderlijk opslagaccount waar soft delete is uitgeschakeld. 

## <a name="faq"></a>Veelgestelde vragen

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Voor welke opslagservices kan ik soft delete gebruiken?

Momenteel is soft delete alleen beschikbaar voor blob (object) opslag.

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Is soft delete beschikbaar voor alle opslagaccounttypen?

Ja, soft delete is beschikbaar voor Blob-opslagaccounts en voor blobs in algemene doeleinden (zowel GPv1- als GPv2-opslagaccounts. Zowel standaard als premium accounttypes worden ondersteund. Soft delete is beschikbaar voor onbeheerde schijven, die paginablobs onder de covers zijn. Soft delete is niet beschikbaar voor beheerde schijven.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Is soft delete beschikbaar voor alle opslaglagen?

Ja, soft delete is beschikbaar voor alle opslaglagen, waaronder hot, cool en archief. Soft delete biedt echter geen overschrijfbescherming voor blobs in de archieflaag.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Kan ik de API Blob Tier instellen op blobs met zachte verwijderde momentopnamen?

Ja. De zachte verwijderde momentopnamen blijven in de oorspronkelijke laag, maar de basisblob wordt verplaatst naar de nieuwe laag. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium-opslagaccounts hebben een momentopnamelimiet per blob van 100. Tellen zachte verwijderde momentopnamen mee voor deze limiet?

Nee, zachte verwijderde momentopnamen tellen niet mee voor deze limiet.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Kan ik soft delete inschakelen voor bestaande opslagaccounts?

Ja, soft delete is configureerbaar voor zowel bestaande als nieuwe opslagaccounts.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Als ik een hele account of container verwijder waarbij soft delete is ingeschakeld, worden dan alle bijbehorende blobs opgeslagen?

Nee, als u een volledig account of container verwijdert, worden alle bijbehorende blobs definitief verwijderd. Zie [Resources vergrendelen om onverwachte wijzigingen te voorkomen](../../azure-resource-manager/management/lock-resources.md)voor meer informatie over het beveiligen van een opslagaccount tegen onbedoelde verwijderingen.

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Kan ik capaciteitsstatistieken voor verwijderde gegevens weergeven?

Zachte verwijderde gegevens worden opgenomen als onderdeel van de totale opslagcapaciteit van uw opslagaccount. Zie [Storage Analytics](../common/storage-analytics.md)voor meer informatie over het bijhouden en bewaken van de opslagcapaciteit.

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Als ik soft delete uitschakel, heb ik dan nog steeds toegang tot zachte verwijderde gegevens?

Ja, u nog steeds niet-verlopen zachte verwijderde gegevens openen en herstellen wanneer soft delete is uitgeschakeld.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Kan ik zachte verwijderde momentopnamen van mijn blob lezen en kopiëren?  

Ja, maar u moet undelete eerst op de blob aanroepen.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Is soft delete beschikbaar voor alle blobtypen?

Ja, soft delete is beschikbaar voor blokblobs, toevoegenblobs en paginablobs.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Is soft delete beschikbaar voor virtuele machineschijven?  

Soft delete is beschikbaar voor zowel premium als standaard onbeheerde schijven, die paginablobs onder de covers zijn. Soft delete helpt u alleen gegevens te herstellen die zijn verwijderd door **Blob verwijderen,** **Blob plaatsen,** **Bloklijst plaatsen,** **Blob-bewerkingen plaatsen** en **kopiëren.** Gegevens die door een oproep naar **Put-pagina** worden overschreven, kunnen niet worden hersteld.

Een virtuele Azure-machine schrijft naar een onbeheerde schijf met behulp van oproepen naar **Put-pagina,** dus het gebruik van soft delete om schrijfbewerkingen ongedaan te maken naar een onbeheerde schijf van een Azure VM is geen ondersteund scenario.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Moet ik mijn bestaande toepassingen wijzigen om soft delete te gebruiken?

Het is mogelijk om te profiteren van soft delete, ongeacht de API-versie die u gebruikt. Als u echter zachte verwijderde blobs en blob-momentopnamen wilt weergeven en herstellen, moet u versie 2017-07-29 van de [REST-API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) voor opslagservices of meer gebruiken. Microsoft raadt aan altijd de nieuwste versie van de Azure Storage API te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [.NET-voorbeeldcode](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
* [Azure-opslagreplicatie](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Ontwerpen van zeer beschikbare toepassingen met RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Failover-geheugen en opslagaccount (preview) in Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
