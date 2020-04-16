---
title: Wat is Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Met de Azure Cognitive Services Form Recognizer u sleutel-/waardeparen en tabelgegevens uit formulierdocumenten identificeren en extraheren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6919849d28573ad7388a7f2e317d2b8433f35559
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399454"
---
# <a name="what-is-form-recognizer"></a>Wat is Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer is een cognitieve service die machine learning-technologie gebruikt om tekst, sleutel-/waardeparen en tabelgegevens uit formulierdocumenten te identificeren en te extraheren. Het neemt tekst in van formulieren en levert gestructureerde gegevens op die de relaties in het oorspronkelijke bestand bevatten. U krijgt snel nauwkeurige resultaten die zijn afgestemd op uw specifieke inhoud zonder zware handmatige interventie of uitgebreide expertise op het gebied van data science. Form Recognizer bestaat uit aangepaste modellen, het vooraf gebouwde ontvangstmodel en de lay-out-API. U Form Recognizer-modellen aanroepen met behulp van een REST API om de complexiteit te verminderen en deze te integreren in uw workflow of toepassing.

Form Recognizer bestaat uit de volgende services:
* **Aangepaste modellen** - Haal sleutel-/waardeparen en tabelgegevens uit formulieren. Deze modellen zijn getraind met uw eigen gegevens, zodat ze zijn afgestemd op uw formulieren.
* **Voorgebouwd ontvangstmodel** - Haal gegevens uit de verkoopbevestigingen van de VS met behulp van een voorgebouwd model.
* **Lay-out-API** - Tekst- en tabelstructuren, samen met de coördinaten van het selectiekader, uit documenten halen.

<!-- add diagram -->

## <a name="custom-models"></a>Aangepaste modellen

Aangepaste modellen van Form Recognizer trainen naar uw eigen gegevens en u hebt slechts vijf voorbeeldinvoerformulieren nodig om te starten. Een getraind model kan gestructureerde gegevens uitproduceren die de relaties in het oorspronkelijke formulierdocument bevatten. Nadat u het model hebt getraind, u het testen en omscholen en uiteindelijk gebruiken om gegevens op betrouwbare wijze uit meer formulieren te halen volgens uw behoeften.

U hebt de volgende opties wanneer u aangepaste modellen traint: training met gelabelde gegevens en zonder gelabelde gegevens.

### <a name="train-without-labels"></a>Trein zonder labels

Formulierherkenning gebruikt standaard leren zonder toezicht om inzicht te krijgen in de lay-out en relaties tussen velden en vermeldingen in uw formulieren. Wanneer u uw invoerformulieren indient, groepeert het algoritme de formulieren op type, detecteert welke toetsen en tabellen aanwezig zijn en koppelt het waarden aan sleutels en vermeldingen aan tabellen. Dit vereist geen handmatige gegevensetikettering of intensieve codering en onderhoud, en we raden u aan deze methode eerst te proberen.

### <a name="train-with-labels"></a>Trainen met labels

Wanneer u traint met gelabelde gegevens, leert het model onder toezicht om waarden van belang te extraheren, met behulp van de gelabelde formulieren die u verstrekt. Dit resulteert in beter presterende modellen en kan modellen produceren die werken met complexe formulieren of formulieren die waarden zonder sleutels bevatten.

Form Recognizer gebruikt de [Layout API](#layout-api) om de verwachte groottes en posities van gedrukte en handgeschreven tekstelementen te leren. Vervolgens gebruikt het door de gebruiker opgegeven labels om de sleutel-/waardekoppelingen in de documenten te leren. We raden u aan vijf handmatig gelabelde formulieren van hetzelfde type te gebruiken om aan de slag te gaan bij het trainen van een nieuw model en meer gelabelde gegevens toe te voegen als dat nodig is om de nauwkeurigheid van het model te verbeteren.

## <a name="prebuilt-receipt-model"></a>Vooraf gebouwd ontvangstmodel

Form Recognizer bevat ook een model voor het&mdash;lezen van Engelse verkoopontvangsten uit de Verenigde Staten het type dat wordt gebruikt door restaurants, benzinestations, detailhandel, enzovoort[(voorbeeld ontvangst).](./media/contoso-receipt-small.png) Dit model haalt belangrijke informatie, zoals de tijd en datum van de transactie, merchant informatie, bedragen van belastingen en totalen en nog veel meer. Bovendien is het vooraf gebouwde ontvangstmodel getraind om alle tekst op een ontvangstbewijs te herkennen en terug te geven.

## <a name="layout-api"></a>Indeling-API

Form Recognizer kan ook tekst- en tabelstructuur (de rij- en kolomnummers die aan de tekst zijn gekoppeld) extraheren met behulp van ocr (high-definition optical character recognition).

## <a name="get-started"></a>Aan de slag

Volg een snelle start om aan de slag te gaan met het extraheren van gegevens uit uw formulieren. We raden u aan de gratis service te gebruiken wanneer u de technologie leert. Vergeet niet dat het aantal gratis pagina's is beperkt tot 500 per maand.

* Aangepast - train een model naar uw formulieren
  * Trein zonder labels
    * [Snelstart: train een Form Recognizer-model en vernieuw formuliergegevens met behulp van de REST API met cURL](quickstarts/curl-train-extract.md)
    * [Snelstart: train een Form Recognizer-model en vernieuw formuliergegevens met behulp van de REST API met Python](quickstarts/python-train-extract.md)
  * Trainen met labels
    * [Een formulierherkenningsmodel trainen met labels met het voorbeeldlabelgereedschap](quickstarts/label-tool.md)
    * [Een formulierherkenningsmodel trainen met labels met REST API en Python](quickstarts/python-labeled-data.md)
* Vooraf gebouwde ontvangsten - uittreksel gegevens uit de VS verkoopontvangsten
  * [Snelstart: ontvangstgegevens extraheren met behulp van cURL](quickstarts/curl-receipts.md)
  * [Snelstart: ontvangstgegevens extraheren met Python](quickstarts/python-receipts.md)
* Indeling - tekst en tabelstructuur uit formulieren extraheren
  * [Snelstart: lay-outgegevens extraheren met Python](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>Bekijk de REST API's

U gebruikt de volgende API's om modellen te trainen en gestructureerde gegevens uit formulieren te extraheren.

|Naam |Beschrijving |
|---|---|
| **Aangepaste treinmodel**| Train een nieuw model om uw formulieren te analyseren met behulp van vijf vormen van hetzelfde type. Stel de parameter _useLabelFile_ in om `true` te trainen met handmatig gelabelde gegevens. |
| **Formulier analyseren** |Analyseer één document dat als stroom wordt doorgegeven om tekst, sleutel/waardeparen en tabellen uit het formulier met uw aangepaste model te extraheren.  |
| **Ontvangst bewijs analyseren** |Analyseer één ontvangstbewijsdocument om belangrijke informatie en andere ontvangsttekst te extraheren.|
| **Indeling analyseren** |Analyseer de lay-out van een formulier om tekst en tabelstructuur te extraheren.|

Bekijk de [referentiedocumentatie van](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) de REST API voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Wat is er nieuw](./whats-new.md) voor meer informatie over recente wijzigingen.

## <a name="input-requirements"></a>Invoervereisten
### <a name="custom-model"></a>Aangepast model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Vooraf gebouwd ontvangstmodel

De invoervereisten voor het ontvangstmodel zijn iets anders.

* De indeling moet JPEG, PNG, BMP, PDF (tekst of gescand) of TIFF zijn.
* De bestandsgrootte moet minder dan 20 MB bedragen.
* De afmetingen van de afbeelding moeten tussen de 50 x 50 pixels en 10000 x 10000 pixels liggen.
* PDF-afmetingen moeten maximaal 17 x 17 inch zijn, overeenkomend met juridische of A3-papierformaten en kleiner.
* Voor PDF en TIFF worden alleen de eerste 200 pagina's verwerkt (met een gratis laagabonnement worden alleen de eerste twee pagina's verwerkt).

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Deze service wordt aangeboden als [een voorbeeld](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) van een Azure-service onder de [onlineservicevoorwaarden](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Zoals met alle cognitieve services, moeten ontwikkelaars die de Form Recognizer-service gebruiken, op de hoogte zijn van het Microsoft-beleid voor klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voltooi een [snelle start](quickstarts/curl-train-extract.md) om aan de slag te gaan met de [FORM Recognizer API's](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).
