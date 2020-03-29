---
title: Taalconcepten - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker ondersteunt knowledge base content in vele talen. Elke QnA Maker-service moet echter worden gereserveerd voor één taal. De eerste kennisbasis die is gemaakt en die zich richt op een bepaalde QnA Maker-service, bepaalt de taal van die service.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220631"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Taalondersteuning van knowledge base content voor QnA Maker

Taal voor de service wordt geselecteerd wanneer u de eerste kennisbasis in de resource maakt. Alle aanvullende kennisbanken in de bron moeten in dezelfde taal zijn.

De taal bepaalt de relevantie van de resultaten die QnA Maker biedt als antwoord op vragen van gebruikers.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Eén taal voor alle kennisbanken in resource

Met QnA Maker u de taal voor uw QnA-service selecteren en tegelijkertijd de eerste kennisbank maken. Voor alle kennisbanken in een QnA Maker-bron moeten ze allemaal in dezelfde taal zijn. Deze taal kan niet worden gewijzigd.

Het creëren van kennisbanken in verschillende talen in één resource heeft een negatieve invloed op de relevantie van de resultaten die QnA Maker biedt als antwoord op vragen van gebruikers.

Bekijk een lijst met [ondersteunde talen](../overview/language-support.md#languages-supported) en hoe talen van invloed zijn op matching [en relevantie.](#query-matching-and-relevance)

## <a name="select-language-when-creating-first-knowledge-base"></a>Taal selecteren bij het maken van de eerste kennisbasis

Taalselectie maakt deel uit van de stappen om de eerste kennisbasis in een resource te maken.

![QnA Maker portal screenshot van het selecteren van taal voor de eerste kennisbank](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Querymatching en relevantie
QnA Maker is afhankelijk van [Azure Cognitive Search taalanalysers](https://docs.microsoft.com/rest/api/searchservice/language-support) voor het verstrekken van resultaten.

Hoewel de Azure Cognitive Search-mogelijkheden op gelijke voet staan voor ondersteunde talen, heeft QnA Maker een extra ranker die boven de Azure-zoekresultaten zit. In dit rankermodel gebruiken we een aantal speciale semantische en op woorden gebaseerde functies in de volgende talen.

|Talen met extra ranker|
|--|
|Chinees|
|Tsjechisch|
|Nederlands|
|Engels|
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

Deze extra ranking is een interne werking van de QnA Maker's ranker.

## <a name="verify-language"></a>Taal verifiëren

U de taal van uw QnA Maker-bron verifiëren via de pagina service-instellingen in de QnA Maker.

![QnA Maker-portal schermafbeelding van pagina Service-instellingen](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een knowledge base migreren](../Tutorials/migrate-knowledge-base.md)
