---
title: Vaardig heden opnieuw instellen (API-Version = 2019-050 -06-preview)
titleSuffix: Azure Cognitive Search
description: Preview-REST API gebruikt voor incrementele verrijking wanneer u hele of gedeeltelijke herverwerking van een vaardig heden nodig hebt.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832161"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Vaardig heden opnieuw instellen (API-Version = 2019-05 -06-preview)

> [!IMPORTANT] 
> Incrementele verrijking is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen portal-of .NET SDK-ondersteuning.

De aanvraag voor het **opnieuw instellen van vaardig heden** geeft aan welke vaardig heden moeten worden verwerkt bij de uitvoering van de volgende Indexeer functie. Voor Indexeer functies waarvoor caching is ingeschakeld, kunt u expliciet aanvragen verwerken voor vaardigheids updates die de Indexeer functie niet kan detecteren. Als u bijvoorbeeld externe wijzigingen aanbrengt, zoals revisies op een aangepaste vaardigheid, kunt u deze API gebruiken om de vaardigheid opnieuw uit te voeren. Uitvoer, zoals een kennis archief of een zoek index, worden vernieuwd met herbruikbare gegevens uit de cache en nieuwe inhoud per de bijgewerkte vaardigheid.

U kunt een bestaande [vaardig heden](https://docs.microsoft.com/rest/api/searchservice/create-skillset) opnieuw instellen met behulp van een http-put, waarbij u de naam van de vaardig heden opgeeft die moet worden bijgewerkt op de aanvraag-URI. U moet **API-Version = 2019-05 -06-preview** gebruiken voor de aanvraag.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

U kunt ook POST gebruiken en de naam van de Indexeer functie in de hoofd tekst van de aanvraag plaatsen:

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>Aanvraagheaders  

 In de volgende tabel worden de vereiste en optionele aanvraag headers beschreven.  

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Content-Type:*|Vereist. Stel dit in op `application/json`|  
|*API-sleutel:*|Vereist. De `api-key` wordt gebruikt om de aanvraag voor uw zoek service te verifiëren. Het is een teken reeks waarde die uniek is voor uw service. De aanvraag voor het **opnieuw instellen van de vaardig heden** moet een `api-key` header bevatten die is ingesteld op uw beheerders sleutel (in plaats van een query sleutel).|  

U hebt ook de service naam nodig om de aanvraag-URL te maken. U kunt de service naam en het `api-key` ophalen op de pagina overzicht van de service in de Azure Portal. Zie [een Azure Cognitive Search-service maken](https://docs.microsoft.com/azure/search/search-create-service-portal) voor Help voor pagina navigatie.  

## <a name="request-body-syntax"></a>Syntaxis van aanvraag tekst  

De hoofd tekst van de aanvraag is een matrix met vaardigheids namen.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Antwoord  

Status code: 204 geen inhoud voor een geslaagde reactie. 

## <a name="see-also"></a>Zie ook

+ [REST-Api's zoeken](https://docs.microsoft.com/rest/api/searchservice)
+ [HTTP-statuscode](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [Overzicht van AI-verrijking](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Caching en incrementele verrijking configureren](search-howto-incremental-index.md)
+ [Incrementele verrijking](cognitive-search-incremental-indexing-conceptual.md)