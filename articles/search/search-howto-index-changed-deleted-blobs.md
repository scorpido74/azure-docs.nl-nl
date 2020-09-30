---
title: Gewijzigde en verwijderde blobs
titleSuffix: Azure Cognitive Search
description: Na het maken van een eerste zoek index die uit Azure Blob-opslag wordt geïmporteerd, kunt u met de volgende Indexeer bewerking alleen de blobs ophalen die zijn gewijzigd of verwijderd. In dit artikel worden de details uitgelegd.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2e73039418233c97fc20242ed7af7df14c5b47ee
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534774"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>De detectie van wijzigingen en verwijderingen voor blobs in azure Cognitive Search indexeren instellen

Nadat een eerste zoek index is gemaakt, wilt u mogelijk volgende Indexeer functies configureren om alleen die documenten op te halen die zijn gemaakt of verwijderd sinds de eerste uitvoering. Voor zoek inhoud die afkomstig is uit Azure Blob-opslag, wordt de detectie van wijzigingen automatisch uitgevoerd wanneer u een schema gebruikt voor het activeren van indexeren. De service indexeert standaard alleen de gewijzigde blobs, zoals wordt bepaald door de tijds tempel van de BLOB `LastModified` . In tegens telling tot andere gegevens bronnen die worden ondersteund door zoek indexen, hebben blobs altijd een tijds tempel, waardoor het niet meer nodig is om een wijzigings detectie beleid hand matig in te stellen.

Hoewel de wijzigings detectie een gegeven is, is de detectie van de verwijdering niet. Als u verwijderde documenten wilt detecteren, moet u ervoor zorgen dat u de methode ' zacht verwijderen ' gebruikt. Als u de blobs naar rechts verwijdert, worden de bijbehorende documenten niet verwijderd uit de zoek index.

Er zijn twee manieren om de methode voor zacht verwijderen te implementeren. Beide worden hieronder beschreven.

## <a name="native-blob-soft-delete-preview"></a>Voorlopig verwijderen van systeemeigen blob (preview)

> [!IMPORTANT]
> Ondersteuning voor het voorlopig verwijderen van een blob is in preview. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2020-06-30-preview](./search-api-preview.md) biedt deze functie. Er is momenteel geen portal-of .NET SDK-ondersteuning.

> [!NOTE]
> Wanneer u het beleid voor voorlopig verwijderen van de native-BLOB gebruikt, moet u de document sleutels voor de documenten in de index een BLOB-eigenschap of BLOB-meta gegevens zijn.

In deze methode gebruikt u de [systeem eigen BLOB](../storage/blobs/soft-delete-blob-overview.md) -functie voor voorlopig verwijderen die wordt aangeboden door Azure Blob-opslag. Als systeem eigen BLOB verwijderen is ingeschakeld in uw opslag account, heeft uw gegevens bron een systeem eigen beleid voor zacht verwijderen en de Indexeer functie vindt een blob die is overgegaan naar een voorlopig verwijderde status. de Indexeer functie verwijdert dat document uit de index. Het beleid voor voorlopig verwijderen van de systeem eigen BLOB wordt niet ondersteund bij het indexeren van blobs uit Azure Data Lake Storage Gen2.

Voer de volgende stappen uit:

1. [Systeem eigen zacht verwijderen inschakelen voor Azure Blob-opslag](../storage/blobs/soft-delete-blob-overview.md). We raden u aan het Bewaar beleid in te stellen op een waarde die veel hoger is dan het schema voor de indexerings interval. Als er een probleem is met het uitvoeren van de Indexeer functie of als u een groot aantal documenten hebt om te indexeren, is er genoeg tijd voor de Indexeer functie om de zachte verwijderde blobs uiteindelijk te verwerken. Met Azure Cognitive Search Indexeer functies wordt alleen een document uit de index verwijderd als het de BLOB verwerkt terwijl deze de status zacht verwijderd heeft.

1. Configureer een systeem eigen detectie beleid voor het voorlopig verwijderen van blobs op de gegevens bron. Hieronder kunt u een voorbeeld bekijken. Omdat deze functie in preview is, moet u de preview-REST API gebruiken.

1. Voer de Indexeer functie uit of stel de Indexeer functie in op een schema. Wanneer de Indexeer functie wordt uitgevoerd en de BLOB wordt verwerkt, wordt het document uit de index verwijderd.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>Verwijderde blobs opnieuw indexeren (met systeem eigen beleid voor voorlopig verwijderen)

Als u een BLOB verwijdert uit Azure Blob-opslag waarvoor systeem eigen laad bare verwijdering is ingeschakeld in uw opslag account, wordt de BLOB overgezet naar een voorlopig verwijderde status, zodat u de optie hebt de verwijdering van die Blob in de Bewaar periode ongedaan te maken. Als u een verwijdering omkeert nadat de Indexeer functie de bewerking heeft verwerkt, indexeert de Indexeer functie niet altijd de herstelde blob. Dit komt doordat de Indexeer functie bepaalt welke blobs op basis van de tijds tempel van de BLOB moet worden geïndexeerd `LastModified` . Wanneer een zachte verwijderde BLOB ongedaan wordt verwijderd, wordt de `LastModified` tijds tempel ervan niet bijgewerkt, dus als de Indexeer functie al blobs met meer recente `LastModified` tijds tempels heeft verwerkt, wordt de niet-verwijderde BLOB niet opnieuw geïndexeerd. 

Om ervoor te zorgen dat een niet-verwijderde BLOB opnieuw wordt geïndexeerd, moet u de tijds tempel van de BLOB bijwerken `LastModified` . Een manier om dit te doen, is door de meta gegevens van die BLOB opnieuw op te slaan. U hoeft de meta gegevens niet te wijzigen. Als u de meta gegevens opnieuw opslaat, wordt de tijds tempel van de BLOB bijgewerkt `LastModified` zodat de Indexeer functie weet dat deze de BLOB opnieuw moet indexeren.

## <a name="soft-delete-using-custom-metadata"></a>Zacht verwijderen met aangepaste meta gegevens

In deze methode gebruikt u de meta gegevens van een blob om aan te geven wanneer een document uit de zoek index moet worden verwijderd. Deze methode vereist twee afzonderlijke acties, waarbij u het zoek document uit de index verwijdert, gevolgd door het verwijderen van blobs in Azure Storage.

Voer de volgende stappen uit:

1. Voeg een aangepaste sleutel-waardepaar voor meta gegevens toe aan de blob om aan te geven dat deze logisch is verwijderd door Azure Cognitive Search.

1. Configureer een beleid voor het detecteren van een tijdelijke verwijderings kolom voor de gegevens bron. Hieronder kunt u een voorbeeld bekijken.

1. Zodra de Indexeer functie de BLOB heeft verwerkt en het document uit de index heeft verwijderd, kunt u de BLOB verwijderen in Azure Blob-opslag.

Het volgende beleid beschouwt bijvoorbeeld een blob die moet worden verwijderd als deze een meta gegevens eigenschap `IsDeleted` met de waarde heeft `true` :

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>Verwijderde blobs opnieuw indexeren (met behulp van aangepaste meta gegevens)

Wanneer een Indexeer functie een verwijderde BLOB verwerkt en het bijbehorende Zoek document uit de index verwijdert, wordt die BLOB niet opnieuw bezocht als u deze later herstelt als de tijds tempel van de BLOB `LastModified` ouder is dan de laatste uitgevoerde indexer.

Als u het document opnieuw wilt indexeren, wijzigt u de `"softDeleteMarkerValue" : "false"` voor die Blob en voert u de Indexeer functie opnieuw uit.

## <a name="help-us-make-azure-cognitive-search-better"></a>Help ons Azure Cognitive Search beter te maken

Als u een functie verzoek of ideeën voor verbeteringen hebt, geeft u uw invoer op [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)op. Als u hulp nodig hebt bij het gebruik van de bestaande functie, plaatst u uw vraag op [stack overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Volgende stappen

* [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
* [Een BLOB-Indexeer functie configureren](search-howto-indexing-azure-blob-storage.md)
* [Overzicht van BLOB-indexering](search-blob-storage-integration.md)