---
title: API-versie beheer voor .NET en REST
titleSuffix: Azure Cognitive Search
description: Versie beleid voor Azure Cognitive Search REST-Api's en de client bibliotheek in de .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 20f11b14c0393f27a81710218d630f052775e1c3
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85078965"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versies in azure Cognitive Search

De functie-updates worden regel matig door Azure Cognitive Search getotaleerd. Soms is voor deze updates niet altijd een nieuwe versie van de API nodig om achterwaartse compatibiliteit te behouden. Als u een nieuwe versie publiceert, kunt u bepalen wanneer en hoe u de updates van zoek services integreert in uw code.

Als regel publiceert het Azure Cognitive Search-team nieuwe versies alleen wanneer dat nodig is, omdat dit een zekere moeite kan zijn om uw code bij te werken om een nieuwe API-versie te gebruiken. Een nieuwe versie is alleen nodig als een aspect van de API is gewijzigd op een manier die achterwaartse compatibiliteit verbreekt. Dergelijke wijzigingen kunnen zich voordoen als gevolg van oplossingen voor bestaande functies of vanwege nieuwe functies die de bestaande API-surface area wijzigen.

Dezelfde regel is van toepassing op SDK-updates. De Azure Cognitive Search SDK volgt de regels voor [semantische versie beheer](https://semver.org/) , wat betekent dat de versie drie delen heeft: primair, secundair en buildnummer (bijvoorbeeld 1.1.0). Er wordt alleen een nieuwe primaire versie van de SDK uitgebracht voor wijzigingen die achterwaartse compatibiliteit verstoren. Bij het bijwerken van de niet-brekende onderdelen wordt de secundaire versie verhoogd, en met fout oplossingen wordt de build-versie alleen verhoogd.

> [!NOTE]
> Uw Azure Cognitive Search service-exemplaar ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

## <a name="snapshot-of-current-versions"></a>Moment opname van huidige versies
Hieronder vindt u een moment opname van de huidige versies van alle programmeer interfaces naar Azure Cognitive Search.


| Interfaces | Meest recente primaire versie | Status |
| --- | --- | --- |
| [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search) |9.0 |Algemeen beschikbaar, uitgebracht 2019 |
| [.NET SDK preview](https://aka.ms/search-sdk-preview) |8,0-Preview |Preview, uitgebracht: april 2019 |
| [Service REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Algemeen beschikbaar |
| [Service REST API 2019-05-06-preview](search-api-preview.md) |2019-05-06-preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Algemeen beschikbaar |
| [REST-API voor beheer](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Algemeen beschikbaar |

Voor de REST Api's, met inbegrip `api-version` van de op elke aanroep is vereist. Met behulp `api-version` hiervan kunt u eenvoudig een specifieke versie instellen, zoals een preview-API. In het volgende voor beeld ziet u hoe de `api-version` para meter wordt opgegeven:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Hoewel elke aanvraag een heeft `api-version` , raden we u aan dezelfde versie te gebruiken voor alle API-aanvragen. Dit geldt met name wanneer nieuwe API-versies kenmerken of bewerkingen introduceren die niet worden herkend door eerdere versies. Het mixen van API-versies kan onbedoelde gevolgen hebben en moet worden vermeden.
>
> De service REST API en het beheer REST API afzonderlijk van elkaar zijn geversied. Eventuele gelijkenisën met versie nummers zijn bijkomend.

Algemeen beschik bare Api's (of GA) kunnen worden gebruikt in productie en zijn onderworpen aan Azure Service Level Agreements. Preview-versies hebben experimentele functies die niet altijd worden gemigreerd naar een GA-versie. **U wordt ten zeerste geadviseerd om Preview-Api's te gebruiken in productie toepassingen.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Update naar de nieuwste versie van de REST API op 15 oktober 2020
De volgende versies van de Azure Cognitive Search REST API worden buiten gebruik gesteld en worden niet meer ondersteund vanaf 15 oktober 2020: **2014-07-31-preview**, **2014-10-20-preview**, **2015-02-28-preview**en **2015-02-28**. Daarnaast worden versies van de Azure Cognitive Search .NET SDK die ouder zijn dan **3.0.0-RC** ook buiten gebruik gesteld, omdat ze een van deze rest API versies richten. Na deze datum zullen toepassingen die gebruikmaken van een van de afgeschafte REST API-of SDK-versies niet meer werken en moeten ze worden bijgewerkt. Net als bij elke wijziging van dit type, geven we de kennisgeving van 12 maanden, zodat u voldoende tijd hebt om aan te passen.  Als u Azure Cognitive Search wilt blijven gebruiken, moet u de bestaande code migreren die de [rest API](search-api-migration.md) voor [rest API versie 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) of hoger of de .net SDK naar [versie 3,0](search-dotnet-sdk-migration.md) of hoger op 15 oktober 2020.  Als u vragen hebt over het bijwerken van de nieuwste versie, stuurt u een e-mail naar azuresearch_contact@microsoft.com 15 mei 2020 om ervoor te zorgen dat u voldoende tijd hebt om uw code bij te werken.

## <a name="about-preview-and-generally-available-versions"></a>Over een preview-versie en algemeen beschik bare versies
Met Azure Cognitive Search worden eerst experimentele functies vooraf vrijgegeven via de REST API en vervolgens via voorlopige versies van de .NET SDK.

Preview-functies zijn beschikbaar voor testen en experimenten, met als doel het verzamelen van feedback over het ontwerp en de implementatie van functies. Daarom kunnen preview-functies na verloop van tijd veranderen, mogelijk op manieren die achterwaartse compatibiliteit verstoren. Dit is in tegens telling tot functies in een GA-versie, die stabiel zijn en waarschijnlijk niet worden gewijzigd, met uitzonde ring van kleine neerwaarts compatibele oplossingen en uitbrei dingen. Daarnaast maken preview-functies niet altijd deel uit van een GA-versie.

Daarom wordt u aangeraden om een productie code te schrijven die afhankelijk is van Preview-versies. Als u een oudere preview-versie gebruikt, raden we u aan om te migreren naar de algemeen beschik bare versie (GA).

Voor de .NET SDK: richt lijnen voor code migratie vindt u bij [het upgraden van de .NET SDK](search-dotnet-sdk-migration-version-9.md).

De algemene Beschik baarheid houdt in dat Azure Cognitive Search nu onder de service level agreement (SLA). De SLA is te vinden in [Azure Cognitive Search Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
