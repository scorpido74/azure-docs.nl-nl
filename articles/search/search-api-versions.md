---
title: API-versiebeheer voor .NET en REST
titleSuffix: Azure Cognitive Search
description: Versiebeleid voor Azure Cognitive Search REST API's en de clientbibliotheek in de .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137285"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versies in Azure Cognitive Search

Azure Cognitive Search rolt regelmatig functie-updates uit. Soms, maar niet altijd, vereisen deze updates een nieuwe versie van de API om achterwaartse compatibiliteit te behouden. Als u een nieuwe versie publiceert, u bepalen wanneer en hoe u zoekservice-updates in uw code integreert.

In de regel publiceert het Azure Cognitive Search-team alleen nieuwe versies wanneer dat nodig is, omdat het enige moeite kan inhouden om uw code te upgraden naar een nieuwe API-versie. Een nieuwe versie is alleen nodig als een bepaald aspect van de API is veranderd op een manier die achterwaartse compatibiliteit breekt. Dergelijke wijzigingen kunnen optreden als gevolg van correcties aan bestaande functies of vanwege nieuwe functies die het bestaande API-oppervlak wijzigen.

Dezelfde regel geldt voor SDK-updates. De Azure Cognitive Search SDK volgt de [semantische versieregels,](https://semver.org/) wat betekent dat de versie drie delen heeft: groot, klein en buildnummer (bijvoorbeeld 1.1.0). Een nieuwe belangrijke versie van de SDK wordt alleen uitgebracht voor wijzigingen die achterwaartse compatibiliteit doorbreken. Niet-brekende functie-updates verhogen de secundaire versie en bugfixes verhogen de buildversie alleen maar.

> [!NOTE]
> Uw Azure Cognitive Search-serviceinstantie ondersteunt verschillende REST API-versies, waaronder de nieuwste. U een versie blijven gebruiken wanneer deze niet langer de laatste is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u de API-versie in elke aanvraag opgeven via de parameter api-versie. Bij gebruik van de .NET SDK bepaalt de versie van de SDK die u gebruikt de overeenkomstige versie van de REST API. Als u een oudere SDK gebruikt, u die code zonder wijzigingen blijven uitvoeren, zelfs als de service is geüpgraded om een nieuwere API-versie te ondersteunen.

## <a name="snapshot-of-current-versions"></a>Momentopname van huidige versies
Hieronder vindt u een momentopname van de huidige versies van alle programmeerinterfaces naar Azure Cognitive Search.


| Interfaces | Meest recente grote versie | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Algemeen beschikbaar, uitgebracht mei 2019 |
| [.NET SDK Preview](https://aka.ms/search-sdk-preview) |8.0-preview |Preview, uitgebracht april 2019 |
| [Service REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Algemeen beschikbaar |
| [Service REST API 2019-05-06-Preview](search-api-preview.md) |2019-05-06-Preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Algemeen beschikbaar |
| [REST-API voor beheer](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Algemeen beschikbaar |

Voor de REST API's is inclusief de `api-version` on elke oproep vereist. Met `api-version` behulp van maakt het eenvoudig om een specifieke versie te targeten, zoals een preview-API. In het volgende voorbeeld `api-version` wordt uitgelegd hoe de parameter is opgegeven:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Hoewel elke aanvraag `api-version`een aanvraag heeft, raden we u aan dezelfde versie te gebruiken voor alle API-aanvragen. Dit geldt vooral wanneer nieuwe API-versies kenmerken of bewerkingen introduceren die niet worden herkend door eerdere versies. Het mengen van API-versies kan onbedoelde gevolgen hebben en moet worden vermeden.
>
> De Service REST API en Management REST API worden onafhankelijk van elkaar uitgevoerd. Elke gelijkenis in versienummers is toevallig.

Algemeen beschikbare (of GA) API's kunnen worden gebruikt in de productie en zijn onderworpen aan Azure-servicelevelovereenkomsten. Previewversies hebben experimentele functies die niet altijd zijn gemigreerd naar een GA-versie. **U wordt ten zeerste aangeraden om preview-API's in productietoepassingen te vermijden.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Update naar de nieuwste versie van de REST API v.v. 15 oktober 2020
De volgende versies van de Azure Cognitive Search REST API worden buiten gebruik gesteld en worden niet meer ondersteund vanaf 15 oktober 2020: **2014-07-31-Preview,** **2014-10-20-Preview,** **2015-02-28-Preview**en **2015-02-28**. Bovendien worden versies van de Azure Cognitive Search .NET SDK ouder dan **3.0.0-rc** ook buiten gebruik gesteld omdat ze zich richten op een van deze REST API-versies. Na deze datum werken toepassingen die een van de afgeschafte REST API- of SDK-versies gebruiken, niet meer en moeten ze worden geüpgraded. Zoals met elke verandering van dit type, geven we 12 maanden van tevoren, dus je hebt voldoende tijd om aan te passen.  Als u Azure Cognitive Search wilt blijven gebruiken, migreert u bestaande code die is gericht op de [REST API-versie](search-api-migration.md) [2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) of nieuwer of de .NET SDK v.15 oktober 2020 naar [versie 3.0](search-dotnet-sdk-migration.md) of nieuwer.  Als je vragen hebt over updaten naar de azuresearch_contact@microsoft.com nieuwste versie, stuur dan voor 15 mei 2020 een e-mail om er zeker van te zijn dat je genoeg tijd hebt om je code bij te werken.

## <a name="about-preview-and-generally-available-versions"></a>Over preview- en algemeen beschikbare versies
Azure Cognitive Search brengt altijd eerst experimentele functies uit via de REST API en vervolgens via prerelease-versies van de .NET SDK.

Preview-functies zijn beschikbaar voor testen en experimenteren, met als doel het verzamelen van feedback over het ontwerp en de implementatie van functies. Om deze reden kunnen preview-functies in de loop van de tijd veranderen, mogelijk op manieren die de compatibiliteit achteruit breken. Dit in tegenstelling tot functies in een GA-versie, die stabiel zijn en waarschijnlijk niet zullen veranderen, met uitzondering van kleine achterwaarts compatibele oplossingen en verbeteringen. Ook preview-functies maken het niet altijd een GA-release.

Om deze redenen raden we u aan geen productiecode te schrijven die afhankelijk is van preview-versies. Als u een oudere voorbeeldversie gebruikt, raden we u aan te migreren naar de algemeen beschikbare GA-versie.

Voor de .NET SDK: Richtlijnen voor codemigratie zijn te vinden bij [Upgrade de .NET SDK](search-dotnet-sdk-migration-version-9.md).

Algemene beschikbaarheid betekent dat Azure Cognitive Search nu onder de SERVICE Level Agreement (SLA) valt. De SLA is te vinden bij [Azure Cognitive Search Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
