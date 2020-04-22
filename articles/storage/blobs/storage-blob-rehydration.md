---
title: Blobgegevens uit de archieflaag opnieuw hydrateren
description: Rehydrateer uw blobs uit archiefopslag, zodat u toegang hebt tot de gegevens.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684058"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Blobgegevens uit de archieflaag opnieuw hydrateren

Terwijl een blob zich in de archieftoegangslaag bevindt, wordt deze als offline beschouwd en kan deze niet worden gelezen of gewijzigd. De blob-metagegevens blijven online en beschikbaar, zodat u de blob en de eigenschappen ervan weergeven. Het lezen en wijzigen van blobgegevens is alleen beschikbaar met online lagen zoals hot of cool. Er zijn twee opties om gegevens op te halen en te openen die zijn opgeslagen in de categorie archieftoegang.

1. [Een gearchiveerde blob opnieuw hydrateren naar een onlinelaag](#rehydrate-an-archived-blob-to-an-online-tier) : een archiefblob opnieuw hydrateren om te warmof koel door de laag te wijzigen met de bewerking [Blob Tier instellen.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)
2. [Een gearchiveerde blob kopiëren naar een onlinelaag](#copy-an-archived-blob-to-an-online-tier) - Een nieuwe kopie van een archiefblob maken met de bewerking [Blob kopiëren.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Geef een andere blobnaam en een doellaag van hot of cool op.

 Zie [Azure Blob-opslag: hot, cool en archieftoegangslagen](storage-blob-storage-tiers.md)voor meer informatie over lagen.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Een gearchiveerde blob opnieuw hydrateren naar een onlinelaag

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Een gearchiveerde blob naar een online laag kopiëren

Als u uw archiefblob niet wilt rehydrateren, u ervoor kiezen om een [Copy Blob-bewerking](https://docs.microsoft.com/rest/api/storageservices/copy-blob) uit te voeren. Uw oorspronkelijke blob blijft ongewijzigd in het archief, terwijl een nieuwe blob wordt gemaakt in de online hot of cool tier waar u aan werken. In de bewerking Blob kopiëren u de optionele eigenschap *x-ms-rehydraatprioriteit* ook instellen op Standaard of Hoog om de prioriteit op te geven waarop u uw blobkopie wilt maken.

Het kopiëren van een blob uit het archief kan uren in beslag nemen, afhankelijk van de geselecteerde prioriteit voor rehydrateren. Achter de schermen leest de bewerking **Blob kopiëren** uw blob met archiefbron om een nieuwe online blob te maken in de geselecteerde doellaag. De nieuwe blob kan zichtbaar zijn wanneer u blobs aanvoert, maar de gegevens zijn pas beschikbaar nadat de blob is gelezen uit de blob van het bronarchief en de gegevens naar de nieuwe online bestemmingsblob zijn geschreven. De nieuwe blob is als een onafhankelijke kopie en elke wijziging of verwijdering ervan heeft geen invloed op de blob van het bronarchief.

Archiefblobs kunnen alleen worden gekopieerd naar online doellagen binnen hetzelfde opslagaccount. Het kopiëren van een archiefblob naar een andere archiefblob wordt niet ondersteund. In de volgende tabel worden de mogelijkheden van CopyBlob aangegeven.

|                                           | **Bron van hot tier**   | **Koele laagbron** | **Bron van archieflagen**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Bestemming hot tier**                  | Ondersteund             | Ondersteund            | Ondersteund binnen hetzelfde account; in afwachting van rehydrateren               |
| **Koele laagbestemming**                 | Ondersteund             | Ondersteund            | Ondersteund binnen hetzelfde account; in afwachting van rehydrateren               |
| **Bestemming archieflaag**              | Ondersteund             | Ondersteund            | Niet ondersteund         |

## <a name="pricing-and-billing"></a>Prijzen en facturering

Het hydrateren van blobs uit het archief in hete of koele lagen worden in rekening gebracht als leesbewerkingen en het ophalen van gegevens. Het gebruik van hoge prioriteit heeft hogere kosten voor het ophalen van gegevens in vergelijking met de standaardprioriteit. Rehydratie met hoge prioriteit wordt weergegeven als een afzonderlijk regelitem op uw factuur. Als een aanvraag met hoge prioriteit om een archiefblob van een paar gigabytes terug te sturen meer dan 5 uur in zich opneemt, wordt het percentage met hoge prioriteit niet in rekening gebracht. Echter, standaard retrieval tarieven nog steeds van toepassing als de rehydratie werd geprioriteerd boven andere verzoeken.

Het kopiëren van blobs uit archief naar hete of koele lagen worden in rekening gebracht als leesbewerkingen en het ophalen van gegevens. Er wordt een schrijfbewerking in rekening gebracht voor het maken van de nieuwe blobkopie. Vroege verwijderingskosten zijn niet van toepassing wanneer u naar een online blob kopieert, omdat de bronblob ongewijzigd blijft in de archieflaag. Als deze optie is geselecteerd, zijn er wel kosten voor het ophalen met hoge prioriteit.

Blobs in de archieflaag moeten minimaal 180 dagen worden opgeslagen. Als u gearchiveerde blobs vóór 180 dagen verwijderde of opnieuw uitzet, worden er kosten in de vroege verwijdering verbonden.

> [!NOTE]
> Zie [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over prijzen voor blokblobs en gegevensrehydratie. Zie [Prijsgegevens voor gegevensoverdrachten](https://azure.microsoft.com/pricing/details/data-transfers/)voor meer informatie over uitgaande kosten voor gegevensoverdracht.

## <a name="quickstart-scenarios"></a>Snelstartscenario's

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Een archiefblob opnieuw hydrateren naar een onlinelaag
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek en selecteer **alle bronnen**in de Azure-portal.

1. Selecteer uw opslagaccount.

1. Selecteer de container en selecteer vervolgens de blob.

1. Selecteer laag **Wijzigen**in de **eigenschappen van de Blob**.

1. Selecteer de toegangslaag **Hot** or **Cool.** 

1. Selecteer een prioriteit voor rehydrateren van **Standaard** of **Hoog**.

1. Selecteer **Opslaan** onderin.

![Opslagaccountlaag](media/storage-tiers/blob-access-tier.png)
![wijzigen De status Rehydraat controleren](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Het volgende PowerShell-script kan worden gebruikt om de bloblaag van een archiefblob te wijzigen. De `$rgName` variabele moet worden geïnitialiseerd met de naam van uw resourcegroep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslagaccount. De `$containerName` variabele moet worden geïnitialiseerd met uw containernaam. De `$blobName` variabele moet worden geïnitialiseerd met de naam van uw blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Een archiefblob kopiëren naar een nieuwe blob met een onlinelaag
Het volgende PowerShell-script kan worden gebruikt om een archiefblob te kopiëren naar een nieuwe blob binnen hetzelfde opslagaccount. De `$rgName` variabele moet worden geïnitialiseerd met de naam van uw resourcegroep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslagaccount. De `$srcContainerName` `$destContainerName` variabelen en variabelen moeten worden geïnitialiseerd met uw containernamen. De `$srcBlobName` `$destBlobName` variabelen en variabelen moeten worden geïnitialiseerd met uw blobnamen. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Blob-opslaglagen](storage-blob-storage-tiers.md)
* [Controleer de prijzen voor hot, cool en archiveren in Blob-opslag- en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
* [De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)
* [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)
