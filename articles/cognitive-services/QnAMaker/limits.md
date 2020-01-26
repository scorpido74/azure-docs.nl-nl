---
title: Limieten en grenzen - QnA Maker
description: QnA Maker heeft meta-limieten voor het delen van de knowledge base en de service. Het is belangrijk dat u uw knowledge base binnen de grenzen om te testen en publiceren.
ms.topic: article
ms.date: 01/23/2020
ms.openlocfilehash: 32e12d9e983795d2fb403a5b6bc304289ece92c2
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760229"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker kennisdatabase limieten en grenzen

QnA Maker grenzen die hieronder worden aangegeven, zijn een combi natie van de [limieten voor de prijs categorie voor Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) en de [QnA Maker prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). U moet beide sets limieten kennen om inzicht te krijgen in het aantal kennissen dat u per resource kunt maken en hoe groot elke kennis database kan groeien.

## <a name="knowledge-bases"></a>Kennis bases

Het maximum aantal kennis grondslagen is gebaseerd op [limieten voor Azure-Cognitive Search lagen](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Azure Cognitive Search-laag** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maxi maal aantal gepubliceerde kennis grondslagen toegestaan|2|14|49|199|199|2,999|

 Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases publiceren (1 index per gepubliceerde kennis basis). De vijftiende index, `testkb`, wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen.

## <a name="extraction-limits"></a>Extractie limieten

### <a name="file-naming-constraints"></a>Beperkingen voor bestands naamgeving

Bestands namen mogen niet de volgende tekens bevatten:

|Geen teken gebruiken|
|--|
|`'` met enkele aanhalings tekens|
|`"` voor dubbele aanhalings tekens|

### <a name="maximum-file-size"></a>Maximale bestandsgrootte

|Indeling|Maximale bestands grootte (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximum aantal bestanden

Het maximum aantal bestanden dat kan worden geëxtraheerd en de maximale bestands grootte is gebaseerd op uw **[QnA Maker prijs categorie limieten](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maximum aantal diep gaande koppelingen van URL

Het maximum aantal diep gaande koppelingen dat kan worden verkend voor het uitpakken van QnAs van een URL-pagina is **20**.

## <a name="metadata-limits"></a>Limieten voor metagegevens

### <a name="by-azure-cognitive-search-pricing-tier"></a>Door de prijs categorie voor Azure Cognitive Search

Het maximum aantal meta gegevens velden per Knowledge Base is gebaseerd op de **[limieten van uw Azure Cognitive Search-laag](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Azure Cognitive Search-laag** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximale metagegevensvelden per QnA Maker-service (voor alle kB's)|1000|100 *|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Op naam en waarde

De lengte en de acceptabele tekens voor de naam en waarde van de meta gegevens worden weer gegeven in de volgende tabel.

|Item|Toegestane tekens|Overeenkomst met regex-patroon|Maximum aantal tekens|
|--|--|--|--|
|Name|Hulp<br>alfanumeriek (letters en cijfers)<br>`_` (onderstrepings teken)|`^[a-zA-Z0-9_]+$`|100|
|Waarde|Maakt alles mogelijk behalve<br>`:` (dubbele punt)<br>`|` (verticale pijp)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limieten voor Knowledge Base-inhoud
Algemene beperkingen met betrekking tot de inhoud in het knowledge base:
* Lengte van de antwoordtekst: 25.000
* Lengte van de vraagtekst: 1000
* Lengte van de metagegevens van sleutel/waarde-tekst: 100
* Ondersteunde tekens voor de naam van de meta gegevens: alfabetten, cijfers en `_`
* Ondersteunde tekens voor de meta gegevens waarde: alle behalve `:` en `|`
* Lengte van bestandsnaam: 200
* Ondersteunde bestandsindelingen: ".tsv", '.pdf', '.txt', ".docx", '.xlsx'.
* Maximum aantal alternatieve vragen: 300
* Maximum aantal antwoord paren voor vraag: is afhankelijk van de gekozen **[Azure Cognitive Search-laag](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Een vraag-en-antwoord paar worden toegewezen aan een document in azure Cognitive Search index.
* URL/HTML-pagina: 1.000.000 tekens

## <a name="create-knowledge-base-call-limits"></a>Limieten voor Knowledge base-aanroep maken:
Deze vertegenwoordigen de limieten voor elk maken knowledge base-actie. dat wil zeggen, te klikken op *maken KB* of de CreateKnowledgeBase-API aan te roepen.
* Maximum aantal alternatieve vragen per antwoord: 300
* Maximum aantal URL's: 10
* Maximum aantal bestanden: 10

## <a name="update-knowledge-base-call-limits"></a>Knowledge base-aanroep limieten bijwerken
Dit zijn de limieten voor elke update-actie. dat wil zeggen, te klikken op *opslaan en trainen* of de UpdateKnowledgeBase-API aan te roepen.
* Lengte van de bronnaam van elke: 300
* Maximum aantal alternatieve vragen dat is toegevoegd of verwijderd: 300
* Maximum aantal metagegevensvelden toegevoegd of verwijderd: 10
* Maximum aantal URL's die kunnen worden vernieuwd: 5

## <a name="next-steps"></a>Volgende stappen

Meer informatie over wanneer en hoe u de [service prijs categorieën](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)wijzigt.
