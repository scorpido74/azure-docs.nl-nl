---
title: Wat is Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Met de Azure Cognitive Services-formulier herkenning kunt u sleutel-waardeparen en tabel gegevens uit formulier documenten identificeren en extra heren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 0d78f3cc4f2b12b2d9f45878a0c1b91263112689
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118537"
---
# <a name="what-is-form-recognizer"></a>Wat is Form Recognizer?

Azure Form Recognizer is een cognitieve service die gebruikmaakt van machine learning technologie om tekst, sleutel-waardeparen en tabel gegevens uit formulier documenten te identificeren en op te halen. Het neemt tekst uit formulieren en voert gestructureerde gegevens uit die de relaties in het oorspronkelijke bestand bevatten. U krijgt snel nauw keurige resultaten die zijn afgestemd op uw specifieke inhoud zonder zware hand matige interventie of expertise met uitgebreide gegevens wetenschap. Formulier herkenner bestaat uit aangepaste modellen, het vooraf gedefinieerde ontvangstbewijs model en de lay-Outapi. U kunt modellen voor formulier herkenning aanroepen met behulp van een REST API om de complexiteit te reduceren en te integreren in uw werk stroom of toepassing.

Formulier herkenner bestaat uit de volgende services:
* **Aangepaste modellen** : Extraheer sleutel-waardeparen en tabel gegevens uit formulieren. Deze modellen worden getraind met uw eigen gegevens, zodat ze zijn afgestemd op uw formulieren.
* **Preconstrueerd ontvangstbewijs model** : haalt gegevens op uit de Amerikaanse verkoop kwitanties met behulp van een vooraf gebouwd model.
* **Indelings-API** -tekst-en tabel structuren extra heren, samen met de coördinaten van het selectie kader, van documenten.

<!-- add diagram -->

## <a name="custom-models"></a>Aangepaste modellen

Aangepaste modellen voor formulier herkenning bieden uw eigen gegevens en u hoeft slechts vijf invoer formulieren voor voor beelden te starten. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd die de relaties in het oorspronkelijke formulier document bevatten. Nadat u het model hebt getraind, kunt u het testen en opnieuw trainen en uiteindelijk gebruiken om gegevens te extra heren uit meer formulieren, afhankelijk van uw behoeften.

U hebt de volgende opties wanneer u aangepaste modellen traint: training met gelabelde gegevens en zonder gelabelde gegevens.

### <a name="train-without-labels"></a>Trainen zonder labels

Standaard gebruikt de formulier herkenning onbewaakte lessen om inzicht te krijgen in de indeling en relaties tussen velden en items in uw formulieren. Wanneer u uw invoer formulieren verzendt, verdeelt de algoritme de formulieren op type, detecteert welke sleutels en tabellen aanwezig zijn en koppelt waarden aan sleutels en vermeldingen aan tabellen. Hiervoor zijn geen hand matige gegevens labeling of intensieve code ring en onderhoud vereist. u wordt aangeraden deze methode eerst te proberen.

### <a name="train-with-labels"></a>Trainen met labels

Wanneer u met gelabelde gegevens traint, heeft het model onder meer de aandacht voor het extra heren van belang rijke waarden, met behulp van de gelabelde formulieren die u opgeeft. Dit resulteert in betere uitvoering van modellen en kan modellen produceren die met complexe formulieren of formulieren met waarden zonder sleutels werken.

De [indelings-API](#layout-api) wordt gebruikt voor de lay-out van het formulier om de verwachte grootte en posities van gedrukte en handgeschreven tekst elementen te ontdekken. Vervolgens worden door de gebruiker opgegeven labels gebruikt voor het leren van de sleutel/waarde-koppelingen in de documenten. We raden u aan vijf hand matig gelabelde formulieren van hetzelfde type te gebruiken om aan de slag te gaan wanneer een nieuw model wordt getraind en zo nodig meer gelabelde gegevens toe te voegen om de nauw keurigheid van het model te verbeteren.

## <a name="prebuilt-receipt-model"></a>Gebaseerd ontvangst model

Formulier herkenning bevat ook een model voor het lezen van Engelse verkoop bewijzen van de Verenigde Staten&mdash;het type dat wordt gebruikt door restaurants, stations, detail handel, enzovoort (voor beeld van een[kwitantie](./media/contoso-receipt-small.png)). Dit model extraheert belang rijke informatie zoals de tijd en datum van de trans actie, zakelijke informatie, bedragen van belastingen en totalen en meer. Daarnaast wordt het vooraf gedefinieerde ontvangstbewijs model getraind om alle tekst op een kwitantie te herkennen en te retour neren.

## <a name="layout-api"></a>Indelings-API

Formulier herkenning kan ook tekst-en tabel structuur (de rij-en kolom nummers die zijn gekoppeld aan de tekst) extra heren met high-definition optische teken herkenning (OCR). 

## <a name="get-started"></a>Aan de slag

Volg een Snelstartgids om aan de slag te gaan met het extra heren van gegevens uit uw formulieren. U wordt aangeraden de gratis service te gebruiken wanneer u de technologie bekijkt. Houd er rekening mee dat het aantal beschik bare pagina's beperkt is tot 500 per maand.

* Aangepast: een model trainen op uw formulieren
  * Trainen zonder labels
    * [Snelstartgids: een model voor een formulier herkenning trainen en formulier gegevens extra heren met behulp van de REST API met krul](quickstarts/curl-train-extract.md)
    * [Quick Start: een model voor een formulier herkenning trainen en formulier gegevens extra heren met behulp van de REST API met python](quickstarts/python-train-extract.md)
  * Trainen met labels 
    * [Een model voor het herkennen van een formulier met labels trainen met behulp van het voor beeld-label programma](quickstarts/label-tool.md)
    * [Een model voor het herkennen van een formulier met labels trainen met behulp van REST API en python](quickstarts/python-labeled-data.md) 
* Vooraf gegenereerde bevestigingen: gegevens ophalen uit Amerikaanse verkoop ontvangsten
  * [Snelstartgids: ontvangst gegevens ophalen met behulp van krul](quickstarts/curl-receipts.md)
  * [Snelstartgids: ontvangst gegevens ophalen met behulp van python](quickstarts/python-receipts.md)
* Indeling: tekst-en tabel structuur uit formulieren extra heren
  * [Snelstartgids: indelings gegevens extra heren met behulp van python](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>De REST-Api's controleren

U gebruikt de volgende Api's om modellen te trainen en gestructureerde gegevens uit formulieren op te halen.

|Naam |Beschrijving |
|---|---|
| **Aangepast model trainen**| Train een nieuw model om uw formulieren te analyseren met behulp van vijf soorten formulieren van hetzelfde type. Stel de para meter _useLabelFile_ in op `true` om met hand matig gelabelde gegevens te trainen. |
| **Formulier analyseren** |Analyseer één document dat is door gegeven als een stroom voor het extra heren van tekst, sleutel-waardeparen en tabellen uit het formulier met uw aangepaste model.  |
| **Toename analyseren** |Analyseer één ontvangst document voor het extra heren van belang rijke informatie en andere ontvangst tekst.|
| **Indeling analyseren** |Analyseer de indeling van een formulier om tekst-en tabel structuur op te halen.|

Bekijk de [rest API referentie documentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Wat is er nieuw](./whats-new.md) ? voor meer informatie over recente wijzigingen.

## <a name="input-requirements"></a>Invoer vereisten
### <a name="custom-model"></a>Aangepast model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Gebaseerd ontvangst model

De invoer vereisten voor het ontvangstbewijs model zijn iets anders.

* De indeling moet JPEG, PNG, BMP, PDF (tekst of gescand) of TIFF zijn.
* De bestands grootte moet minder dan 20 MB zijn.
* De afmetingen van afbeeldingen moeten tussen 50 x 50 pixels en 10000 x 10000 pixels liggen. 
* PDF-dimensies mogen Maxi maal 17 x 17 inch zijn, overeenkomen met de papier formaten Legal of a3 en kleiner.
* Voor PDF en TIFF worden alleen de eerste 200 pagina's verwerkt (met een abonnement met een gratis laag, worden alleen de eerste twee pagina's verwerkt).

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Deze service wordt aangeboden als een [Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) van een Azure-service onder de [voor waarden van de online service](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Net als bij alle cognitieve services moeten ontwikkel aars die gebruikmaken van de Form Recognizer-service op de hoogte zijn van het micro soft-beleid voor klant gegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voer een [Snelstartgids](quickstarts/curl-train-extract.md) uit om aan de slag te gaan met de [API voor formulier herkenning](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).
