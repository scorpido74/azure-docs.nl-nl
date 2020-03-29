---
title: Zoeken via Azure Data Lake Storage Gen2 (voorbeeld)
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren van inhoud en metagegevens in Azure Data Lake Storage Gen2. Deze functie is momenteel in openbare preview
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
> Azure Data Lake Storage Gen2-ondersteuning is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. U toegang tot de previews aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt deze functie. Er is momenteel geen portal of .NET SDK-ondersteuning.


Wanneer u een Azure-opslagaccount instelt, hebt u de optie om [hiërarchische naamruimte](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)in te schakelen. Hierdoor kan het verzamelen van inhoud in een account worden georganiseerd in een hiërarchie van mappen en geneste submappen. Door hiërarchische naamruimte in te schakelen, schakelt u [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)in.

In dit artikel wordt beschreven hoe u aan de slag met het indexeren van documenten die zich in Azure Data Lake Storage Gen2 bevinden.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Azure Data Lake Storage Gen2-indexer instellen

Er zijn een paar stappen die u moet voltooien om inhoud van Data Lake Storage Gen2 te indexeren.

### <a name="step-1-sign-up-for-the-preview"></a>Stap 1: Aanmelden voor de preview

Meld u aan voor de data lake storage Gen2 indexer preview door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. Je ontvangt een bevestigingsmail zodra je bent geaccepteerd in de preview.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Stap 2: De installatiestappen voor azure blob-opslagindexering volgen

Zodra u de bevestiging hebt ontvangen dat uw voorvertoning is geslaagd, u de indexeringspijplijn maken.

U inhoud en metagegevens van Data Lake Storage Gen2 indexeren met de [REST API-versie 2019-05-06-Preview.](search-api-preview.md) Er is op dit moment geen portal of .NET SDK-ondersteuning.

Het indexeren van inhoud in Data Lake Storage Gen2 is identiek aan het indexeren van inhoud in Azure Blob-opslag. Dus om te begrijpen hoe u de Gegevens lake Storage Gen2-gegevensbron, index en indexer instelt, raadpleegt u [Hoe u documenten in Azure Blob Storage indexeren met Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). Het blob-opslagartikel bevat ook informatie over welke documentindelingen worden ondersteund, welke blob-metagegevensworden geëxtraheerd, incrementele indexering en meer. Deze informatie is hetzelfde voor Data Lake Storage Gen2.

## <a name="access-control"></a>Toegangsbeheer

Azure Data Lake Storage Gen2 implementeert een [toegangscontrolemodel](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) dat zowel RBAC) als POSIX-achtige toegangscontrolelijsten (ACL's) ondersteunt voor Azure role-based access control.Azure Azure Data Lake Storage Gen2 implements an access control model that supports both Azure role-based access control (RBAC) and POSIX-like access control lists (ACL's). Bij het indexeren van inhoud uit Data Lake Storage Gen2 haalt Azure Cognitive Search de RBAC- en ACL-informatie niet uit de inhoud. Als gevolg hiervan wordt deze informatie niet opgenomen in uw Azure Cognitive Search-index.

Als het belangrijk is om het toegangscontrolebeheer op elk document in de index te behouden, is het aan de ontwikkelaar van de toepassing om [beveiligingsbijsnijden](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)te implementeren.

## <a name="change-detection"></a>Detectie wijzigen

De Data Lake Storage Gen2-indexer ondersteunt wijzigingsdetectie. Dit betekent dat wanneer de indexer wordt uitgevoerd, alleen de gewijzigde `LastModified` blobs worden geïndexeerd, zoals bepaald door de tijdstempel van de blob.

> [!NOTE] 
> Data Lake Storage Gen2 maakt het mogelijk mappen te hernoemen. Wanneer een map wordt hernoemd, worden de tijdstempels voor de blobs in die map niet bijgewerkt. Als gevolg hiervan zal de indexer deze blobs niet opnieuw indexeren. Als u de blobs in een map opnieuw moet indexeren nadat een map de naam `LastModified` wijzigt omdat ze nu nieuwe URL's hebben, moet u de tijdstempel voor alle blobs in de map bijwerken, zodat de indexer weet ze opnieuw te indexeren tijdens een toekomstige run.
