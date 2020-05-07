---
title: Zacht verwijderen voor Azure Storage-blobs | Microsoft Docs
description: Azure Storage biedt nu een tijdelijke verwijdering voor blob-objecten, zodat u uw gegevens eenvoudiger kunt herstellen wanneer deze foutief wordt gewijzigd of verwijderd door een toepassing of een andere gebruiker van het opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 389dea74f5002cb09d7683947356d236ea8d338b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858704"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Voorlopig verwijderen voor Azure Storage-blobs

Azure Storage biedt nu een tijdelijke verwijdering voor blob-objecten, zodat u uw gegevens eenvoudiger kunt herstellen wanneer deze foutief wordt gewijzigd of verwijderd door een toepassing of een andere gebruiker van het opslag account.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Hoe zacht verwijderen werkt

Wanneer deze optie is ingeschakeld, kunt u met zacht verwijderen uw gegevens opslaan en herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd. Deze beveiliging wordt uitgebreid naar BLOB-gegevens die worden gewist als gevolg van een overschrijving.

Wanneer gegevens worden verwijderd, wordt deze overgezet naar een voorlopig verwijderde status in plaats van dat ze permanent worden gewist. Als zacht verwijderen is ingeschakeld en u gegevens overschrijft, wordt een voorlopig verwijderde moment opname gegenereerd om de status van de overschreven gegevens op te slaan. Voorlopig verwijderde objecten zijn onzichtbaar, tenzij expliciet wordt vermeld. U kunt configureren hoelang voorlopig verwijderde gegevens nog moeten kunnen worden hersteld voordat ze definitief verlopen.

Zacht verwijderen is achterwaarts compatibel, dus u hoeft geen wijzigingen aan te brengen in uw toepassingen om te profiteren van de beveiligingen die deze functie biedt. Met [gegevens herstel](#recovery) wordt echter een nieuwe **undelete BLOB** API geïntroduceerd.

### <a name="configuration-settings"></a>Configuratie-instellingen

Wanneer u een nieuw account maakt, is zacht verwijderen standaard uitgeschakeld. Voorlopig verwijderen is standaard uitgeschakeld voor bestaande opslag accounts. U kunt de functie op elk gewenst moment in-of uitschakelen tijdens de levens duur van een opslag account.

Als de functie is uitgeschakeld, hebt u nog steeds toegang tot de dynamische, verwijderde gegevens en wordt ervan uitgegaan dat de gegevens die worden verwijderd, zijn opgeslagen toen de functie werd ingeschakeld. Wanneer u zacht verwijderen inschakelt, moet u ook de Bewaar periode configureren.

De retentie periode geeft de hoeveelheid tijd aan dat de gegevens die door zacht worden verwijderd worden opgeslagen en beschikbaar zijn voor herstel. Voor blobs en BLOB-moment opnamen die expliciet worden verwijderd, wordt de klok van de Bewaar periode gestart wanneer de gegevens worden verwijderd. Voor tijdelijke verwijderde moment opnamen die zijn gegenereerd door de functie voor voorlopig verwijderen wanneer gegevens worden overschreven, wordt de klok gestart wanneer de moment opname wordt gegenereerd. Op dit moment kunt u voorlopig verwijderde gegevens tussen 1 en 365 dagen bewaren.

U kunt de tijdelijke Bewaar periode voor verwijderen op elk gewenst moment wijzigen. Een bijgewerkte Bewaar periode geldt alleen voor recentelijk verwijderde gegevens. Eerder verwijderde gegevens verlopen op basis van de Bewaar periode die is geconfigureerd tijdens het verwijderen van de gegevens. Het verwijderen van een voorlopig verwijderd object heeft geen invloed op de verloop tijd.

### <a name="saving-deleted-data"></a>Verwijderde gegevens worden opgeslagen

Met zacht verwijderen worden uw gegevens in veel gevallen bewaard, waarbij blobs of BLOB-moment opnamen worden verwijderd of overschreven.

Wanneer een BLOB wordt overschreven met behulp van **put-BLOB**, **put-blok**, **put-lijst**of- **BLOB kopiëren** een moment opname van de status van de BLOB voordat de schrijf bewerking wordt uitgevoerd, wordt automatisch gegenereerd. Deze moment opname is een voorlopig verwijderde moment opname. het is onzichtbaar, tenzij tijdelijke verwijderde objecten expliciet worden weer gegeven. Zie de sectie [herstel](#recovery) voor meer informatie over het weer geven van zachte verwijderde objecten.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Zacht verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden onder oudere gegevens weer gegeven. Als B0 wordt overschreven met B1, wordt er een voorlopig verwijderde moment opname van B0 gegenereerd. Als B1 wordt overschreven met B2, wordt er een voorlopig verwijderde moment opname van B1 gegenereerd.*

> [!NOTE]  
> Bij zacht verwijderen wordt de beveiliging van de overschrijving voor kopieer bewerkingen alleen geboden wanneer deze is ingeschakeld voor het account van de doel-blob.

> [!NOTE]  
> Bij zacht verwijderen wordt de beveiliging van blobs in de opslaglaag niet overschreven. Als een BLOB in archief wordt overschreven door een nieuwe Blob in een wille keurige laag, wordt de overschreven BLOB permanent verlopen.

Wanneer **Delete BLOB** wordt aangeroepen voor een moment opname, wordt die moment opname gemarkeerd als zacht verwijderd. Er wordt geen nieuwe moment opname gegenereerd.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Zacht verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden onder oudere gegevens weer gegeven. Als **moment opname-BLOB** wordt aangeroepen, wordt B0 een moment opname en B1 de actieve status van de blob. Wanneer de B0-moment opname wordt verwijderd, wordt deze als zacht verwijderd gemarkeerd.*

Wanneer **Delete BLOB** wordt aangeroepen op een basis-BLOB (een blob die niet zelf een moment opname is), wordt die BLOB gemarkeerd als zacht verwijderd. Consistent met het vorige gedrag: het aanroepen van **Delete BLOB** op een blob met actieve moment opnamen retourneert een fout. Als u **Delete BLOB** aanroept voor een blob met tijdelijke verwijderde moment opnamen, wordt er geen fout geretourneerd. U kunt nog steeds een BLOB en alle bijbehorende moment opnamen in één bewerking verwijderen wanneer de functie voor voorlopig verwijderen is ingeschakeld. Hiermee markeert u de basis-Blob en moment opnamen als zacht verwijderd.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Zacht verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden onder oudere gegevens weer gegeven. Hier wordt een **Delete BLOB** -aanroep gemaakt om B2 en alle bijbehorende moment opnamen te verwijderen. De actieve blob, B2 en alle bijbehorende moment opnamen zijn gemarkeerd als zacht verwijderd.*

> [!NOTE]  
> Wanneer een zachte verwijderde BLOB wordt overschreven, wordt er automatisch een voorlopig verwijderde moment opname van de status van de BLOB vóór de schrijf bewerking gegenereerd. De nieuwe BLOB neemt de laag over van de overschreven blob.

Met zacht verwijderen worden uw gegevens niet opgeslagen in gevallen waarin containers of accounts worden verwijderd, noch wanneer meta gegevens en BLOB-eigenschappen worden overschreven. Als u een opslag account wilt beveiligen tegen foutieve verwijdering, kunt u een vergren deling configureren met behulp van de Azure Resource Manager. Raadpleeg het Azure Resource Manager artikel [resources vergren delen om te voor komen dat er onverwachte wijzigingen](../../azure-resource-manager/management/lock-resources.md) meer zijn.

De volgende tabel bevat details over het verwachte gedrag wanneer zacht verwijderen is ingeschakeld:

| REST API bewerking | Resourcetype | Beschrijving | Wijziging in gedrag |
|--------------------|---------------|-------------|--------------------|
| [Verwijderen](/rest/api/storagerp/StorageAccounts/Delete) | Account | Hiermee verwijdert u het opslag account, inclusief alle containers en blobs die het bevat.                           | Geen wijziging. Containers en blobs in het verwijderde account kunnen niet worden hersteld. |
| [Container verwijderen](/rest/api/storageservices/delete-container) | Container | Hiermee verwijdert u de container, inclusief alle blobs die deze bevat. | Geen wijziging. Blobs in de verwijderde container kunnen niet worden hersteld. |
| [BLOB plaatsen](/rest/api/storageservices/put-blob) | Blok-, toevoeg-en pagina-blobs | Hiermee maakt u een nieuwe BLOB of vervangt u een bestaande BLOB binnen een container | Als deze wordt gebruikt om een bestaande BLOB te vervangen, wordt automatisch een moment opname van de status van de BLOB vóór de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde BLOB als en alleen als deze wordt vervangen door een BLOB van hetzelfde type (blok, toevoegen of pagina). Als deze wordt vervangen door een BLOB van een ander type, worden alle bestaande voorlopig verwijderde gegevens permanent verlopen. |
| [Blob verwijderen](/rest/api/storageservices/delete-blob) | Blok-, toevoeg-en pagina-blobs | Hiermee wordt een BLOB-of BLOB-moment opname gemarkeerd voor verwijdering. De BLOB of moment opname wordt later verwijderd tijdens het opschonen van de verzameling | Als deze wordt gebruikt om een BLOB-moment opname te verwijderen, wordt die moment opname gemarkeerd als zacht verwijderd. Als deze wordt gebruikt om een BLOB te verwijderen, wordt die BLOB gemarkeerd als zacht verwijderd. |
| [BLOB kopiëren](/rest/api/storageservices/copy-blob) | Blok-, toevoeg-en pagina-blobs | Kopieert een bron-BLOB naar een bestemmings-Blob in hetzelfde opslag account of in een ander opslag account. | Als deze wordt gebruikt om een bestaande BLOB te vervangen, wordt automatisch een moment opname van de status van de BLOB vóór de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde BLOB als en alleen als deze wordt vervangen door een BLOB van hetzelfde type (blok, toevoegen of pagina). Als deze wordt vervangen door een BLOB van een ander type, worden alle bestaande voorlopig verwijderde gegevens permanent verlopen. |
| [Blok keren](/rest/api/storageservices/put-block) | Blok-blobs | Hiermee maakt u een nieuw blok dat moet worden doorgevoerd als onderdeel van een blok-blob. | Als wordt gebruikt om een blok door te voeren op een blob die actief is, is er geen wijziging. Als wordt gebruikt om een blok door te voeren op een blob die zacht is verwijderd, wordt een nieuwe BLOB gemaakt en wordt automatisch een moment opname gegenereerd om de status van de zachte verwijderde BLOB vast te leggen. |
| [Blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list) | Blok-blobs | Hiermee wordt een BLOB doorgevoerd door de set blok-Id's op te geven waaruit de blok-BLOB bestaat. | Als deze wordt gebruikt om een bestaande BLOB te vervangen, wordt automatisch een moment opname van de status van de BLOB vóór de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde BLOB als en alleen als dit een blok-blob is. Als deze wordt vervangen door een BLOB van een ander type, worden alle bestaande voorlopig verwijderde gegevens permanent verlopen. |
| [Pagina plaatsen](/rest/api/storageservices/put-page) | Pagina-blobs | Schrijft een bereik van pagina's naar een pagina-blob. | Geen wijziging. Pagina-BLOB-gegevens die worden overschreven of gewist met deze bewerking, worden niet opgeslagen en kunnen niet worden hersteld. |
| [Blok toevoegen](/rest/api/storageservices/append-block) | Toevoeg-blobs | Schrijft een gegevens blok naar het einde van een toevoeg-BLOB | Geen wijziging. |
| [BLOB-eigenschappen instellen](/rest/api/storageservices/set-blob-properties) | Blok-, toevoeg-en pagina-blobs | Hiermee stelt u waarden in voor systeem eigenschappen die zijn gedefinieerd voor een blob. | Geen wijziging. Overschreven BLOB-eigenschappen kunnen niet worden hersteld. |
| [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata) | Blok-, toevoeg-en pagina-blobs | Hiermee stelt u door de gebruiker gedefinieerde meta gegevens voor de opgegeven Blob in als een of meer naam/waarde-paren. | Geen wijziging. Overschreven BLOB-meta gegevens kunnen niet worden hersteld. |

Het is belang rijk om te zien dat het aanroepen van "pagina plaatsen" voor het overschrijven of wissen van de bereiken van een pagina-BLOB niet automatisch moment opnamen genereert. Virtuele-machine schijven worden ondersteund door pagina-blobs en de **put-pagina** gebruiken om gegevens te schrijven.

### <a name="recovery"></a>Herstel

Bij het aanroepen van de bewerking voor het [ongedaan](/rest/api/storageservices/undelete-blob) maken van de BLOB voor een voorlopig verwijderde basis-BLOB worden deze hersteld en alle gekoppelde tijdelijke verwijderde moment opnamen als actief. Als de `Undelete Blob` bewerking wordt aangeroepen op een actieve basis-blob, worden alle gekoppelde tijdelijke verwijderde moment opnamen als actief hersteld. Wanneer moment opnamen als actief worden teruggezet, zien ze eruit als door de gebruiker gegenereerde moment opnamen. de basis-BLOB wordt niet overschreven.

Als u een BLOB wilt herstellen naar een specifieke voorlopig verwijderde moment opname, `Undelete Blob` kunt u aanroepen op de basis-blob. Vervolgens kunt u de moment opname kopiëren over de nu actieve blob. U kunt de moment opname ook kopiëren naar een nieuwe blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Zacht verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden onder oudere gegevens weer gegeven. Hier wordt de **verwijdering van BLOB ongedaan** gemaakt op BLOB B, waardoor de basis-blob, B1 en alle bijbehorende moment opnamen, hier net als actief, wordt hersteld. In de tweede stap wordt B0 gekopieerd over de basis-blob. Met deze Kopieer bewerking wordt een voorlopig verwijderde moment opname van B1 gegenereerd.*

Als u de voorlopig verwijderde blobs en BLOB-moment opnamen wilt weer geven, kunt u ervoor kiezen om verwijderde gegevens in **lijst-blobs**op te nemen. U kunt ervoor kiezen om alleen voorlopig verwijderde basis-blobs weer te geven, of om ook te voorzien in voorlopig verwijderde BLOB-moment opnamen. Voor alle voorlopig verwijderde gegevens kunt u de tijd bekijken waarop de gegevens zijn verwijderd, evenals het aantal dagen voordat de gegevens permanent verlopen.

### <a name="example"></a>Voorbeeld

Hieronder vindt u de console-uitvoer van een .NET-script dat uploadt, overschrijft, moment opnamen, verwijderen en terugzetten van een blob met de naam *HelloWorld* wanneer zacht verwijderen is ingeschakeld:

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

Zie de sectie [volgende stappen](#next-steps) voor een verwijzing naar de toepassing die deze uitvoer heeft geproduceerd.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Alle voorlopig verwijderde gegevens worden gefactureerd tegen hetzelfde aantal als actieve gegevens. Er worden geen kosten in rekening gebracht voor gegevens die permanent worden verwijderd na de geconfigureerde Bewaar periode. Zie meer [informatie over hoe moment opnamen worden samengevoegd](storage-blob-snapshots.md)voor een diep gaande kennis van moment opnamen en hoe ze kosten samen voegen.

U wordt niet gefactureerd voor de trans acties die betrekking hebben op het automatisch genereren van moment opnamen. Er worden kosten in rekening gebracht voor het **verwijderen van BLOB** -trans acties tegen de snelheid voor schrijf bewerkingen.

Bekijk de [pagina met prijzen voor azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor Azure Blob Storage in het algemeen.

Wanneer u voorlopig verwijderen voor het eerst inschakelt, kunt u het beste een kleine Bewaar periode gebruiken om beter te begrijpen hoe de functie van invloed is op uw factuur.

## <a name="get-started"></a>Aan de slag

De volgende stappen laten zien hoe u aan de slag gaat met zacht verwijderen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Schakel de optie voor het voorlopig verwijderen van blobs in uw opslag account in met behulp van Azure Portal:

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/). 

2. Navigeer naar de optie **gegevens bescherming** onder **BLOB-service**.

3. Klik op **ingeschakeld** onder **voorlopig verwijderen van BLOB**

4. Voer het aantal dagen in dat u wilt *bewaren voor* onder **Bewaar beleid**

5. Klik op de knop **Opslaan** om uw instellingen voor gegevens beveiliging te bevestigen

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Als u de voorlopig verwijderde blobs wilt weer geven, schakelt u het selectie vakje **Verwijderde blobs weer geven** in.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Als u tijdelijke verwijderde moment opnamen voor een bepaalde BLOB wilt weer geven, selecteert u de BLOB en klikt u vervolgens op **moment opnamen weer geven**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Zorg ervoor dat het selectie vakje **Verwijderde moment opnamen weer geven** is geselecteerd.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Wanneer u op een zachte verwijderde BLOB of moment opname klikt, ziet u de nieuwe BLOB-eigenschappen. Ze geven aan wanneer het object is verwijderd en hoeveel dagen resteren totdat de moment opname van de BLOB of BLOB permanent is verlopen. Als het voorlopig verwijderde object geen moment opname is, hebt u ook de mogelijkheid om de verwijdering ervan ongedaan te maken.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Houd er rekening mee dat het verwijderen van een BLOB ook de verwijdering van alle gekoppelde moment opnamen ongedaan maakt. Als u het verwijderen van tijdelijke verwijderde moment opnamen voor een actieve BLOB ongedaan wilt maken, klikt u op de BLOB en selecteert u **verwijderen van alle moment opnamen ongedaan**maken.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Wanneer u de moment opnamen van een BLOB hebt verwijderd, kunt u op **niveau verhogen** klikken om een moment opname te kopiëren over de hoofd-blob, waardoor de BLOB wordt teruggezet naar de moment opname.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij. In het volgende voor beeld wordt zacht verwijderen ingeschakeld voor een subset van accounts in een abonnement:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
U kunt controleren of de functie voor voorlopig verwijderen is ingeschakeld met behulp van de volgende opdracht:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Als u blobs wilt herstellen die per ongeluk zijn verwijderd, roept u verwijderen ongedaan maken op deze blobs. Houd er rekening mee dat bij het aanroepen van **undelete BLOB**, zowel op actieve als Soft verwijderde blobs, alle gekoppelde tijdelijke verwijderde moment opnamen worden hersteld als actief. In het volgende voor beeld wordt de verwijdering van alle voorlopig verwijderde en actieve blobs in een container ongedaan gemaakt:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Gebruik de volgende opdracht om het huidige Bewaar beleid voor voorlopig verwijderen te vinden:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Als u wilt controleren of zacht verwijderen is ingeschakeld, gebruikt u de volgende opdracht: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij:

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

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Als u blobs wilt herstellen die per ongeluk zijn verwijderd, roept u verwijderen ongedaan maken op deze blobs. Houd er rekening mee dat bij het aanroepen van **undelete BLOB**, zowel op actieve als Soft verwijderde blobs, alle gekoppelde tijdelijke verwijderde moment opnamen worden hersteld als actief. In het volgende voor beeld wordt de verwijdering van alle voorlopig verwijderde en actieve blobs in een container ongedaan gemaakt:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Als u wilt herstellen naar een specifieke BLOB-versie, roept u eerst undelete aan voor een BLOB en kopieert u vervolgens de gewenste moment opname over de blob. In het volgende voor beeld wordt een blok-BLOB hersteld naar de meest recent gegenereerde moment opname:

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

## <a name="special-considerations"></a>Speciale overwegingen

Als er een kans is dat uw gegevens per ongeluk worden gewijzigd of verwijderd door een toepassing of een andere gebruiker van het opslag account, wordt het inschakelen van de functie voor voorlopig verwijderen aanbevolen. Het inschakelen van zacht verwijderen voor vaak overschreven gegevens kan leiden tot hogere kosten voor opslag capaciteit en een grotere latentie bij het weer geven van blobs. U kunt deze extra kosten en latentie verminderen door de vaak overschreven gegevens op te slaan in een afzonderlijk opslag account, waar zacht verwijderen is uitgeschakeld. 

## <a name="faq"></a>Veelgestelde vragen

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Voor welke opslag Services kan ik zacht verwijderen gebruiken?

Op dit moment is voorlopig verwijderen alleen beschikbaar voor Blob-opslag (object).

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Is zacht verwijderen beschikbaar voor alle typen opslag accounts?

Ja, voorlopig verwijderen is beschikbaar voor Blob Storage-accounts en voor blobs in algemeen gebruik (zowel GPv1 als GPv2)-opslag accounts. Standaard-en Premium-account typen worden ondersteund. Zacht verwijderen is beschikbaar voor niet-beheerde schijven, wat pagina-blobs zijn onder de kaften. Zacht verwijderen is niet beschikbaar voor beheerde schijven.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Is zacht verwijderen beschikbaar voor alle opslag lagen?

Ja, zacht verwijderen is beschikbaar voor alle opslag lagen, waaronder hot, cool en Archive. Zacht verwijderen biedt echter geen beveiliging tegen overschrijven voor blobs in de opslaglaag.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Kan ik de API set BLOB-laag gebruiken om blobs laag te maken met tijdelijke verwijderde moment opnamen?

Ja. De voorlopig verwijderde moment opnamen blijven in de oorspronkelijke laag, maar de basis-BLOB wordt verplaatst naar de nieuwe laag. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium Storage-accounts hebben een limiet van 100 voor de BLOB-moment opname. Zijn er voorlopig verwijderde moment opnamen in de buurt van deze limiet?

Nee, tijdelijke verwijderde moment opnamen tellen niet mee aan deze limiet.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Kan ik zacht verwijderen inschakelen voor bestaande opslag accounts?

Ja, zacht verwijderen kan worden geconfigureerd voor zowel bestaande als nieuwe opslag accounts.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Als ik een hele account of container Verwijder waarvoor Soft zacht verwijderen is ingeschakeld, worden alle bijbehorende blobs opgeslagen?

Nee, als u een hele account of container verwijdert, worden alle gekoppelde blobs definitief verwijderd. Zie [resources vergren delen om onverwachte wijzigingen te voor komen](../../azure-resource-manager/management/lock-resources.md)voor meer informatie over het beveiligen van een opslag account tegen onbedoeld verwijderen.

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Kan ik de metrische gegevens voor de capaciteit weer geven voor verwijderde data?

Zacht verwijderde gegevens worden opgenomen als onderdeel van de totale capaciteit van de opslag account. Zie [Opslaganalyse](../common/storage-analytics.md)voor meer informatie over het volgen en bewaken van opslag capaciteit.

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Als ik zacht verwijderen uitschakel, kan ik dan nog steeds toegang krijgen tot dynamische, verwijderde gegevens?

Ja, u kunt nog steeds toegang krijgen tot onverlopen dynamische, verwijderde gegevens wanneer de functie voor dynamisch verwijderen is uitgeschakeld.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Kan ik voorlopig verwijderde moment opnamen van mijn BLOB lezen en kopiëren?  

Ja, maar u moet eerst undelete aanroepen op de blob.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Is zacht verwijderen beschikbaar voor alle typen blobs?

Ja, voorlopig verwijderen is beschikbaar voor blok-blobs, toevoeg-blobs en pagina-blobs.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Is er een tijdelijke verwijdering beschikbaar voor virtuele machine-schijven?  

Zacht verwijderen is beschikbaar voor zowel Premium als standaard niet-beheerde schijven. Dit zijn pagina-blobs onder de kaften. Met voorlopig verwijderen kunt u alleen gegevens herstellen die zijn verwijderd door **BLOB verwijderen**, **BLOB plaatsen**, **blokkerings lijst**plaatsen, **blok keren** en BLOB-bewerkingen **kopiëren** . Gegevens die worden overschreven door een aanroep naar de **put-pagina** , kunnen niet worden hersteld.

Een virtuele machine van Azure schrijft naar een niet-beheerde schijf met behulp van aanroepen om de **pagina te plaatsen**. met de opdracht voorlopig verwijderen kunt u schrijf bewerkingen naar een niet-beheerde schijf van een Azure-VM ongedaan maken.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Moet ik mijn bestaande toepassingen wijzigen voor het gebruik van zacht verwijderen?

Het is mogelijk om te profiteren van de functie voor voorlopig verwijderen, ongeacht de API-versie die u gebruikt. Om voorlopig verwijderde blobs en BLOB-moment opnamen weer te geven en te herstellen, moet u versie 2017-07-29 van de [Storage services-rest API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) of hoger gebruiken. Micro soft adviseert altijd de nieuwste versie van de Azure Storage-API te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [.NET-voorbeeld code](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
* [Replicatie Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Geo-redundantie gebruiken om Maxi maal beschik bare toepassingen te ontwerpen](../common/geo-redundant-design.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Herstel na nood geval en failover van het opslag account](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
