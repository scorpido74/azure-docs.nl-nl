---
title: API-versie beheer voor .NET SDK en REST-Api's-Azure Search
description: Versie beleid voor Azure Search REST-Api's en de client bibliotheek in de .NET SDK.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: f400c6fcb2b35e2adcf605c96bb802041cd9e0a9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182352"
---
# <a name="api-versions-in-azure-search"></a>API-versies in Azure Search
Azure Search updates van onderdelen regel matig samen. Soms is voor deze updates niet altijd een nieuwe versie van de API nodig om achterwaartse compatibiliteit te behouden. Als u een nieuwe versie publiceert, kunt u bepalen wanneer en hoe u de updates van zoek services integreert in uw code.

De Azure Search-team publiceert de nieuwe versies alleen wanneer dat nodig is, omdat het de moeite waard is om uw code bij te werken om een nieuwe API-versie te gebruiken. Een nieuwe versie is alleen nodig als een aspect van de API is gewijzigd op een manier die achterwaartse compatibiliteit verbreekt. Dergelijke wijzigingen kunnen zich voordoen als gevolg van oplossingen voor bestaande functies of vanwege nieuwe functies die de bestaande API-surface area wijzigen.

Dezelfde regel is van toepassing op SDK-updates. De Azure Search SDK volgt de regels voor [semantische versie beheer](https://semver.org/) , wat betekent dat de versie drie delen heeft: primair, secundair en buildnummer (bijvoorbeeld 1.1.0). Er wordt alleen een nieuwe primaire versie van de SDK uitgebracht voor wijzigingen die achterwaartse compatibiliteit verstoren. Bij het bijwerken van de niet-brekende onderdelen wordt de secundaire versie verhoogd, en met fout oplossingen wordt de build-versie alleen verhoogd.

> [!NOTE]
> Uw Azure Search service-exemplaar ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

## <a name="snapshot-of-current-versions"></a>Moment opname van huidige versies
Hieronder vindt u een moment opname van de huidige versies van alle programmeer interfaces die u kunt Azure Search.


| Interfaces | Meest recente primaire versie | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Algemeen beschikbaar, uitgebracht 2019 |
| [.NET SDK preview](https://aka.ms/search-sdk-preview) |8,0-Preview |Preview, uitgebracht: april 2019 |
| [Service REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Algemeen verkrijgbaar |
| [Service REST API 2019-05-06-preview](search-api-preview.md) |2019-05-06-preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Algemeen verkrijgbaar |
| [REST-API voor beheer](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Algemeen verkrijgbaar |

Voor de rest api's, met inbegrip van de `api-version` op elke aanroep is vereist. Met `api-version` behulp hiervan kunt u eenvoudig een specifieke versie instellen, zoals een preview-API. In het volgende voor beeld ziet u `api-version` hoe de para meter wordt opgegeven:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Hoewel elke aanvraag een `api-version`heeft, raden we u aan dezelfde versie te gebruiken voor alle API-aanvragen. Dit geldt met name wanneer nieuwe API-versies kenmerken of bewerkingen introduceren die niet worden herkend door eerdere versies. Het mixen van API-versies kan onbedoelde gevolgen hebben en moet worden vermeden.
>
> De service REST API en het beheer REST API afzonderlijk van elkaar zijn geversied. Eventuele gelijkenisën met versie nummers zijn bijkomend.

Algemeen beschik bare Api's (of GA) kunnen worden gebruikt in productie en zijn onderworpen aan Azure Service Level Agreements. Preview-versies hebben experimentele functies die niet altijd worden gemigreerd naar een GA-versie. **U wordt ten zeerste geadviseerd om Preview-Api's te gebruiken in productie toepassingen.**

## <a name="about-preview-and-generally-available-versions"></a>Over een preview-versie en algemeen beschik bare versies
Azure Search eerst experimentele functies vooraf op te heffen via de REST API en vervolgens via voorlopige versies van de .NET SDK.

Preview-functies zijn beschikbaar voor testen en experimenten, met als doel het verzamelen van feedback over het ontwerp en de implementatie van functies. Daarom kunnen preview-functies na verloop van tijd veranderen, mogelijk op manieren die achterwaartse compatibiliteit verstoren. Dit is in tegens telling tot functies in een GA-versie, die stabiel zijn en waarschijnlijk niet worden gewijzigd, met uitzonde ring van kleine neerwaarts compatibele oplossingen en uitbrei dingen. Daarnaast maken preview-functies niet altijd deel uit van een GA-versie.

Daarom wordt u aangeraden om een productie code te schrijven die afhankelijk is van Preview-versies. Als u een oudere preview-versie gebruikt, raden we u aan om te migreren naar de algemeen beschik bare versie (GA).

Voor de .NET SDK: Hulp bij het uitvoeren van code migratie vindt u bij [het upgraden van de .NET SDK](search-dotnet-sdk-migration-version-9.md).

De algemene Beschik baarheid betekent dat Azure Search nu onder de service level agreement (SLA). De SLA kan worden gevonden op [Azure Search Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
