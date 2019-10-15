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
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312196"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Zoek opdracht in volledige tekst toevoegen aan Azure Blob-gegevens met behulp van Azure Search

Het kan lastig zijn om te zoeken in de verschillende typen inhoud die zijn opgeslagen in Azure Blob Storage. U kunt echter met behulp van [Azure Search](search-what-is-azure-search.md)de inhoud van uw blobs indexeren en doorzoeken met slechts enkele klikken. Azure Search heeft ingebouwde integratie voor het indexeren van de Blob-opslag via een [*BLOB-Indexeer functie*](search-howto-indexing-azure-blob-storage.md) waarmee gegevens bronnen kunnen worden toegevoegd aan indexering.

## <a name="supported-content-types"></a>Ondersteunde inhouds typen

Door een BLOB Indexeer functie uit te voeren via een container, kunt u tekst en meta gegevens uit de volgende inhouds typen extra heren met één query:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

U kunt eventueel [AI-verrijking](search-blob-ai-integration.md) koppelen in de vorm van een *vaardig heden* om nieuwe informatie en structuur te maken op basis van blobs, inclusief tekst representaties van afbeeldings bestanden. Door de toevoeging van AI-verrijking wordt de lijst met inhouds typen uitgebreid met JPEG en PNG. Er worden vaardig heden voor afbeeldings verwerking toegevoegd, zoals [optische teken herkenning (OCR)](cognitive-search-skill-ocr.md) en het identificeren van [visuele functies](cognitive-search-skill-image-analysis.md) waarmee de visuele inhoud in elke afbeelding kan worden geïndexeerd.

## <a name="search-through-your-blob-metadata"></a>Zoeken in uw BLOB-meta gegevens
Een veelvoorkomend scenario waarmee u gemakkelijk kunt sorteren op blobs van elk inhouds type is het indexeren van zowel aangepaste meta gegevens als systeem eigenschappen voor elke blob. Op deze manier wordt informatie voor alle blobs geïndexeerd, ongeacht het document type. U kunt vervolgens door gaan met het sorteren, filteren en facet voor alle inhoud van de Blob-opslag.

[Meer informatie over het indexeren van BLOB-meta gegevens.](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>Index en zoek opdracht via JSON-blobs
Azure Search kunnen worden geconfigureerd voor het extra heren van gestructureerde inhoud gevonden in blobs die JSON bevatten. Azure Search kunnen JSON-blobs lezen en de gestructureerde inhoud parseren in de juiste velden van een Azure Search document. Azure Search kunnen ook blobs maken die een matrix van JSON-objecten bevatten en elk element toewijzen aan een afzonderlijk Azure Search document.

JSON parseren kan momenteel niet worden geconfigureerd via de portal. [Meer informatie over JSON-parsering in Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Snelstart
Azure Search kunnen rechtstreeks worden toegevoegd aan blobs via de pagina voor de Blob Storage-Portal.

![](./media/search-blob-storage-integration/blob-blade.png)

Klik op **Azure Search toevoegen** om een stroom te starten waarin u een bestaande Azure Search service kunt selecteren of een nieuwe service wilt maken. Als u een nieuwe service maakt, gaat u naar de portal-ervaring van uw opslag account. U kunt teruggaan naar de pagina van de opslag Portal en de optie **Azure Search toevoegen** opnieuw selecteren, waar u de bestaande service kunt selecteren.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de indexering van de Azure Search BLOB vindt u in de volledige [documentatie](https://aka.ms/azsblobindexer).
