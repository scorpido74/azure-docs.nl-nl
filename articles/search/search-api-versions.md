---
title: API-versie beheer voor .NET SDK en REST-Api's
titleSuffix: Azure Cognitive Search
description: Versie beleid voor Azure Cognitive Search REST-Api's en de client bibliotheek in de .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4a976655d0f634c7026f008d64516a629947e90c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793061"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versies in azure Cognitive Search

De functie-updates worden regel matig door Azure Cognitive Search getotaleerd. Soms is voor deze updates niet altijd een nieuwe versie van de API nodig om achterwaartse compatibiliteit te behouden. Als u een nieuwe versie publiceert, kunt u bepalen wanneer en hoe u de updates van zoek services integreert in uw code.

Als regel publiceert het Azure Cognitive Search-team nieuwe versies alleen wanneer dat nodig is, omdat dit een zekere moeite kan zijn om uw code bij te werken om een nieuwe API-versie te gebruiken. Een nieuwe versie is alleen nodig als een aspect van de API is gewijzigd op een manier die achterwaartse compatibiliteit verbreekt. Dergelijke wijzigingen kunnen zich voordoen als gevolg van oplossingen voor bestaande functies of vanwege nieuwe functies die de bestaande API-surface area wijzigen.

Dezelfde regel is van toepassing op SDK-updates. De Azure Cognitive Search SDK volgt de regels voor [semantische versie beheer](https://semver.org/) , wat betekent dat de versie drie delen heeft: primair, secundair en buildnummer (bijvoorbeeld 1.1.0). Er wordt alleen een nieuwe primaire versie van de SDK uitgebracht voor wijzigingen die achterwaartse compatibiliteit verstoren. Bij het bijwerken van de niet-brekende onderdelen wordt de secundaire versie verhoogd, en met fout oplossingen wordt de build-versie alleen verhoogd.

> [!NOTE]
> Uw Azure Cognitive Search service-exemplaar ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

## <a name="snapshot-of-current-versions"></a>Moment opname van huidige versies
Hieronder vindt u een moment opname van de huidige versies van alle programmeer interfaces naar Azure Cognitive Search.


| Natie | Meest recente primaire versie | Status |
| --- | --- | --- |
| [.NET-SDK](https://aka.ms/search-sdk) |9.0 |Algemeen beschikbaar, uitgebracht 2019 |
| [.NET SDK preview](https://aka.ms/search-sdk-preview) |8,0-Preview |Preview, uitgebracht: april 2019 |
| [Service-REST-API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Algemeen verkrijgbaar |
| [Service REST API 2019-05-06-preview](search-api-preview.md) |2019-05-06-preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Algemeen verkrijgbaar |
| [REST-API voor beheer](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Algemeen verkrijgbaar |

Voor de REST-Api's, met inbegrip van de `api-version` op elke aanroep is vereist. Met behulp van `api-version` is het eenvoudig om een specifieke versie te richten, zoals een preview-API. In het volgende voor beeld ziet u hoe de para meter `api-version` wordt opgegeven:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Hoewel elke aanvraag een `api-version`heeft, raden we u aan dezelfde versie te gebruiken voor alle API-aanvragen. Dit geldt met name wanneer nieuwe API-versies kenmerken of bewerkingen introduceren die niet worden herkend door eerdere versies. Het mixen van API-versies kan onbedoelde gevolgen hebben en moet worden vermeden.
>
> De service REST API en het beheer REST API afzonderlijk van elkaar zijn geversied. Eventuele gelijkenisën met versie nummers zijn bijkomend.

Algemeen beschik bare Api's (of GA) kunnen worden gebruikt in productie en zijn onderworpen aan Azure Service Level Agreements. Preview-versies hebben experimentele functies die niet altijd worden gemigreerd naar een GA-versie. **U wordt ten zeerste geadviseerd om Preview-Api's te gebruiken in productie toepassingen.**

## <a name="about-preview-and-generally-available-versions"></a>Over een preview-versie en algemeen beschik bare versies
Met Azure Cognitive Search worden eerst experimentele functies vooraf vrijgegeven via de REST API en vervolgens via voorlopige versies van de .NET SDK.

Preview-functies zijn beschikbaar voor testen en experimenten, met als doel het verzamelen van feedback over het ontwerp en de implementatie van functies. Daarom kunnen preview-functies na verloop van tijd veranderen, mogelijk op manieren die achterwaartse compatibiliteit verstoren. Dit is in tegens telling tot functies in een GA-versie, die stabiel zijn en waarschijnlijk niet worden gewijzigd, met uitzonde ring van kleine neerwaarts compatibele oplossingen en uitbrei dingen. Daarnaast maken preview-functies niet altijd deel uit van een GA-versie.

Daarom wordt u aangeraden om een productie code te schrijven die afhankelijk is van Preview-versies. Als u een oudere preview-versie gebruikt, raden we u aan om te migreren naar de algemeen beschik bare versie (GA).

Voor de .NET SDK: richt lijnen voor code migratie vindt u bij [het upgraden van de .NET SDK](search-dotnet-sdk-migration-version-9.md).

De algemene Beschik baarheid houdt in dat Azure Cognitive Search nu onder de service level agreement (SLA). De SLA is te vinden in [Azure Cognitive Search Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
