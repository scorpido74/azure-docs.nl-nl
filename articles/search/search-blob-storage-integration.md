---
title: Zoek opdracht in volledige tekst toevoegen aan Azure Blob Storage
titleSuffix: Azure Search
description: Inhoud extra heren en structuur toevoegen aan Azure-blobs bij het bouwen van een index voor zoeken in volledige tekst in Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 7e5eb73cc6abc72689bbc674b29f4d288dd66b6f
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302904"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Zoek opdracht in volledige tekst toevoegen aan Azure Blob-gegevens met behulp van Azure Search

Het kan lastig zijn om te zoeken in de verschillende typen inhoud die zijn opgeslagen in Azure Blob Storage. U kunt echter met behulp van Azure Search de inhoud van uw blobs indexeren en doorzoeken met slechts enkele klikken. Voor het zoeken in Blob Storage moet een Azure Search-service worden ingericht. De verschillende service limieten en prijs categorieën van Azure Search zijn te vinden op de [pagina met prijzen](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Wat is Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) is een zoek service waarmee ontwikkel aars eenvoudig krachtige zoek functies voor volledige tekst kunnen toevoegen aan webtoepassingen en mobiele toepassingen. Als service biedt Azure Search de nood zaak om een zoek infrastructuur te beheren, met een [Sla voor 99,9% uptime](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Modellen voor ondernemings documenten indexeren en zoeken
Met ondersteuning voor [document extractie](https://aka.ms/azsblobindexer) in Azure Blob-opslag kunt u de volgende inhoud indexeren:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Door tekst en meta gegevens uit deze bestands typen extra heren, kunt u met één query zoeken in meerdere bestands indelingen. 

## <a name="search-through-your-blob-metadata"></a>Zoeken in uw BLOB-meta gegevens
Een veelvoorkomend scenario waarmee u gemakkelijk kunt sorteren op blobs van elk inhouds type is het indexeren van zowel aangepaste meta gegevens als systeem eigenschappen voor elke blob. Op deze manier wordt informatie voor alle blobs geïndexeerd, ongeacht het document type. U kunt vervolgens door gaan met het sorteren, filteren en facet voor alle inhoud van de Blob-opslag.

[Meer informatie over het indexeren van BLOB-meta gegevens.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Afbeeldingen zoeken
Azure Search de zoek functie voor volledige tekst, facet navigatie en sorteer mogelijkheden kunnen nu worden toegepast op de meta gegevens van afbeeldingen die zijn opgeslagen in blobs.

Cognitieve Zoek opdrachten bevatten vaardig heden voor afbeeldings verwerking, zoals [optische teken herkenning (OCR)](cognitive-search-skill-ocr.md) en het identificeren van [visuele functies](cognitive-search-skill-image-analysis.md) waarmee de visuele inhoud in elke afbeelding kan worden geïndexeerd.

## <a name="index-and-search-through-json-blobs"></a>Index en zoek opdracht via JSON-blobs
Azure Search kunnen worden geconfigureerd voor het extra heren van gestructureerde inhoud gevonden in blobs die JSON bevatten. Azure Search kunnen JSON-blobs lezen en de gestructureerde inhoud parseren in de juiste velden van een Azure Search document. Azure Search kunnen ook blobs maken die een matrix van JSON-objecten bevatten en elk element toewijzen aan een afzonderlijk Azure Search document.

JSON parseren kan momenteel niet worden geconfigureerd via de portal. [Meer informatie over JSON-parsering in Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Snelstart
Azure Search kunnen rechtstreeks worden toegevoegd aan blobs via de pagina voor de Blob Storage-Portal.

![](./media/search-blob-storage-integration/blob-blade.png)

Klik op **Azure Search toevoegen** om een stroom te starten waarin u een bestaande Azure Search service kunt selecteren of een nieuwe service wilt maken. Als u een nieuwe service maakt, gaat u naar de portal-ervaring van uw opslag account. U kunt teruggaan naar de pagina van de opslag Portal en de optie **Azure Search toevoegen** opnieuw selecteren, waar u de bestaande service kunt selecteren.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de indexering van de Azure Search BLOB vindt u in de volledige [documentatie](https://aka.ms/azsblobindexer).
