---
title: Limieten en grenzen-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker heeft meta limieten voor delen van de Knowledge Base en de service. Het is belang rijk dat u uw Knowledge Base binnen deze grenzen houdt om te testen en te publiceren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4effd14029eaaee1e1c22cdb814096820e19e089
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794034"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limieten en grenzen voor de Knowledge Base QnA Maker

QnA Maker grenzen die hieronder worden aangegeven, zijn een combi natie van de [limieten voor de prijs categorie voor Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) en de [QnA Maker prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). U moet beide sets limieten kennen om inzicht te krijgen in het aantal kennissen dat u per resource kunt maken en hoe groot elke kennis database kan groeien.

## <a name="knowledge-bases"></a>Kennis bases

Het maximum aantal kennis grondslagen is gebaseerd op [limieten voor Azure-Cognitive Search lagen](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Azure Cognitive Search-laag** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maxi maal aantal gepubliceerde kennis grondslagen toegestaan|2|14|49|199|199|2\.999|

 Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases publiceren (1 index per gepubliceerde kennis basis). De vijftiende index, `testkb`, wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen. 

## <a name="extraction-limits"></a>Extractie limieten

### <a name="maximum-number-of-files"></a>Maximum aantal bestanden

Het maximum aantal bestanden dat kan worden geëxtraheerd en de maximale bestands grootte is gebaseerd op uw **[QnA Maker prijs categorie limieten](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maximum aantal diep gaande koppelingen van URL

Het maximum aantal diep gaande koppelingen dat kan worden verkend voor het uitpakken van QnAs van een URL-pagina is **20**.

## <a name="metadata-limits"></a>Limieten voor meta gegevens

### <a name="by-azure-cognitive-search-pricing-tier"></a>Door de prijs categorie voor Azure Cognitive Search

Het maximum aantal meta gegevens velden per Knowledge Base is gebaseerd op de **[limieten van uw Azure Cognitive Search-laag](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Azure Cognitive Search-laag** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximum aantal meta gegevens velden per QnA Maker service (in alle Kb's)|1000|100 *|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Op naam en waarde

De lengte en de acceptabele tekens voor de naam en waarde van de meta gegevens worden weer gegeven in de volgende tabel.

|Item|Toegestane tekens|Overeenkomst met regex-patroon|Maximum aantal tekens|
|--|--|--|--|
|Naam|Hulp<br>alfanumeriek (letters en cijfers)<br>`_` (onderstrepings teken)|`^[a-zA-Z0-9_]+$`|100|
|Waarde|Maakt alles mogelijk behalve<br>`:` (dubbele punt)<br>`|` (verticale pijp)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limieten voor Knowledge Base-inhoud
Algemene limieten voor de inhoud van de Knowledge Base:
* Lengte van antwoord tekst: 25.000
* Lengte van de vraag tekst: 1.000
* Lengte van meta gegevens sleutel/waarde: 100
* Ondersteunde tekens voor de naam van de meta gegevens: alfabetten, cijfers en `_`  
* Ondersteunde tekens voor de meta gegevens waarde: alle behalve `:` en `|` 
* Lengte van bestands naam: 200
* Ondersteunde bestands indelingen: '. TSV ', '. PDF ', '. txt ', '. docx ', '. xlsx '.
* Maximum aantal alternatieve vragen: 300
* Maximum aantal antwoord paren voor vraag: is afhankelijk van de gekozen **[Azure Cognitive Search-laag](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Een vraag-en-antwoord paar worden toegewezen aan een document in azure Cognitive Search index. 
* URL/HTML-pagina: 1.000.000 tekens

## <a name="create-knowledge-base-call-limits"></a>Maak Knowledge Base-oproep limieten:
Dit zijn de limieten voor elke actie voor het maken van een Knowledge Base; dat wil zeggen, klikken op *KB maken* of de CREATEKNOWLEDGEBASE-API aanroepen.
* Maximum aantal alternatieve vragen per antwoord: 300
* Maximum aantal Url's: 10
* Maximum aantal bestanden: 10

## <a name="update-knowledge-base-call-limits"></a>Limieten voor Knowledge Base-aanroepen bijwerken
Dit duidt op de limieten voor elke update-actie. dat wil zeggen, klikken op *opslaan en trainen* of het aanroepen van de UPDATEKNOWLEDGEBASE-API.
* Lengte van elke bron naam: 300
* Maximum aantal alternatieve vragen dat is toegevoegd of verwijderd: 300
* Maximum aantal meta gegevens velden dat is toegevoegd of verwijderd: 10
* Maximum aantal Url's dat kan worden vernieuwd: 5

## <a name="next-steps"></a>Volgende stappen

Meer informatie over wanneer en hoe u de [service prijs categorieën](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)wijzigt.
