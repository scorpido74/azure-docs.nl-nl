---
title: Limieten en grenzen - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker heeft meta-limieten voor het delen van de knowledge base en de service. Het is belangrijk dat u uw knowledge base binnen de grenzen om te testen en publiceren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c7b0dc39d2da403383f245b9ff3227734c58cbbe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193483"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker kennisdatabase limieten en grenzen

QnA Maker grenzen die hieronder worden aangegeven, zijn een combi natie van de [Azure Search prijs categorie limieten](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) en de [QnA Maker prijs categorie limieten](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). U moet beide sets limieten kennen om inzicht te krijgen in het aantal kennissen dat u per resource kunt maken en hoe groot elke kennis database kan groeien.

## <a name="knowledge-bases"></a>Kennis bases

Het maximum aantal kennis grondslagen is gebaseerd op de limieten van [Azure Search lagen](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Azure Search tier** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maxi maal aantal gepubliceerde kennis grondslagen toegestaan|2|14|49|199|199|2,999|

 Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases publiceren (1 index per gepubliceerde kennis basis). De vijftiende index `testkb`,, wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen. 

## <a name="extraction-limits"></a>Extractie limieten

### <a name="maximum-number-of-files"></a>Maximum aantal bestanden

Het maximum aantal bestanden dat kan worden geëxtraheerd en de maximale bestands grootte is gebaseerd op uw **[QnA Maker prijs categorie limieten](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maximum aantal diep gaande koppelingen van URL

Het maximum aantal diep gaande koppelingen dat kan worden verkend voor het uitpakken van QnAs van een URL-pagina is **20**.

## <a name="metadata-limits"></a>Limieten voor metagegevens

### <a name="by-azure-search-pricing-tier"></a>Per Azure Search prijs categorie

Het maximum aantal meta gegevens velden per Knowledge Base is gebaseerd op de limieten van uw **[Azure Search-laag](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Azure Search tier** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximale metagegevensvelden per QnA Maker-service (voor alle kB's)|1000|100 *|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Op naam en waarde

De lengte en de acceptabele tekens voor de naam en waarde van de meta gegevens worden weer gegeven in de volgende tabel.

|Item|Toegestane tekens|Overeenkomst met regex-patroon|Maximum aantal tekens|
|--|--|--|--|
|Name|Hulp<br>alfanumeriek (letters en cijfers)<br>`_`weigeren|`^[a-zA-Z0-9_]+$`|100|
|Value|Maakt alles mogelijk behalve<br>`:`punt<br>`|`(verticale pijp)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limieten voor Knowledge Base-inhoud
Algemene beperkingen met betrekking tot de inhoud in het knowledge base:
* Lengte van antwoord tekst: 25,000
* Lengte van de vraag tekst: 1000
* Lengte van meta gegevens sleutel/waarde: 100
* Ondersteunde tekens voor naam van meta gegevens: Letters, cijfers en`_`  
* Ondersteunde tekens voor de meta gegevens waarde: Alle behalve `:` en`|` 
* Lengte van bestands naam: 200
* Ondersteunde bestandsindelingen: ".tsv", '.pdf', '.txt', ".docx", '.xlsx'.
* Maximum aantal alternatieve vragen: 300
* Maximum aantal antwoord paren vraag: Is afhankelijk van de gekozen **[laag Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Een vraag-en-antwoord paar worden toegewezen aan een document op Azure Search index. 
* URL/HTML-pagina: 1.000.000 tekens

## <a name="create-knowledge-base-call-limits"></a>Limieten voor Knowledge base-aanroep maken:
Deze vertegenwoordigen de limieten voor elk maken knowledge base-actie. dat wil zeggen, te klikken op *maken KB* of de CreateKnowledgeBase-API aan te roepen.
* Maximum aantal alternatieve vragen per antwoord: 300
* Maximum aantal Url's: 10
* Maximum aantal bestanden: 10

## <a name="update-knowledge-base-call-limits"></a>Knowledge base-aanroep limieten bijwerken
Dit zijn de limieten voor elke update-actie. dat wil zeggen, te klikken op *opslaan en trainen* of de UpdateKnowledgeBase-API aan te roepen.
* Lengte van elke bron naam: 300
* Maximum aantal alternatieve vragen dat is toegevoegd of verwijderd: 300
* Maximum aantal meta gegevens velden dat is toegevoegd of verwijderd: 10
* Maximum aantal Url's dat kan worden vernieuwd: 5

## <a name="next-steps"></a>Volgende stappen

Meer informatie over wanneer en hoe u de [service prijs categorieën](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)wijzigt.
