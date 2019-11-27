---
title: Woorden lijst-LUIS
titleSuffix: Azure Cognitive Services
description: De verklarende woordenlijst verklaart de termen die u kunt tegenkomen wanneer u met de LUIS-API-Service werkt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325950"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Taal informatie over woorden lijst met veelgestelde woorden en concepten
De Language Understanding (LUIS) verklarende woordenlijst wordt uitgelegd dat de voorwaarden die u kunt tegenkomen wanneer u met de LUIS-API-Service werkt.

## <a name="active-version"></a>Actieve versie

De actieve LUIS-versie is de versie die wijzigingen in het model ontvangt. Als u in de [Luis](luis-reference-regions.md) -Portal wijzigingen wilt aanbrengen in een versie die niet de actieve versie is, moet u deze versie eerst instellen als actief.

## <a name="authoring"></a>Ontwerp

Ontwerpen is de mogelijkheid om een [Luis-app](#luis-app)te maken, te beheren en te implementeren met behulp van de [Luis](luis-reference-regions.md) -portal of de [ontwerp-api's](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Sleutel ontwerpen

Eerder met de naam "Programmatic" sleutel. Gebruikt voor het maken van de app. Niet gebruikt voor productie-niveau eindpunt query's. Zie [sleutel limieten](luis-boundaries.md#key-limits)voor meer informatie.

## <a name="batch-test-json-file"></a>JSON-bestand voor batch-test

Batch tests is de mogelijkheid om een huidige model van de LUIS-app te valideren met een consistente en bekende testset van gebruikers uitingen. De batch test is gedefinieerd in een [JSON-bestand](luis-concept-batch-test.md#batch-file-format).

Zie ook:
* [Concepten](luis-concept-batch-test.md)
* [Instructies](luis-how-to-batch-test.md)
* [Zelfstudie](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a>Samen werker

Een samen werker/bijdrager is niet de [eigenaar](#owner) van de app, maar heeft dezelfde machtigingen om de intenties, entiteiten en uitingen toe te voegen, te bewerken en te verwijderen.

## <a name="contributor"></a>Groep

Een mede werker is hetzelfde als een [samen werker](#collaborator).

## <a name="descriptor"></a>Descriptor

Een descriptor is een [functie](#features) die wordt toegepast op een model tijdens de trainings tijd, inclusief [woordgroepen lijsten](#phrase-list) en [entiteiten](#entity). 

## <a name="domain"></a>Domeinen

In de context van LUIS is een **domein** een kennis gebied. Uw domein is specifiek voor uw app-gebied van kennis. Dit is een algemeen gebied, zoals de agent van de reis-app. Een agent van de reis-app kan ook zijn specifiek voor alleen de delen van gegevens voor uw bedrijf, zoals specifieke geografische locaties, talen en services.

## <a name="endpoint"></a>Endpoints

Met de [Luis-eind punt](https://go.microsoft.com/fwlink/?linkid=2092356) -URL kunt u Luis-query's verzenden nadat de [Luis-app](#luis-app) is gemaakt en gepubliceerd. De eindpunt-URL bevat de regio van de gepubliceerde app, evenals de app-ID. U kunt het eind punt vinden op de pagina **[sleutels en eind punten](luis-how-to-azure-subscription.md)** van uw app, of u kunt de eind punt-URL ophalen uit de informatie-API voor het [ophalen van apps](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

## <a name="entity"></a>Vennootschap

[Entiteiten](luis-concept-entity-types.md) zijn belang rijke woorden in [uitingen](luis-concept-utterance.md) die informatie beschrijven die relevant is voor de [intentie](luis-concept-intent.md), en soms van belang zijn. Een entiteit is in feite een gegevenstype in LUIS.

## <a name="f-measure"></a>F-meting

Bij het [testen van batches](luis-interactive-test.md#batch-testing)wordt de nauw keurigheid van de test gemeten.

## <a name="false-negative"></a>ONWAAR negatief (FN)

In [batch tests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevens punten uitingen waarin de afwezigheid van het doel intentie/entiteit onjuist is voor speld door uw app.

## <a name="false-positive"></a>Onjuist positief (FP)

In [batch tests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevens punten uitingen waarin de app het bestaan van de doel intentie/entiteit onjuist heeft voor speld.

## <a name="features"></a>Functies

In machine learning is een [functie](luis-concept-feature.md) een onderscheiding van eigenschappen of kenmerken van gegevens die uw systeem observeert.

## <a name="intent"></a>Bedoeling

Een [intentie](luis-concept-intent.md) vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel of het doel, uitgedrukt in de invoer van een gebruiker, zoals een vlucht reserveren, een factuur betaalt of zoeken naar een nieuwsartikel. De intentie voorspelling is in LUIS gebaseerd op de hele utterance. Entiteiten zijn ter vergelijking: onderdelen van een utterance.

## <a name="labeling"></a>Labels

Labelen of markeren is het proces van het koppelen van een woord of woord groep in een [utterance](#utterance) met een [entiteit](#entity) (data type).

## <a name="luis-app"></a>LUIS-app

Een LUIS-app is een verzameling taal modellen voor de verwerking van natuurlijke taal, waaronder [intenties](#intent), [entiteiten](#entity)en gelabelde [uitingen](#utterance).

## <a name="owner"></a>Bent

Elke app heeft een eigenaar op die de persoon die de app hebt gemaakt. De eigenaar kan deel [nemers](#collaborator)toevoegen.

## <a name="pattern"></a>Daarin
De vorige patroon functie wordt vervangen door [patronen](luis-concept-patterns.md). Patronen voor het verbeteren van nauwkeurigheid door minder training voorbeelden gebruiken.

## <a name="phrase-list"></a>Woordgroepen lijst

Een [woordgroepen lijst](luis-concept-feature.md) bevat een groep waarden (woorden of zinsdelen) die deel uitmaken van dezelfde klasse en moeten op dezelfde manier worden behandeld (bijvoorbeeld namen van steden of producten). Een lijst met uitwisselbaar wordt beschouwd als synoniemen.

## <a name="prebuilt-domains"></a>Vooraf gebouwd domein

Een [vooraf gebouwd domein](luis-how-to-use-prebuilt-domains.md) is een Luis-app die is geconfigureerd voor een specifiek domein, zoals Home Automation (HomeAutomation) of restaurant reserveringen (RestaurantReservation). De intenties, uitingen en entiteiten zijn geconfigureerd voor dit domein.

## <a name="prebuilt-entity"></a>Vooraf gebouwde entiteit

Een [vooraf samengestelde entiteit](luis-prebuilt-entities.md) is een entiteit Luis biedt algemene typen informatie, zoals Number, URL en e-mail. U wilt een vooraf gedefinieerde entiteit toevoegen aan uw toepassing.

## <a name="precision"></a>Nauwkeurigheid
In [batch testen](luis-interactive-test.md#batch-testing)is precisie (ook wel positieve Voorspellings waarde genoemd) de Fractie van relevante uitingen van de opgehaalde uitingen.

## <a name="programmatic-key"></a>Programmatische sleutel

De naam van de [ontwerp sleutel](#authoring-key)is gewijzigd.

## <a name="publish"></a>Gepubliceerd

Publiceren houdt in dat er een LUIS actieve versie beschikbaar is op het staging-of productie- [eind punt](#endpoint).  

## <a name="quota"></a>Overschreden

LUIS quota is de beperking van de [Azure-abonnementweergave](https://aka.ms/luis-price-tier). Het quotum LUIS kan worden beperkt door beide aanvragen per seconde (http-Status 429) en het totale aantal aanvragen in een maand (http-Status 403).

## <a name="recall"></a>Halen
In [batch testen](luis-interactive-test.md#batch-testing), intrekken (ook wel gevoeligheid genoemd), is de mogelijkheid om Luis te generaliseren.

## <a name="semantic-dictionary"></a>Semantische woorden lijst
Een semantische woordenlijst is beschikbaar op de pagina van de entiteit lijst, evenals de pagina van de lijst met woorden. De functionaliteit voor semantische woordenlijst bevat suggesties van woorden op basis van het huidige bereik.

## <a name="sentiment-analysis"></a>Sentimentanalyse
Sentiment analyse biedt positieve of negatieve waarden van de uitingen die worden geleverd door [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Spraak gebeuren

Spraak voorbereiden kan uw spraakherkenning service gereed worden gemaakt met uw LUIS-model.

## <a name="spelling-correction"></a>Spelling correctie

Schakel de spellingcontrole van Bing om te corrigeren van verkeerd gespelde woorden in de uitingen voordat voorspelling.

## <a name="starter-key"></a>Start sleutel

Een gratis sleutel die moet worden gebruikt voor het eerst op basis van LUIS.

## <a name="structure"></a>Structuur

Voeg structuur toe aan een door de machine geleerde entiteit om subonderdelen met descriptoren (onderdelen) en beperkingen (reguliere expressies of lijst entiteiten) te bieden.

## <a name="subscription-key"></a>Abonnements sleutel

De abonnements sleutel is de **Voorspellings eindpunt** sleutel die is gekoppeld aan de Luis-service die [u in azure hebt gemaakt](luis-how-to-azure-subscription.md). Deze sleutel is niet de [ontwerp sleutel](#programmatic-key). Als u een eindpuntsleutel hebt, moet het worden gebruikt voor een eindpunt-aanvragen in plaats van de sleutel van de ontwerphandleiding. U kunt de huidige eindpunt sleutel weer geven in de eind punt-URL onder aan de [pagina **sleutels en eind punten** ](luis-how-to-azure-subscription.md) op de website van [Luis](luis-reference-regions.md) . Het is de waarde van de **abonnements sleutel** naam/waarde-paar.

## <a name="test"></a>Proeven

Als u een LUIS-app [test](luis-interactive-test.md#test-your-app) , wordt een utterance door gegeven aan Luis en worden de JSON-resultaten weer gegeven.

## <a name="timezoneoffset"></a>Verschuiving van tijd zone

Het eindpunt bevat timezoneOffset. Dit is het aantal minuten dat u wilt toevoegen of verwijderen uit de datetimeV2 vooraf gedefinieerde entiteit. Bijvoorbeeld, als de utterance is 'welk moment is het nu?', de datetimeV2 geretourneerd de huidige tijd voor de clientaanvraag. Als uw clientaanvraag afkomstig is van een bot of andere toepassingen die niet is hetzelfde als de gebruiker van uw bot, moet u tussen de bot en de gebruiker in de verschuiving doorgeven.

Zie de [tijd zone van de vooraf gemaakte datetimeV2-entiteit wijzigen](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Een token is de kleinste eenheid die kan worden voorzien in een entiteit. Tokeniseren is gebaseerd op de [cultuur](luis-language-support.md#tokenization)van de toepassing.

## <a name="train"></a>Leerling

Training is het proces van het onderwijs van LUIS over eventuele wijzigingen in de actieve versie sinds de laatste training.

## <a name="true-negative"></a>Waar negatief (TN)

In [batch tests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevens punten uitingen waarin uw app de afwezigheid van het doel intentie/entiteit op de juiste wijze heeft voor speld.

## <a name="true-positive"></a>True positief (TP)

In [batch tests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevens punten uitingen waarin uw app het bestaan van de doel intentie/entiteit op de juiste wijze heeft voor speld.

## <a name="utterance"></a>Utterance

Een utterance is een woordgroep natuurlijke taal, zoals 'book 2 tickets naar de volgende dinsdag Seattle'. Voorbeeld-uitingen worden toegevoegd aan het doel.

## <a name="version"></a>Versie

Een LUIS- [versie](luis-how-to-manage-versions.md) is een specifiek gegevens model dat is gekoppeld aan een Luis-app-id en het gepubliceerde eind punt. Elke LUIS-app bestaat uit ten minste één versie.
