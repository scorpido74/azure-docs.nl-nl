---
title: Wat is Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Met de Azure Form Recognizer-service kunt u sleutel-waardeparen en tabelgegevens in uw formulierdocumenten identificeren en extraheren, en kunt u belangrijke gegevens extraheren uit verkoopbonnen en visitekaartjes.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: geautomatiseerde gegevensverwerking, documentverwerking, geautomatiseerde gegevensinvoer, formulierverwerking
ms.openlocfilehash: 5243c170e1f6b5f647057b8cfafbcac9b2fb4db3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318956"
---
# <a name="what-is-form-recognizer"></a>Wat is Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer is een cognitieve service waarmee u software voor geautomatiseerde gegevensverwerking kunt bouwen met behulp van machine learning-technologie. U kunt de service gebruiken om tekst, sleutel-waardeparen en tabelgegevens identificeren en uit uw formulierdocumenten extraheren&mdash;. Dit levert gestructureerde gegevens op die de relaties in het oorspronkelijke bestand bevatten. U krijgt snel nauwkeurige resultaten die zijn afgestemd op uw specifieke inhoud zonder dat u veel handmatige ingrepen moet uitvoeren of heel deskundig moet zijn in de gegevenswetenschap. Gebruik Form Recognizer om gegevensinvoer in uw toepassingen te automatiseren.

Form Recognizer bestaat uit aangepaste modellen voor documentverwerking, vooraf ontwikkelde modellen voor verkoopbonnen en visitekaartjes en de indelings-API. U kunt Form Recognizer-modellen aanroepen met behulp van een REST API of clientbibliotheek-SDK’s om de complexiteit te reduceren en deze te integreren in uw werkstroom of toepassing.

Form Recognizer bestaat uit de volgende services:
* **[Aangepaste modellen](#custom-models)** : sleutel-waardeparen en tabelgegevens uit formulieren extraheren. Deze modellen worden getraind met uw eigen gegevens, zodat ze zijn afgestemd op uw formulieren.
* **[Vooraf samengestelde modellen](#prebuilt-models)** : gegevens ophalen uit unieke formuliertypen met vooraf samengestelde modellen. Op dit moment zijn er vooraf samengestelde modellen voor aankoopbewijzen en visitekaartjes in het Engels.
* **[Indelings-API](#layout-api)** : hiermee worden tekst- en tabelstructuren uit documenten geëxtraheerd, samen met de bijbehorende begrenzingsvakcoördinaten.

## <a name="custom-models"></a>Aangepaste modellen

Aangepaste Form Recognizer-modellen worden getraind naar uw eigen gegevens; u hebt slechts vijf voorbeelden van invoerformulieren nodig om te beginnen. Met een getraind documentverwerkingsmodel kunnen gestructureerde gegevens worden uitgevoerd waarin ook de relaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

U hebt de volgende opties wanneer u aangepaste modellen traint: training met en zonder gelabelde gegevens.

### <a name="train-without-labels"></a>Trainen zonder labels

Standaard gebruikt Form Recognizer leren zonder supervisie om informatie in te leren over de indeling en relaties tussen velden en items in uw formulieren. Wanneer u uw invoerformulieren verzendt, clustert het algoritme de formulieren op type, detecteert welke sleutels en tabellen aanwezig zijn en koppelt waarden aan sleutels en items aan tabellen. Hiervoor hoeft u niet handmatig gegevens te labelen, of intensief te coderen en onderhouden. U wordt aangeraden deze methode eerst te proberen.

Zie [Een set trainingsgegevens bouwen](./build-training-data-set.md) voor tips over het verzamelen van uw trainingsdocumenten.

### <a name="train-with-labels"></a>Trainen met labels

Wanneer u met gelabelde gegevens traint, leert het model onder supervisie om nuttige waarden te extraheren met behulp van de gelabelde formulieren die u opgeeft. Dit resulteert in betere presterende modellen en kan modellen produceren die met complexe formulieren of formulieren met waarden zonder sleutels kunnen werken.

De Form Recognizer gebruikt de [indelings-API](#layout-api) voor meer informatie over de verwachte grootten en posities van gedrukte en handgeschreven tekstelementen. Vervolgens worden door de gebruiker opgegeven labels gebruikt voor het leren van de sleutel/waarde-koppelingen in de documenten. We raden u aan vijf handmatig gelabelde formulieren van hetzelfde type te gebruiken om aan de slag te gaan wanneer u een nieuw model traint, en zo nodig meer gelabelde gegevens toe te voegen om de nauwkeurigheid van het model te verbeteren.

## <a name="prebuilt-models"></a>Vooraf gemaakte modellen

Form Recognizer bevat ook vooraf ontwikkelde modellen voor geautomatiseerde gegevensverwerking van unieke formuliertypen.

### <a name="prebuilt-receipt-model"></a>Vooraf samengesteld model voor aankoopbewijzen

Het vooraf samengesteld model voor aankoopbewijzen wordt in Australië, Canada, het Verenigd Koninkrijk, India en de Verenigde Staten gebruikt voor het lezen van Engelse aankoopbewijzen&mdash;het type dat wordt gebruikt door restaurants, tankstations, winkels, enzovoort. Dit model haalt belangrijke informatie op, zoals de tijd en datum van de transactie, informatie over de verkoper, btw, regelitems, totalen, enzovoort. Daarnaast wordt het vooraf samengestelde ontvangstbewijsmodel getraind om alle tekst op een ontvangstbewijs te herkennen en te retourneren. Raadpleeg de conceptuele handleiding voor [Verkoopbonnen](./concept-receipts.md) voor meer informatie.

![voorbeeld van aankoopbewijs](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Vooraf samengesteld model voor visitekaartjes

Het vooraf samengesteld model voor visitekaartjes haalt informatie, zoals naam, functie, adres, e-mail, bedrijf en telefoonnummers van de persoon op uit visitekaartjes in het Engels. Raadpleeg de conceptuele handleiding voor [Visitekaartjes](./concept-business-cards.md) voor meer informatie.

![voorbeeld van visitekaartje](./media/business-card-english.jpg)

## <a name="layout-api"></a>Indelings-API

Form Recognizer kan ook tekst- en tabelstructuur (de rij- en kolomnummers die zijn gekoppeld aan de tekst) extraheren met optische tekenherkenning (OCR) in high-definition.

## <a name="get-started"></a>Aan de slag

Volg een quickstart om aan de slag te gaan met het extraheren van gegevens uit uw formulieren. U wordt aangeraden de gratis service te gebruiken wanneer u de technologie leert. Houd er rekening mee dat het aantal gratis pagina's beperkt is tot 500 per maand.

* [Quickstarts voor clientbibliotheek](./quickstarts/client-library.md) (alle talen, meerdere scenario's)
* Quickstarts voor webinterface
  * [Trainen met labels: voorbeeldhulpprogramma voor labelen](quickstarts/label-tool.md)
* Quickstarts voor REST
  * Aangepaste modellen trainen en formuliergegevens extraheren
    * [Trainen zonder labels - cURL](quickstarts/curl-train-extract.md)
    * [Trainen zonder labels - Python](quickstarts/python-train-extract.md)
    * [Trainen met labels - Python](quickstarts/python-labeled-data.md)
  * Gegevens extraheren uit aankoopbewijzen
    * [Ontvangstgegevens extraheren - cURL](quickstarts/curl-receipts.md)
    * [Ontvangstgegevens extraheren - Python](quickstarts/python-receipts.md)
  * Gegevens extraheren uit visitekaartjes
    * [Gegevens extraheren uit visitekaartjes - Python](quickstarts/python-business-cards.md)
  * Tekst- en tabelstructuur extraheren uit formulieren
    * [Indelingsgegevens extraheren - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>De REST API's bekijken

U gebruikt de volgende API's om modellen te trainen en gestructureerde gegevens uit formulieren te extraheren.

|Naam |Beschrijving |
|---|---|
| **Aangepast model trainen**| Train een nieuw model om uw formulieren te analyseren met behulp van vijf formulieren van hetzelfde type. Stel de parameter _useLabelFile_ in op `true` om met handmatig gelabelde gegevens te trainen. |
| **Formulier analyseren** |Analyseer met uw aangepaste model één document dat is doorgegeven als een stroom voor het extraheren van tekst, sleutel-waardeparen en tabellen uit het formulier.  |
| **Ontvangstbewijs analyseren** |Analyseer één ontvangstbewijsdocument voor het extraheren van belangrijke informatie en andere ontvangstbewijstekst.|
| **Visitekaartjes analyseren** |Analyseer een visitekaartje om belangrijke informatie en tekst te extraheren.|
| **Indeling analyseren** |Analyseer de indeling van een formulier om tekst- en tabelstructuur te extraheren.|

Lees het [naslagmateriaal bij de REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Nieuwe functies](./whats-new.md) voor meer informatie over recente wijzigingen.

## <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Form Recognizer-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voltooi een [quickstart voor de clientbibliotheek](quickstarts/client-library.md) om aan de slag te gaan met het schrijven van een formulierverwerkingsapp met een Form Recognizer in de taal van uw keuze.