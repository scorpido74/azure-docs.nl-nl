---
title: Taalondersteuning - QnA Maker
titleSuffix: Azure Cognitive Services
description: De taal van een Knowledge Base is van invloed op de mogelijkheid van QnA Maker om vragen en antwoorden van bronnen automatisch uit te pakken, evenals de relevantie van de resultaten QnA Maker als reactie op gebruikers query's. Een lijst van cultuur, natuurlijke talen die worden ondersteund door de QnA Maker voor uw knowledge base. Geen verschillende talen in de dezelfde knowledge base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066665"
---
# <a name="language-support-for-qna-maker"></a>Taal ondersteuning voor QnA Maker

De taal van een kennisdatabase is van invloed op de QnA Maker kunnen automatisch extract vragen en antwoorden uit [bronnen](../Concepts/data-sources-supported.md), evenals de relevantie van de resultaten QnA Maker in reactie op query's van gebruikers biedt.

## <a name="auto-extraction"></a>Automatische extractie
QnA Maker biedt ondersteuning voor vraag/antwoord uitpakken in een willekeurige pagina taal, maar de effectiviteit van de winning is veel hoger zijn voor de volgende talen, zoals QnA Maker trefwoorden gebruikt om te identificeren van vragen.

|Ondersteunde talen| Landinstelling|
|-----|----|
|Nederlands|nl-*|
|Frans|FR-*|
|Italiaans|IT-*|
|Duits|de-*|
|Spaans|ES-*|

## <a name="primary-language-detection"></a>Primaire taal detectie

De primaire taal die wordt gebruikt voor de detectie is ingesteld voor de QnA Maker resource, en alle kennis grondslagen die zijn gemaakt voor die resource, wanneer het eerste document of URL wordt toegevoegd aan de eerste Knowledge Base. De taal kan niet worden gewijzigd. 

Als de gebruiker meerdere talen wil ondersteunen, moeten ze voor elke taal een nieuwe QnA Maker Resource hebben. Meer informatie over het [maken van een op talen gebaseerde QnA Maker Knowledge Base](../how-to/language-knowledge-base.md).  

Controleer de primaire taal door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).  
1. Zoek en selecteer de Azure Search resource die is gemaakt als onderdeel van uw QnA Maker resource. De naam van de Azure Search resource begint met dezelfde naam als de QnA Maker resource en heeft de type- **Zoek service**. Houd er rekening mee dat er slechts één QnA Maker resource kan worden gekoppeld aan een Azure Search resource.
1. Selecteer op de pagina **overzicht** van de resource zoeken de optie **indexen**. 
1. Selecteer de **testkb** -index.
1. Selecteer het tabblad **velden** . 
1. Bekijk de kolom **Analyzer** voor de velden **vragen** en **antwoord** . 


## <a name="query-matching-and-relevance"></a>Query's die overeenkomen met en relevantie
QnA Maker is afhankelijk van [taalanalyse](https://docs.microsoft.com/rest/api/searchservice/language-support) in Azure search om resultaten te bieden. Speciale opnieuw ranking functies zijn beschikbaar voor nl-* talen waarmee betere relevantie.

De Azure Search-mogelijkheden zijn op par voor ondersteunde talen, heeft QnA Maker een extra kerntechnologie die bevindt zich boven de Azure search-resultaten. In dit classificatie model gebruiken we enkele speciale semantische en op woord gebaseerde functies in en-* die nog niet beschikbaar zijn voor andere talen. Deze functies zijn niet beschikbaar, omdat ze deel uitmaken van de interne werking van de classificatie functie van de QnA Maker. 

QnA Maker [automatisch de taal van de Knowledge Base detecteert](#primary-language-detection) tijdens het maken en stelt de Analyzer dienovereenkomstig in. U kunt knowledge bases maken in de volgende talen. 

|Ondersteunde talen|
|-----|
|Arabisch|
|Armeens|
Bengaals|
|Baskisch|
|Bulgaars|
|Catalaans|
|Chinese_Simplified|
|Chinese_Traditional|
|Kroatisch|
|Tsjechisch|
|Deens|
|Nederlands|
|Nederlands|
|Estisch|
|Fins|
|Frans|
|Galicisch|
|Duits|
|Grieks|
|Gujarati|
|Hebreeuws|
|Hindi|
|Hongaars|
|IJslands|
|Indonesisch|
|Iers|
|Italiaans|
|Japans|
|Kanarees|
|Koreaans|
|Lets|
|Litouws|
|Malayalam|
|Maleis|
|Noors|
|Pools|
|Portugees|
|Punjabi|
|Roemeens|
|Russisch|
|Serbian_Cyrillic|
|Serbian_Latin|
|Slowaaks|
|Sloveens|
|Spaans|
|Zweeds|
|Tamil|
|Telugu|
|Thais|
|Turks|
|Oekraïens|
|Urdu|
|Vietnamees|
