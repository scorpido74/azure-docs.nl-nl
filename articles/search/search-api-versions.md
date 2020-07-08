---
title: API-versies
titleSuffix: Azure Cognitive Search
description: Versie beleid voor Azure Cognitive Search REST-Api's en de client bibliotheek in de .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830091"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versies in azure Cognitive Search

De functie-updates worden regel matig door Azure Cognitive Search getotaleerd. Soms is voor deze updates niet altijd een nieuwe versie van de API nodig om achterwaartse compatibiliteit te behouden. Als u een nieuwe versie publiceert, kunt u bepalen wanneer en hoe u de updates van zoek services integreert in uw code.

Als regel publiceert het Azure Cognitive Search-team nieuwe versies alleen wanneer dat nodig is, omdat dit een zekere moeite kan zijn om uw code bij te werken om een nieuwe API-versie te gebruiken. Een nieuwe versie is alleen nodig als een aspect van de API is gewijzigd op een manier die achterwaartse compatibiliteit verbreekt. Dergelijke wijzigingen kunnen zich voordoen als gevolg van oplossingen voor bestaande functies of vanwege nieuwe functies die de bestaande API-surface area wijzigen.

Dezelfde regel is van toepassing op SDK-updates. De Azure Cognitive Search SDK volgt de regels voor [semantische versie beheer](https://semver.org/) , wat betekent dat de versie drie delen heeft: primair, secundair en buildnummer (bijvoorbeeld 1.1.0). Er wordt alleen een nieuwe primaire versie van de SDK uitgebracht voor wijzigingen die achterwaartse compatibiliteit verstoren. Bij het bijwerken van de niet-brekende onderdelen wordt de secundaire versie verhoogd, en met fout oplossingen wordt de build-versie alleen verhoogd.

> [!NOTE]
> Een exemplaar van de Azure Cognitive Search-service ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

## <a name="rest-apis"></a>REST-API’s

Deze tabel bevat de versie geschiedenis van huidige en eerder uitgebrachte versies van de Search Service REST API. Documentatie wordt gepubliceerd voor de huidige stabiele en Preview-versies.

| Versie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Probleem met achterwaartse compatibiliteit |
|-------------|--------|------------------------------|
| [Management 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | Algemeen beschikbaar | Nieuwste stabiele versie van de beheer REST-Api's, met verbeteringen in Endpoint Protection. Voegt een persoonlijk eind punt, ondersteuning voor persoonlijke koppelingen en netwerk regels voor nieuwe services toe. |
| [Management 2019-10-01-Preview](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | Preview  | Ondanks het versie nummer is dit nog steeds de huidige preview-versie van de REST Api's voor beheer. Er zijn op dit moment geen preview-functies. Alle preview-functies zijn onlangs overgegaan op algemene Beschik baarheid. |
| Beheer 2015-08-19  | Stabiel| De eerste beschik bare versie van de beheer REST-Api's. Biedt service-inrichting, opschalen en API-sleutel beheer. |
| Beheer 2015-08-19-preview | Preview| De eerste preview-versie van de beheer REST-Api's. |
| [Search 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Stabiel | De nieuwste stabiele versie van de zoek opdracht voor zoeken in REST-Api's, met voor uitgangen in relevantie scores. |
| [Search 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview)| Preview | Preview-versie gekoppeld aan stabiele versie. |
| Search 2019-05-06 | Stabiel | Voegt complexe typen toe. |
| Search 2019-05-06-preview | Preview | Preview-versie gekoppeld aan stabiele versie. |
| 2017-11-11 zoeken | Stabiel  | Vaardig heden en AI-verrijking toevoegen. |
| Search 2017-11-11-preview | Preview | Preview-versie gekoppeld aan stabiele versie. |
| 2016-09-01 zoeken |Stabiel | Indexeer functies toevoegen|
| Search 2016-09-01-preview | Preview | Preview-versie gekoppeld aan stabiele versie.|
| 2015-02-28 zoeken | Stabiel  | Eerste beschik bare release.  |
| Search 2015-02-28-preview | Preview | Preview-versie gekoppeld aan stabiele versie. |
| Search 2014-10-20-preview | Preview | Tweede open bare preview. |
| Search 2014-07-31-preview | Preview | Eerste open bare preview. |

## <a name="azure-sdk-for-net"></a>Azure-SDK voor .NET

De versie geschiedenis van het pakket is beschikbaar op NuGet.org. Deze tabel bevat koppelingen naar elke pakket pagina.

| SDK-versie | Status | Beschrijving |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0-Preview. 4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Preview | Nieuwe client bibliotheek van de Azure .NET SDK, uitgebracht op 2020. Streeft naar de REST 2020-06-30 API-versie|
| [**Micro soft. Azure. Search 10,0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Algemeen beschikbaar, uitgebracht op 2019. Streeft naar de REST 2019-05-06 API-versie.|
| [**Micro soft. Azure. Search 8,0-Preview**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Preview, uitgebracht op 2019 april. Streeft naar de REST 2019-05-06-preview-API-versie.|
| [**Micro soft. Azure. Management. Search 3.0.0**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stabiel | Streeft naar de beheer REST API-Version = 2015-08-19. |

## <a name="azure-sdk-for-java"></a>Azure SDK voor Java

| SDK-versie | Status | Beschrijving  |
|-------------|--------|------------------------------|
| [**Java SearchManagementClient 1.35.0**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stabiel | Streeft naar de beheer REST API-Version = 2015-08-19.|

## <a name="azure-sdk-for-python"></a>Azure-SDK voor Python

| SDK-versie | Status | Beschrijving  |
|-------------|--------|------------------------------|
| [**Python Azure-beheer-Search 1,0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stabiel | Streeft naar de beheer REST API-Version = 2015-08-19. |