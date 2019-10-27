---
title: Toegang tot meerdere protocollen op Azure Data Lake Storage (preview) | Microsoft Docs
description: Gebruik BLOB Api's en toepassingen die gebruikmaken van BLOB-Api's met Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 6e3b368d1f5615c34a49b155d288d3c37c28bc26
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933142"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Toegang tot meerdere protocollen op Azure Data Lake Storage (preview-versie)

BLOB-Api's werken nu met accounts die een hiërarchische naam ruimte hebben. Hiermee ontgrendelt u het hele ecosysteem van hulpprogram ma's, toepassingen en services, evenals alle functies van Blob-opslag voor accounts met een hiërarchische naam ruimte.

Tot die tijd moest u mogelijk afzonderlijke opslag oplossingen voor object opslag en Analytics-opslag hebben. Dat komt omdat Azure Data Lake Storage Gen2 beperkte ecosystem-ondersteuning had. Het heeft ook beperkte toegang tot Blob service functies, zoals diagnostische logboek registratie. Een gefragmenteerde opslag oplossing is moeilijk te onderhouden omdat u gegevens tussen accounts moet verplaatsen om verschillende scenario's uit te voeren. Dit hoeft u niet meer te doen.

> [!NOTE]
> Toegang voor meerdere protocollen op Data Lake Storage is in open bare preview en is beschikbaar in alle regio's. U hoeft niet in te schrijven voor de open bare preview omdat deze automatisch beschikbaar is voor alle accounts die een hiërarchische naam ruimte hebben. Zie het artikel [bekende problemen](data-lake-storage-known-issues.md) voor een overzicht van de beperkingen.

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Het hele ecosysteem van toepassingen, hulpprogram ma's en services gebruiken

Met toegang tot meerdere protocollen op Data Lake Storage, kunt u met al uw gegevens werken door gebruik te maken van het hele ecosysteem van hulpprogram ma's, toepassingen en services. Dit omvat Azure-Services, zoals [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [IOT hub](https://docs.microsoft.com/azure/iot-hub/), [Power bi](https://docs.microsoft.com/power-bi/desktop-data-sources)en vele andere. Zie [Azure data Lake Storage integreren met Azure-Services](data-lake-store-integrate-with-azure-services.md)voor een volledige lijst.

Dit geldt ook voor hulpprogram ma's en toepassingen van derden. U kunt ze naar accounts met een hiërarchische naam ruimte wijzen, zonder dat u ze hoeft te wijzigen. Deze *toepassingen werken ook als ze* BLOB-api's aanroepen, omdat BLOB-api's nu kunnen worden uitgevoerd op gegevens in accounts met een hiërarchische naam ruimte.

> [!NOTE]
> Zie het artikel [bekende problemen](data-lake-storage-known-issues.md) voor een overzicht van de beperkingen.

## <a name="use-all-blob-storage-features"></a>Alle functies van Blob-opslag gebruiken

Blob Storage-functies, zoals [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en [beheer van levenscyclus opslag](storage-lifecycle-management-concepts.md) , werken nu met accounts die een hiërarchische naam ruimte hebben. Daarom kunt u hiërarchische naam ruimten inschakelen op uw Blob Storage-accounts zonder de toegang tot deze belang rijke functies te verliezen. 

> [!NOTE]
> Zie het artikel [bekende problemen](data-lake-storage-known-issues.md) voor een overzicht van de beperkingen.

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Hoe toegang tot meerdere protocollen op Data Lake Storage werkt

BLOB-Api's en Data Lake Storage Gen2-Api's kunnen worden gebruikt op dezelfde gegevens in opslag accounts met een hiërarchische naam ruimte. Data Lake Storage Gen2 worden BLOB-Api's via de hiërarchische naam ruimte gerouteerd, zodat u de voor delen van de eerste klassen Directory-bewerkingen en POSIX-compatibele Acl's (Access Control List) kunt krijgen. 

![Toegang voor meerdere protocollen op Data Lake Storage concept](./media/data-lake-storage-interop/interop-concept.png) 

Bestaande hulpprogram ma's en toepassingen die gebruikmaken van de BLOB API, krijgen deze voor delen automatisch. Ontwikkel aars hoeven ze niet te wijzigen. Data Lake Storage Gen2 consistent Toep assen op Acl's op Directory-en bestands niveau, ongeacht het protocol dat hulpprogram ma's en toepassingen gebruiken om toegang te krijgen tot de gegevens. 

## <a name="next-steps"></a>Volgende stappen

Zie [bekende problemen](data-lake-storage-known-issues.md)




