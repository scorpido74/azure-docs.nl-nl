---
title: Toegang tot meerdere protocollen op Azure Data Lake Storage | Microsoft Docs
description: Gebruik BLOB Api's en toepassingen die gebruikmaken van BLOB-Api's met Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f782c5abe611086baae5d2ad71bc38516039a231
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796059"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Toegang tot meerdere protocollen op Azure Data Lake Storage

BLOB-Api's werken nu met accounts die een hiërarchische naam ruimte hebben. Hiermee ontgrendelt u het ecosysteem van hulpprogram ma's, toepassingen en services, evenals diverse functies voor Blob-opslag voor accounts met een hiërarchische naam ruimte.

Tot die tijd moest u mogelijk afzonderlijke opslag oplossingen voor object opslag en Analytics-opslag hebben. Dat komt omdat Azure Data Lake Storage Gen2 beperkte ecosystem-ondersteuning had. Het heeft ook beperkte toegang tot Blob service functies, zoals diagnostische logboek registratie. Een gefragmenteerde opslag oplossing is moeilijk te onderhouden omdat u gegevens tussen accounts moet verplaatsen om verschillende scenario's uit te voeren. Dit hoeft u niet meer te doen.

Met toegang tot meerdere protocollen op Data Lake Storage, kunt u met uw gegevens werken door gebruik te maken van het ecosysteem van hulpprogram ma's, toepassingen en services. Dit geldt ook voor hulpprogram ma's en toepassingen van derden. U kunt ze naar accounts met een hiërarchische naam ruimte wijzen, zonder dat u ze hoeft te wijzigen. Deze *toepassingen werken ook als ze* BLOB-api's aanroepen, omdat BLOB-api's nu kunnen worden uitgevoerd op gegevens in accounts met een hiërarchische naam ruimte.

Blob Storage-functies, zoals [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en [beheer van levenscyclus opslag](storage-lifecycle-management-concepts.md) , werken nu met accounts die een hiërarchische naam ruimte hebben. Daarom kunt u hiërarchische naam ruimten inschakelen op uw Blob Storage-accounts zonder de toegang tot deze belang rijke functies te verliezen. 

> [!NOTE]
> Toegang via meerdere protocollen op Data Lake Storage is algemeen beschikbaar en is beschikbaar in alle regio's. Sommige Azure-Services of Blob Storage-functies die worden ingeschakeld door multi-protocol toegang, blijven in preview. Zie de tabellen in elke sectie van dit artikel voor meer informatie. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Hoe toegang tot meerdere protocollen op Data Lake Storage werkt

BLOB-Api's en Data Lake Storage Gen2-Api's kunnen worden gebruikt op dezelfde gegevens in opslag accounts met een hiërarchische naam ruimte. Data Lake Storage Gen2 worden BLOB-Api's via de hiërarchische naam ruimte gerouteerd, zodat u de voor delen van de eerste klassen Directory-bewerkingen en POSIX-compatibele Acl's (Access Control List) kunt krijgen. 

![Toegang voor meerdere protocollen op Data Lake Storage concept](./media/data-lake-storage-interop/interop-concept.png) 

Bestaande hulpprogram ma's en toepassingen die gebruikmaken van de BLOB API, krijgen deze voor delen automatisch. Ontwikkel aars hoeven ze niet te wijzigen. Data Lake Storage Gen2 consistent Toep assen op Acl's op Directory-en bestands niveau, ongeacht het protocol dat hulpprogram ma's en toepassingen gebruiken om toegang te krijgen tot de gegevens. 

## <a name="blob-storage-feature-support"></a>Ondersteuning voor Blob Storage-functie

Met toegang via meerdere protocollen op Data Lake Storage kunt u meer functies van Blob Storage gebruiken met uw Data Lake Storage. Deze tabel bevat de functies die zijn ingeschakeld door toegang tot meerdere protocollen op Data Lake Storage. 

De items die in deze tabel worden weer gegeven, worden na verloop van tijd gewijzigd, omdat de ondersteuning voor Blob Storage-functies blijft toenemen. 

> [!NOTE]
> Hoewel toegang via meerdere protocollen op Data Lake Storage algemeen beschikbaar is, blijft de ondersteuning voor sommige van deze functies in de preview-versie. 

|Blob-opslag functie | Ondersteunings niveau |
|---|---|
|[Cool-Access-laag](storage-blob-storage-tiers.md)|Algemeen beschikbaar|
|BLOB Sdk's |Algemeen beschikbaar|
|[PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Algemeen beschikbaar|
|[CLI](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Algemeen beschikbaar|
|[Diagnostische logboeken](../common/storage-analytics-logging.md)| Preview|
|[Levenscyclus beheer beleid](storage-lifecycle-management-concepts.md)| Preview|
|[Meldingen via Azure Event Grid](data-lake-storage-events.md)|Preview|
|[Archieftoegangslaag](storage-blob-storage-tiers.md)| Preview|
|[blobfuse](storage-how-to-mount-container-linux.md)|Nog niet ondersteund|
|[Onveranderbare opslag](storage-blob-immutable-storage.md)|Nog niet ondersteund|
|[Moment opnamen](storage-blob-snapshots.md)|Nog niet ondersteund|
|[Voorlopig verwijderen](storage-blob-soft-delete.md)|Nog niet ondersteund|
|[Statische websites](storage-blob-static-website.md)|Nog niet ondersteund|

Zie [bekende problemen](data-lake-storage-known-issues.md)voor meer informatie over algemene bekende problemen en beperkingen met Azure data Lake Storage Gen2.

## <a name="azure-ecosystem-support"></a>Ondersteuning voor Azure ecosysteem

Met toegang via meerdere protocollen op Data Lake Storage kunt u ook meer Azure-Services verbinden met uw Data Lake Storage. Deze tabel geeft een lijst van de services die zijn ingeschakeld door toegang tot meerdere protocollen op Data Lake Storage. 

Net als in de lijst met ondersteunde functies voor Blob-opslag, worden de items die in deze tabel worden weer gegeven na verloop van tijd gewijzigd naarmate de ondersteuning voor Azure-Services blijft toenemen. 

> [!NOTE]
> Hoewel toegang via meerdere protocollen op Data Lake Storage algemeen beschikbaar is, blijft de ondersteuning voor sommige van deze services in de preview-versie. 

|Azure-service | Ondersteunings niveau |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Algemeen beschikbaar|
|[Azure Event Hubs Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Algemeen beschikbaar|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Algemeen beschikbaar|
|[IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Algemeen beschikbaar|
|[Logic apps](https://azure.microsoft.com/services/logic-apps/)|Algemeen beschikbaar|
|[Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|Preview|

Zie [Azure data Lake Storage met Azure-Services integreren](data-lake-storage-integrate-with-azure-services.md)voor de volledige lijst met Azure ecosysteem-ondersteuning voor data Lake Storage Gen2.

Zie [bekende problemen](data-lake-storage-known-issues.md)voor meer informatie over algemene bekende problemen en beperkingen met Azure data Lake Storage Gen2.

## <a name="next-steps"></a>Volgende stappen

Zie [bekende problemen](data-lake-storage-known-issues.md)




