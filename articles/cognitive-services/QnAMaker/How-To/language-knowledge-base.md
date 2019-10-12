---
title: Taal concepten-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker ondersteunt Knowledge Base-inhoud in veel talen. Elke QnA Maker-service moet echter voor één taal worden gereserveerd. De eerste Knowledge Base die is gemaakt, gericht op een bepaalde QnA Maker service, stelt de taal van die service in.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 849c919950c57a1df3b0fb76021de6e10254c7b4
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286416"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Taal ondersteuning van Knowledge Base-inhoud voor QnA Maker

De taal voor de service wordt geselecteerd wanneer u de eerste Knowledge Base maakt in de resource. Alle aanvullende kennis grondslagen in de resource moeten zich in dezelfde taal bekomen. 

De taal bepaalt de relevantie van de resultaten QnA Maker worden uitgevoerd als reactie op gebruikers query's.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Eén taal voor alle kennis grondslagen in de resource

Met QnA Maker kunt u de taal voor uw QnA-service selecteren tijdens het maken van de eerste Knowledge Base. Voor alle Knowledge bases in een QnA Maker resource moeten alle kennis bronnen zich in dezelfde taal bevinden. Deze taal kan niet worden gewijzigd.

Het maken van kennis grondslagen in verschillende talen in een resource is een negatieve invloed op de relevantie van de resultaten QnA Maker levert antwoord op gebruikers query's.

Bekijk een lijst met [ondersteunde talen](../overview/language-support.md#languages-supported) en hoe talen invloed hebben op [treffers en relevantie](#query-matching-and-relevance). 

## <a name="select-language-when-creating-first-knowledge-base"></a>Taal selecteren bij het maken van de eerste Knowledge Base

Taal selectie maakt deel uit van de stappen voor het maken van de eerste Knowledge Base in een resource. 

![Scherm afbeelding van QnA Maker portal van het selecteren van de taal voor de eerste kennis database](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Overeenkomende treffers en relevantie van query's
QnA Maker is afhankelijk van [Azure Search taal analyse](https://docs.microsoft.com/rest/api/searchservice/language-support) functies voor het leveren van resultaten. 

Hoewel de Azure Search-functies op pari zijn voor ondersteunde talen, heeft QnA Maker een extra rangorde die zich boven de Azure Search-resultaten bevindt. In dit classificatie model gebruiken we enkele speciale semantische en op woord gebaseerde functies in de volgende talen. 

|Talen met extra rangorde|
|--|
|Chinees|
|Tsjechisch|
|Nederlands|
|Nederlands|
|Frans|
|Duits|
|Hongaars|
|Italiaans|
|Japans|
|Koreaans|
|Pools|
|Portugees|
|Spaans|
|Zweeds|

Deze extra classificatie is een interne werking van de QnA Makerer.

## <a name="verify-language"></a>Taal controleren

U kunt de taal van uw QnA Maker-bron controleren via de pagina Service-instellingen in de QnA Maker.

![Scherm afbeelding van de QnA Maker portal van de pagina Service-instellingen](../media/language-support/language-knowledge-base.png) 


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een QnA-bot maken met Azure Bot Service](../Tutorials/create-qna-bot.md)
