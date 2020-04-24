---
title: Woorden lijst-LUIS
titleSuffix: Azure Cognitive Services
description: In de woorden lijst worden de termen beschreven die u kunt tegen komen tijdens het werken met de LUIS API-service.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: f764b0d42e08e68b45d49a5aae8542f05707ccde
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099356"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Taal informatie over woorden lijst met veelgestelde woorden en concepten
In de verklarende woorden lijst voor Language Understanding (LUIS) worden de termen beschreven die u kunt tegen komen tijdens het werken met de LUIS API-service.

## <a name="active-version"></a><a name="active-version"></a>Actieve versie

De actieve LUIS-versie is de versie die wijzigingen in het model ontvangt. Als u in de [Luis](luis-reference-regions.md) -Portal wijzigingen wilt aanbrengen in een versie die niet de actieve versie is, moet u deze versie eerst instellen als actief.

## <a name="authoring"></a><a name="authoring"></a>Ontwerpen

Ontwerpen is de mogelijkheid om een [Luis-app](#luis-app)te maken, te beheren en te implementeren met behulp van de [Luis](luis-reference-regions.md) -portal of de [ontwerp-api's](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a><a name="authoring-key"></a>Sleutel ontwerpen

Eerder benoemde programma code. Wordt gebruikt om de app te schrijven. Niet gebruikt voor eindpunt query's op productie niveau. Zie [sleutel limieten](luis-limits.md#key-limits)voor meer informatie.

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>JSON-bestand voor batch-test

Batch tests is de mogelijkheid om een huidige model van de LUIS-app te valideren met een consistente en bekende testset van gebruikers uitingen. De batch test is gedefinieerd in een [JSON-bestand](luis-concept-batch-test.md#batch-file-format).

Zie ook:
* [Concepten](luis-concept-batch-test.md)
* [Uitleg](luis-how-to-batch-test.md)
* [Zelfstudie](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Samenwerker

Een samen werker/bijdrager is niet de [eigenaar](#owner) van de app, maar heeft dezelfde machtigingen om de intenties, entiteiten en uitingen toe te voegen, te bewerken en te verwijderen.

## <a name="contributor"></a><a name="contributor"></a>Inzender

Een mede werker is hetzelfde als een [samen werker](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Descriptor

Een descriptor is een [functie](#features) die wordt toegepast op een model tijdens de trainings tijd, inclusief [woordgroepen lijsten](#phrase-list) en [entiteiten](#entity).

## <a name="domain"></a><a name="domain"></a>Domain

In de context van LUIS is een **domein** een kennis gebied. Uw domein is specifiek voor uw app-gedeelte van kennis. Dit kan een algemeen gebied zijn, zoals de app voor de reis agent. Een reis agent-app kan ook specifiek zijn voor de informatie gebieden voor uw bedrijf, zoals specifieke geografische locaties, talen en services.

## <a name="endpoint"></a><a name="endpoint"></a>Endpoints

Met de [Luis-eind punt](https://go.microsoft.com/fwlink/?linkid=2092356) -URL kunt u Luis-query's verzenden nadat de [Luis-app](#luis-app) is gemaakt en gepubliceerd. De eind punt-URL bevat de regio van de gepubliceerde app en de App-ID. U kunt het eind punt vinden op de pagina **[sleutels en eind punten](luis-how-to-azure-subscription.md)** van uw app, of u kunt de eind punt-URL ophalen uit de informatie-API voor het [ophalen van apps](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

## <a name="entity"></a><a name="entity"></a>Entiteit

[Entiteiten](luis-concept-entity-types.md) zijn belang rijke woorden in [uitingen](luis-concept-utterance.md) die informatie beschrijven die relevant is voor de [intentie](luis-concept-intent.md), en soms van belang zijn. Een entiteit is in feite een Data type in LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>F-meting

Bij het [testen van batches](luis-interactive-test.md#batch-testing)wordt de nauw keurigheid van de test gemeten.

## <a name="false-negative-fn"></a><a name="false-negative"></a>ONWAAR negatief (FN)

In [batch tests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevens punten uitingen waarin de afwezigheid van het doel intentie/entiteit onjuist is voor speld door uw app.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Onjuist positief (FP)

In [batch tests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevens punten uitingen waarin de app het bestaan van de doel intentie/entiteit onjuist heeft voor speld.

## <a name="features"></a><a name="features"></a>Functies

In machine learning is een [functie](luis-concept-feature.md) een onderscheiding van eigenschappen of kenmerken van gegevens die uw systeem observeert.

## <a name="intent"></a><a name="intent"></a>Intentie

Een [intentie](luis-concept-intent.md) vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel of doel dat wordt weer gegeven in de invoer van een gebruiker, zoals het reserveren van een vlucht, het betalen van een factuur of het vinden van een nieuws artikel. In LUIS is de intentie voorspelling gebaseerd op de volledige utterance. Entiteiten, op vergelijking, zijn stuks van een utterance.

## <a name="labeling"></a><a name="labeling"></a>Labels

Labelen of markeren is het proces van het koppelen van een woord of woord groep in een [utterance](#utterance) met een [entiteit](#entity) (data type).

## <a name="luis-app"></a><a name="luis-app"></a>LUIS-app

Een LUIS-app is een verzameling taal modellen voor de verwerking van natuurlijke taal, waaronder [intenties](#intent), [entiteiten](#entity)en gelabelde [uitingen](#utterance).

## <a name="owner"></a><a name="owner"></a>Eigenaar

Elke app heeft één eigenaar die de app heeft gemaakt. De eigenaar kan deel [nemers](#collaborator)toevoegen.

## <a name="patterns"></a><a name="pattern"></a>Patronen
De vorige patroon functie wordt vervangen door [patronen](luis-concept-patterns.md). Gebruik patronen om de nauw keurigheid van de voor spelling te verbeteren door minder trainings voorbeelden te bieden.

## <a name="phrase-list"></a><a name="phrase-list"></a>Woordgroepen lijst

Een [woordgroepen lijst](luis-concept-feature.md) bevat een groep waarden (woorden of zinsdelen) die deel uitmaken van dezelfde klasse en moeten op dezelfde manier worden behandeld (bijvoorbeeld namen van steden of producten). Een lijst die kan worden gewijzigd, wordt beschouwd als synoniemen.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Vooraf gebouwd domein

Een [vooraf gebouwd domein](luis-how-to-use-prebuilt-domains.md) is een Luis-app die is geconfigureerd voor een specifiek domein, zoals Home Automation (HomeAutomation) of restaurant reserveringen (RestaurantReservation). De intenties, uitingen en entiteiten zijn geconfigureerd voor dit domein.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Vooraf gebouwde entiteit

Een [vooraf samengestelde entiteit](luis-prebuilt-entities.md) is een entiteit Luis biedt algemene typen informatie, zoals Number, URL en e-mail. U kiest ervoor een vooraf gemaakte entiteit toe te voegen aan uw toepassing.

## <a name="precision"></a><a name="precision"></a>Nauwkeurigheid
In [batch testen](luis-interactive-test.md#batch-testing)is precisie (ook wel positieve Voorspellings waarde genoemd) de Fractie van relevante uitingen van de opgehaalde uitingen.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Programmatische sleutel

De naam van de [ontwerp sleutel](#authoring-key)is gewijzigd.

## <a name="publish"></a><a name="publish"></a>Publiceren

Publiceren houdt in dat er een LUIS actieve versie beschikbaar is op het staging-of productie- [eind punt](#endpoint).

## <a name="quota"></a><a name="quota"></a>Quotum

LUIS quota is de beperking van de [Azure-abonnementweergave](https://aka.ms/luis-price-tier). Het quotum voor de LUIS kan worden beperkt door beide aanvragen per seconde (HTTP-status 429) en in totaal aantal aanvragen in een maand (HTTP-status 403).

## <a name="recall"></a><a name="recall"></a>Halen
In [batch testen](luis-interactive-test.md#batch-testing), intrekken (ook wel gevoeligheid genoemd), is de mogelijkheid om Luis te generaliseren.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Semantische woorden lijst
Er wordt een semantisch woorden lijst op de pagina lijst entiteit weer gegeven, evenals de lijst pagina woordgroepen. De semantische woorden lijst bevat suggesties van woorden op basis van het huidige bereik.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Sentimentanalyse
Sentiment analyse biedt positieve of negatieve waarden van de uitingen die worden geleverd door [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Spraak gebeuren

Met Speech gebeuren kan uw spraak service worden geprimeeerd met uw LUIS-model.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Spelling correctie

Schakel de Bing spell checker in om foutief gespelde woorden in het uitingen te corrigeren vóór de voor spelling.

## <a name="starter-key"></a><a name="starter-key"></a>Start sleutel

Een gratis sleutel die moet worden gebruikt voor het eerst op basis van LUIS.

## <a name="structure"></a><a name="structure"></a>Structuur

Voeg structuur toe aan een door de machine geleerde entiteit om subonderdelen met descriptoren (onderdelen) en beperkingen (reguliere expressies of lijst entiteiten) te bieden.

## <a name="subscription-key"></a><a name="subscription-key"></a>Abonnementssleutel

De abonnements sleutel is de **Voorspellings eindpunt** sleutel die is gekoppeld aan de Luis-service die [u in azure hebt gemaakt](luis-how-to-azure-subscription.md). Deze sleutel is niet de [ontwerp sleutel](#programmatic-key). Als u een eindpunt sleutel hebt, moet deze worden gebruikt voor alle eindpunt aanvragen in plaats van de ontwerp sleutel. U kunt de huidige eindpunt sleutel weer geven in de eind punt-URL onder aan de [pagina **sleutels en eind punten** ](luis-how-to-azure-subscription.md) op de website van [Luis](luis-reference-regions.md) . Het is de waarde van de **abonnements sleutel** naam/waarde-paar.

## <a name="test"></a><a name="test"></a>Testen

Als u een LUIS-app [test](luis-interactive-test.md#test-your-app) , wordt een utterance door gegeven aan Luis en worden de JSON-resultaten weer gegeven.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Verschuiving van tijd zone

Het eind punt bevat time zone offset. Dit is het aantal minuten dat u wilt toevoegen aan of verwijderen uit de datetimeV2-vooraf gedefinieerde entiteit. Als de utterance bijvoorbeeld ' wat is de tijd is? ' is, is de geretourneerde datetimeV2 de huidige tijd voor de client aanvraag. Als uw client aanvraag afkomstig is van een bot of een andere toepassing die niet dezelfde is als de gebruiker van uw bot, moet u de verschuiving tussen de bot en de gebruiker door geven.

Zie de [tijd zone van de vooraf gemaakte datetimeV2-entiteit wijzigen](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Token
Een token is de kleinste eenheid die in een entiteit kan worden gelabeld. Tokeniseren is gebaseerd op de [cultuur](luis-language-support.md#tokenization)van de toepassing.

## <a name="train"></a><a name="train"></a>Trainen

Training is het proces van het onderwijs van LUIS over eventuele wijzigingen in de actieve versie sinds de laatste training.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Waar negatief (TN)

In [batch tests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevens punten uitingen waarin uw app de afwezigheid van het doel intentie/entiteit op de juiste wijze heeft voor speld.

## <a name="true-positive-tp"></a><a name="true-positive"></a>True positief (TP)

In [batch tests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevens punten uitingen waarin uw app het bestaan van de doel intentie/entiteit op de juiste wijze heeft voor speld.

## <a name="utterance"></a><a name="utterance"></a>Utterance

Een utterance is een term in natuurlijke taal, zoals ' Book 2-tickets naar Seattle Next dinsdag '. Voor beelden van uitingen worden toegevoegd aan de bedoeling.

## <a name="version"></a><a name="version"></a>Versie

Een LUIS- [versie](luis-how-to-manage-versions.md) is een specifiek gegevens model dat is gekoppeld aan een Luis-app-id en het gepubliceerde eind punt. Elke LUIS-app heeft ten minste één versie.
