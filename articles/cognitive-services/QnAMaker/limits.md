---
title: Grenzen en grenzen - QnA Maker
description: QnA Maker heeft metalimieten voor delen van de kennisbank en service. Het is belangrijk om uw kennisbank binnen die grenzen te houden om te testen en te publiceren.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273340"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker kennisbank grenzen en grenzen

QnA Maker-limieten hieronder zijn een combinatie van de [prijsniveaus voor Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) en de [prijsniveaus van QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). U moet beide limieten kennen om te begrijpen hoeveel kennisbases u per resource maken en hoe groot elke kennisbasis kan groeien.

## <a name="knowledge-bases"></a>Kennisbanken

Het maximum aantal kennisbases is gebaseerd op [azure cognitive search-laaglimieten.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Azure Cognitive Search-laag** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximaal aantal gepubliceerde kennisbanken toegestaan|2|14|49|199|199|2,999|

 Als uw laag bijvoorbeeld 15 toegestane indexen heeft, u 14 kennisbanken publiceren (1 index per gepubliceerde kennisbank). De vijftiende `testkb`index, wordt gebruikt voor alle kennisbanken voor authoring en testen.

## <a name="extraction-limits"></a>Extractielimieten

### <a name="file-naming-constraints"></a>Beperkingen voor bestandsnaamgeving

Bestandsnamen mogen niet de volgende tekens bevatten:

|Teken niet gebruiken|
|--|
|Eén offerte`'`|
|Dubbele offerte`"`|

### <a name="maximum-file-size"></a>Maximale bestandsgrootte

|Indeling|Maximale bestandsgrootte (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximum aantal bestanden

Het maximum aantal bestanden dat kan worden geëxtraheerd en de maximale bestandsgrootte is gebaseerd op de prijsniveaulimieten van **[QnA Maker.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**

### <a name="maximum-number-of-deep-links-from-url"></a>Maximum aantal deep-links van URL

Het maximum aantal deep-links dat kan worden gecrawld voor het ophalen van QnA's van een URL-pagina is **20**.

## <a name="metadata-limits"></a>Metagegevenslimieten

Metagegevens worden gepresenteerd als een op tekst `product:windows 10`gebaseerde sleutel:waardepaar, zoals . Het wordt opgeslagen en vergeleken in kleine letters.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Op azure cognitive search-prijscategorie

Het maximum aantal metagegevensvelden per kennisbasis is gebaseerd op uw **[azure cognitive search-laaglimieten.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)**

|**Azure Cognitive Search-laag** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximale metagegevensvelden per QnA Maker-service (voor alle KB's)|1000|100*|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Op naam en waarde

De lengte en acceptabele tekens voor de naam en de waarde van metagegevens worden in de volgende tabel weergegeven.

|Item|Toegestaan chars|Regex-patroonovereenkomst|Max chars|
|--|--|--|--|
|Naam (sleutel)|Allows<br>alfanumeriek (letters en cijfers)<br>`_`(onderstrepen)<br> Mag geen spaties bevatten.|`^[a-zA-Z0-9_]+$`|100|
|Waarde|Staat alles behalve<br>`:`(dikke darm)<br>`|`(verticale pijp)<br>Slechts één waarde toegestaan.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Contentlimieten van Knowledge Base
Algemene beperkingen op de inhoud in de kennisbank:
* Lengte van de antwoordtekst: 25.000
* Lengte van de vraagtekst: 1.000
* Lengte van metagegevenssleutel/waardetekst: 100
* Ondersteunde tekens voor metagegevensnaam: alfabetten, cijfers en`_`
* Ondersteunde tekens voor metagegevenswaarde: alles behalve `:` en`|`
* Lengte van de bestandsnaam: 200
* Ondersteunde bestandsindelingen: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Maximum aantal alternatieve vragen: 300
* Maximaal aantal paren voor vraagbeantwoording: is afhankelijk van de **[gekozen azure cognitive search-laag.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** Een vraag- en antwoordpaar kaarten aan een document op Azure Cognitive Search-index.
* URL/HTML-pagina: 1 miljoen tekens

## <a name="create-knowledge-base-call-limits"></a>Limieten voor knowledge base-oproepen maken:
Deze vertegenwoordigen de grenzen voor elk creëren kennisbasis actie; dat wil zeggen, op *KB maken* of de CreateKnowledgeBase API aanroepen.
* Maximum aantal alternatieve vragen per antwoord: 300
* Maximum aantal URL's: 10
* Maximum aantal bestanden: 10

## <a name="update-knowledge-base-call-limits"></a>Limieten voor knowledge base-oproepen bijwerken
Deze vertegenwoordigen de limieten voor elke updateactie; dat wil zeggen, op *Opslaan en trainen* of de UpdateKnowledgeBase API aanroepen.
* Lengte van elke bronnaam: 300
* Maximum aantal alternatieve vragen toegevoegd of verwijderd: 300
* Maximum aantal metagegevensvelden toegevoegd of verwijderd: 10
* Maximum aantal URL's dat kan worden vernieuwd: 5

## <a name="next-steps"></a>Volgende stappen

Meer informatie over wanneer en hoe u [serviceprijsniveaus kunt](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)wijzigen .
