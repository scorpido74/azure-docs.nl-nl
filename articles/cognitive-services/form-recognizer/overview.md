---
title: Wat is Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Met de Form Recognizer van Azure Cognitive Services kunt u sleutel-waardeparen en tabelgegevens uit formulierdocumenten identificeren en extraheren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 0df61c2ee42d468562efd67a2a66a90a5e4fda53
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723555"
---
# <a name="what-is-form-recognizer"></a>Wat is Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer is een Cognitive Services-dienst die gebruikmaakt van machine learning-technologie om tekst, sleutel-waardeparen en tabelgegevens uit formulierdocumenten te identificeren en te extraheren. Hiermee wordt tekst ingevoerd uit formulieren en gestructureerde gegevens uitgevoerd waarin ook de relaties uit het oorspronkelijke bestand zijn opgenomen. U krijgt snel nauwkeurige resultaten die zijn afgestemd op uw specifieke inhoud zonder dat u veel handmatige ingrepen moet uitvoeren of heel deskundig moet zijn in de gegevenswetenschap. Form Recognizer bestaat uit aangepaste modellen, het vooraf samengesteld ontvangstbewijsmodel en de indelings-API. U kunt Form Recognizer-modellen aanroepen met behulp van een REST API om de complexiteit te reduceren en deze te integreren in uw werkstroom of toepassing.

Form Recognizer bestaat uit de volgende services:
* **Aangepaste modellen**: sleutel-waardeparen en tabelgegevens uit formulieren extraheren. Deze modellen worden getraind met uw eigen gegevens, zodat ze zijn afgestemd op uw formulieren.
* **Vooraf samengestelde modellen**: gegevens ophalen uit unieke formuliertypen met vooraf samengestelde modellen. Op dit moment zijn er vooraf samengestelde modellen voor aankoopbewijzen en visitekaartjes in het Engels.
* **Indelings-API**: hiermee worden tekst- en tabelstructuren uit documenten geëxtraheerd, samen met de bijbehorende begrenzingsvakcoördinaten.

<!-- add diagram -->

## <a name="custom-models"></a>Aangepaste modellen

Aangepaste Form Recognizer-modellen worden getraind naar uw eigen gegevens; u hebt slechts vijf voorbeelden van invoerformulieren nodig om te beginnen. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd waarin ook de relaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

U hebt de volgende opties wanneer u aangepaste modellen traint: training met en zonder gelabelde gegevens.

### <a name="train-without-labels"></a>Trainen zonder labels

Standaard gebruikt Form Recognizer leren zonder supervisie om informatie in te leren over de indeling en relaties tussen velden en items in uw formulieren. Wanneer u uw invoerformulieren verzendt, clustert het algoritme de formulieren op type, detecteert welke sleutels en tabellen aanwezig zijn en koppelt waarden aan sleutels en items aan tabellen. Hiervoor hoeft u niet handmatig gegevens te labelen, of intensief te coderen en onderhouden. U wordt aangeraden deze methode eerst te proberen.

### <a name="train-with-labels"></a>Trainen met labels

Wanneer u met gelabelde gegevens traint, leert het model onder supervisie om nuttige waarden te extraheren met behulp van de gelabelde formulieren die u opgeeft. Dit resulteert in betere presterende modellen en kan modellen produceren die met complexe formulieren of formulieren met waarden zonder sleutels kunnen werken.

De Form Recognizer gebruikt de [indelings-API](#layout-api) voor meer informatie over de verwachte grootten en posities van gedrukte en handgeschreven tekstelementen. Vervolgens worden door de gebruiker opgegeven labels gebruikt voor het leren van de sleutel/waarde-koppelingen in de documenten. We raden u aan vijf handmatig gelabelde formulieren van hetzelfde type te gebruiken om aan de slag te gaan wanneer u een nieuw model traint, en zo nodig meer gelabelde gegevens toe te voegen om de nauwkeurigheid van het model te verbeteren.

## <a name="prebuilt-models"></a>Vooraf gemaakte modellen

Form Recognizer bevat ook vooraf gemaakte modellen voor unieke formuliertypen.
### <a name="prebuilt-receipt-model"></a>Vooraf samengesteld model voor aankoopbewijzen
Het vooraf samengesteld model voor aankoopbewijzen wordt in Australië, Canada, het Verenigd Koninkrijk, India en de Verenigde Staten gebruikt voor het lezen van Engelse aankoopbewijzen&mdash;het type dat wordt gebruikt door restaurants, tankstations, winkels, enzovoort. Dit model haalt belangrijke informatie op, zoals de tijd en datum van de transactie, informatie over de verkoper, btw, regelitems, totalen, enzovoort. Daarnaast wordt het vooraf samengestelde ontvangstbewijsmodel getraind om alle tekst op een ontvangstbewijs te herkennen en te retourneren. 

![voorbeeld van aankoopbewijs](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Vooraf samengesteld model voor visitekaartjes
Het vooraf samengesteld model voor visitekaartjes haalt informatie, zoals naam, functie, adres, e-mail, bedrijf en telefoonnummers van de persoon op uit visitekaartjes in het Engels. 

![voorbeeld van visitekaartje](./media/business-card-english.jpg)

## <a name="layout-api"></a>Indelings-API

Form Recognizer kan ook tekst- en tabelstructuur (de rij- en kolomnummers die zijn gekoppeld aan de tekst) extraheren met optische tekenherkenning (OCR) in high-definition.

## <a name="get-started"></a>Aan de slag

Volg een quickstart om aan de slag te gaan met het extraheren van gegevens uit uw formulieren. U wordt aangeraden de gratis service te gebruiken wanneer u de technologie leert. Houd er rekening mee dat het aantal gratis pagina's beperkt is tot 500 per maand.

* [Quickstart voor clientbibliotheek](./quickstarts/client-library.md) (alle talen, meerdere scenario's)
* Quickstarts voor webinterface
  * [Trainen met labels: voorbeeldhulpprogramma voor labelen](quickstarts/label-tool.md)
* Quickstarts voor REST
  * Aangepaste modellen trainen en formuliergegevens extraheren
    * [Trainen zonder labels - cURL](quickstarts/curl-train-extract.md)
    * [Trainen zonder labels - Python](quickstarts/python-train-extract.md)
    * [Trainen met labels - Python](quickstarts/python-labeled-data.md)
  * Gegevens extraheren uit Amerikaanse aankoopbewijzen
    * [Ontvangstgegevens extraheren - cURL](quickstarts/curl-receipts.md)
    * [Ontvangstgegevens extraheren - Python](quickstarts/python-receipts.md)
  * Tekst- en tabelstructuur extraheren uit formulieren
    * [Indelingsgegevens extraheren - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>De REST API's bekijken

U gebruikt de volgende API's om modellen te trainen en gestructureerde gegevens uit formulieren te extraheren.

|Naam |Beschrijving |
|---|---|
| **Aangepast model trainen**| Train een nieuw model om uw formulieren te analyseren met behulp van vijf formulieren van hetzelfde type. Stel de parameter _useLabelFile_ in op `true` om met handmatig gelabelde gegevens te trainen. |
| **Formulier analyseren** |Analyseer met uw aangepaste model één document dat is doorgegeven als een stroom voor het extraheren van tekst, sleutel-waardeparen en tabellen uit het formulier.  |
| **Ontvangstbewijs analyseren** |Analyseer één ontvangstbewijsdocument voor het extraheren van belangrijke informatie en andere ontvangstbewijstekst.|
| **Indeling analyseren** |Analyseer de indeling van een formulier om tekst- en tabelstructuur te extraheren.|

Lees het [naslagmateriaal bij de REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Nieuwe functies](./whats-new.md) voor meer informatie over recente wijzigingen.

## <a name="input-requirements"></a>Vereisten voor invoer
### <a name="custom-model"></a>Aangepast model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt"></a>Vooraf samengesteld

De invoervereisten voor het ontvangstbewijsmodel zijn iets anders.

* De indeling moet JPEG, PNG, PDF (tekst of gescand) of TIFF zijn.
* Grootte van bestand moet 20 MB of minder zijn.
* De afmetingen van afbeeldingen moeten tussen 50 x 50 en 10000 x 10000 pixels liggen.
* PDF-afmetingen mogen maximaal 17 x 17 inch zijn, wat overeenkomt met de papierformaten Legal of A3 en kleiner.
* Voor PDF en TIFF worden alleen de eerste 200 pagina's verwerkt (met een abonnement in de prijscategorie Gratis worden alleen de eerste twee pagina's verwerkt).

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Form Recognizer-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voer een [quickstart](quickstarts/curl-train-extract.md) uit om aan de slag te gaan met de [Form Recognizer API's](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).