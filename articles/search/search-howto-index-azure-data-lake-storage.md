---
title: Zoeken in Azure Data Lake Storage Gen2 (preview-versie)
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren van inhoud en meta gegevens in Azure Data Lake Storage Gen2. Deze functie is momenteel beschikbaar als open bare preview
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905656"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Documenten indexeren in Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2-ondersteuning is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). U kunt toegang tot de voor beelden aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is momenteel geen portal-of .NET SDK-ondersteuning.


Wanneer u een Azure Storage-account instelt, hebt u de mogelijkheid om een [hiërarchische naam ruimte](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)in te scha kelen. Hierdoor kan het verzamelen van inhoud in een account worden georganiseerd in een hiërarchie van directory's en geneste submappen. Door hiërarchische naam ruimte in te scha kelen, schakelt u [Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)in.

In dit artikel wordt beschreven hoe u aan de slag gaat met het indexeren van documenten in Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Azure Data Lake Storage Gen2 Indexeer functie instellen

Er zijn een paar stappen die u moet volt ooien om inhoud te indexeren van Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Stap 1: Meld u aan voor de preview-versie

Meld u aan voor de Data Lake Storage Gen2 Preview voor indexering door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. U ontvangt een bevestigings-e-mail zodra u deze hebt geaccepteerd in de preview-versie.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Stap 2: de installatie stappen voor het indexeren van Azure Blob Storage volgen

Zodra u een bevestiging hebt ontvangen dat uw Preview-Registratie is geslaagd, bent u klaar om de Indexing-pijp lijn te maken.

U kunt inhoud en meta gegevens indexeren van Data Lake Storage Gen2 met behulp van de [rest API versie 2019-05-06-preview](search-api-preview.md). Er is op dit moment geen portal-of .NET SDK-ondersteuning.

Het indexeren van inhoud in Data Lake Storage Gen2 is identiek aan het indexeren van inhoud in Azure Blob-opslag. Als u wilt weten hoe u de Data Lake Storage Gen2 gegevens bron, index en Indexeer functie kunt instellen, raadpleegt u [documenten in azure Blob Storage indexeren met azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). Het artikel Blob Storage bevat ook informatie over welke document indelingen worden ondersteund, welke eigenschappen van BLOB-meta gegevens worden geëxtraheerd, incrementeel indexeren en nog veel meer. Deze informatie is hetzelfde voor Data Lake Storage Gen2.

## <a name="access-control"></a>Toegangsbeheer

Azure Data Lake Storage Gen2 implementeert een [model voor toegangs beheer](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) dat zowel op rollen gebaseerd toegangs beheer (RBAC) als POSIX-achtige acl's (toegangs beheer lijsten) ondersteunt. Wanneer u inhoud indexeert vanuit Data Lake Storage Gen2, haalt Azure Cognitive Search de RBAC-en ACL-gegevens niet uit de inhoud. Als gevolg hiervan worden deze gegevens niet opgenomen in uw Azure Cognitive Search-index.

Als het beheren van toegangs beheer voor elk document in de index belang rijk is, is het aan de ontwikkelaar van de toepassing om [beveiligings beperking](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)te implementeren.

## <a name="change-detection"></a>Wijzigings detectie

De Data Lake Storage Gen2 indexer ondersteunt de detectie van wijzigingen. Dit betekent dat wanneer de Indexeer functie alleen wordt uitgevoerd, de gewijzigde blobs opnieuw worden geïndexeerd zoals bepaald door de tijds `LastModified` tempel van de blob.

> [!NOTE] 
> Data Lake Storage Gen2 kunnen mappen worden hernoemd. Wanneer de naam van een map wordt gewijzigd, worden de tijds tempels voor de blobs in die map niet bijgewerkt. Als gevolg hiervan worden deze blobs niet opnieuw geïndexeerd door de Indexeer functie. Als u wilt dat de blobs in een map opnieuw worden geïndexeerd nadat de naam van een map is gewijzigd, moet u de `LastModified` tijds tempel voor alle blobs in de map bijwerken zodat de Indexeer functie deze opnieuw kan indexeren tijdens een toekomstige uitvoering.
