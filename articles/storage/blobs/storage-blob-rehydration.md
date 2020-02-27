---
title: BLOB-gegevens worden opnieuw gehydrateerd op basis van de opslaglaag
description: U kunt de blobs uit archief opslag opnieuw gebruiken zodat u toegang hebt tot de gegevens.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 0a7012d9daa808933a51ac05862a8a9aa4cfcf77
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614802"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>BLOB-gegevens worden opnieuw gehydrateerd op basis van de opslaglaag

Terwijl een BLOB zich in de Access-laag Archive bevindt, wordt deze als offline beschouwd en kan deze niet worden gelezen of gewijzigd. De meta gegevens van de BLOB blijven online en beschikbaar, zodat u de BLOB en de bijbehorende eigenschappen kunt weer geven. Het lezen en wijzigen van BLOB-gegevens is alleen beschikbaar voor online lagen, zoals warme of koud. Er zijn twee opties voor het ophalen en openen van gegevens die zijn opgeslagen in de toegangs laag voor het archief.

1. [Een gearchiveerde BLOB opnieuw gehydrateerd naar een online-laag](#rehydrate-an-archived-blob-to-an-online-tier) -een archief-BLOB opnieuw laten worden gehydrateerd op warme of koud door de laag te wijzigen met de bewerking voor het instellen van de [BLOB-laag](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Een gearchiveerde BLOB naar een online-laag kopiëren](#copy-an-archived-blob-to-an-online-tier) : Maak een nieuwe kopie van een archief-blob met behulp van de [Kopieer bewerking BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Geef een andere blobnaam en een bestemmings laag op met de naam Hot of cool.

 Zie [Azure Blob Storage: warme, cool en archief toegangs lagen](storage-blob-storage-tiers.md)voor meer informatie over lagen.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Een gearchiveerde BLOB opnieuw naar een online-laag gehydrateerd

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Een gearchiveerde BLOB kopiëren naar een online-laag

Als u de archief-BLOB niet opnieuw wilt laten worden gehydrateerd, kunt u ervoor kiezen om een [Kopieer-BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) bewerking uit te voeren. De oorspronkelijke BLOB blijft ongewijzigd in archief terwijl er een nieuwe BLOB wordt gemaakt in de online hot of cool-laag, zodat u kunt werken. In de bewerking BLOB kopiëren kunt u ook de optionele *x-MS-autohydrat-Priority-* eigenschap instellen op Standard of High (preview) om de prioriteit op te geven waarop u de BLOB-kopie wilt maken.

Archief-blobs kunnen alleen worden gekopieerd naar online doel lagen binnen hetzelfde opslag account. Het kopiëren van een archief-BLOB naar een andere archief-BLOB wordt niet ondersteund.

Het kopiëren van een BLOB uit het archief kan uren duren, afhankelijk van de geselecteerde opnieuw te maken prioriteit. Achter de schermen leest de bewerking **BLOB kopiëren** de bron-blob van het archief om een nieuwe online-Blob in de geselecteerde doellaag te maken. De nieuwe blob is mogelijk zichtbaar wanneer u blobs vermeldte, maar de gegevens zijn pas beschikbaar als de Lees bewerking van de blob van het bron archief is voltooid en de gegevens naar de nieuwe online-doel-BLOB zijn geschreven. De nieuwe blob is een onafhankelijke kopie en een wijziging of verwijdering hiervan heeft geen invloed op de bron archief-blob.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Reactiveren-blobs uit het archief in warme of coole lagen worden in rekening gebracht als Lees bewerkingen en gegevens ophalen. Het gebruik van high priority (preview) heeft hogere kosten voor bewerkingen en het ophalen van gegevens in vergelijking met de standaard prioriteit. Rehydratatie met hoge prioriteit worden weer gegeven als een afzonderlijk regel item op uw factuur. Als een aanvraag met een hoge prioriteit voor het retour neren van een archief-blob van een paar gigabytes meer dan vijf uur duurt, wordt niet het ophaal tarief met hoge prioriteit in rekening gebracht. Standaard tarieven voor het ophalen van zijn echter nog steeds van toepassing omdat de rehydratatie prioriteit heeft gegeven aan andere aanvragen.

Het kopiëren van blobs van Archive in warme of coole lagen wordt in rekening gebracht als Lees bewerkingen en ophalen van gegevens. Voor het maken van de nieuwe BLOB-kopie wordt een schrijf bewerking in rekening gebracht. Kosten voor vroegtijdig verwijderen zijn niet van toepassing wanneer u naar een online-BLOB kopieert, omdat de bron-BLOB ongewijzigd blijft in de laag van het archief. De kosten voor het ophalen van hoge prioriteit zijn van toepassing indien geselecteerd.

Blobs in de archief laag moeten mini maal 180 dagen worden opgeslagen. Als u gearchiveerde blobs verwijdert of reactiveren, worden de kosten 180 voor vroegtijdige verwijdering in rekening gebracht.

> [!NOTE]
> Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor blok-blobs en gegevens rehydratatie. Zie [prijs informatie voor gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/)voor meer informatie over de kosten voor uitgaande gegevens overdracht.

## <a name="quickstart-scenarios"></a>Snelstartscenario's

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Een archief-BLOB naar een online-laag opnieuw gehydrateerd
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek in het Azure Portal **alle resources**en selecteer deze.

1. Selecteer uw opslagaccount.

1. Selecteer uw container en selecteer vervolgens uw blob.

1. Selecteer in de **BLOB**-eigenschappen **laag wijzigen**.

1. Selecteer de laag **Hot** of **cool** Access. 

1. Selecteer een opnieuw gehydrateerde prioriteit van **standaard** of **hoog**.

1. Selecteer onder **Opslaan** onder.

![Laag van opslag account wijzigen](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Het volgende Power shell-script kan worden gebruikt om de BLOB-laag van een archief-BLOB te wijzigen. De variabele `$rgName` moet worden geïnitialiseerd met de naam van de resource groep. De variabele `$accountName` moet worden geïnitialiseerd met de naam van uw opslag account. De variabele `$containerName` moet worden geïnitialiseerd met de container naam. De variabele `$blobName` moet worden geïnitialiseerd met de naam van de blob. 
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

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Een archief-BLOB kopiëren naar een nieuwe blob met een online-laag
Het volgende Power shell-script kan worden gebruikt om een Archive-BLOB te kopiëren naar een nieuwe BLOB binnen hetzelfde opslag account. De variabele `$rgName` moet worden geïnitialiseerd met de naam van de resource groep. De variabele `$accountName` moet worden geïnitialiseerd met de naam van uw opslag account. De variabelen `$srcContainerName` en `$destContainerName` moeten worden geïnitialiseerd met de container namen. De variabelen `$srcBlobName` en `$destBlobName` moeten worden geïnitialiseerd met de namen van de blobs. 
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

* [Meer informatie over Blob Storage lagen](storage-blob-storage-tiers.md)
* [Prijzen voor de dynamische, statische en archieflaag controleren in Blob Storage- en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
* [De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)
* [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)
