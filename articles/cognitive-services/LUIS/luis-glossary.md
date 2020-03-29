---
title: Woordenlijst - LUIS
titleSuffix: Azure Cognitive Services
description: In de woordenlijst worden termen uitgelegd die u tegenkomen wanneer u met de LUIS API-service werkt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220950"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Taal begrip woordenlijst van gemeenschappelijke woordenschat en concepten
In de woordenlijst Taalbegrip (LUIS) worden termen uitgelegd die u tegenkomen wanneer u met de LUIS API-service werkt.

## <a name="active-version"></a><a name="active-version"></a>Actieve versie

De actieve LUIS-versie is de versie die wijzigingen in het model ontvangt. Als u in de [LUIS-portal](luis-reference-regions.md) wijzigingen wilt aanbrengen in een versie die niet de actieve versie is, moet u die versie eerst als actief instellen.

## <a name="authoring"></a><a name="authoring"></a>Ontwerpen

Authoring is de mogelijkheid om een [LUIS-app](#luis-app)te maken, te beheren en te implementeren, hetzij met behulp van de [LUIS-portal](luis-reference-regions.md) of de [ontwerp-API's](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a><a name="authoring-key"></a>Ontwerpsleutel

Eerder de naam "Programmatic" toets. Wordt gebruikt om de app te schrijven. Niet gebruikt voor eindpuntquery's op productieniveau. Zie [Sleutellimieten voor](luis-boundaries.md#key-limits)meer informatie .

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Json-bestand voor batchtest

Batchtesten is de mogelijkheid om het model van een huidige LUIS-app te valideren met een consistente en bekende testset met gebruikersuitingen. De batchtest wordt gedefinieerd in een [JSON-opgemaakt bestand](luis-concept-batch-test.md#batch-file-format).

Zie ook:
* [Concepten](luis-concept-batch-test.md)
* [Ondersteuning](luis-how-to-batch-test.md)
* [Zelfstudie](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Samenwerker

Een medewerker/bijdrager is niet de [eigenaar](#owner) van de app, maar heeft dezelfde machtigingen om de intenties, entiteiten, uitingen en uitingen toe te voegen, te bewerken en te verwijderen.

## <a name="contributor"></a><a name="contributor"></a>Inzender

Een medewerker is hetzelfde als een [medewerker.](#collaborator)

## <a name="descriptor"></a><a name="descriptor"></a>Descriptor

Een beschrijving is een [functie](#features) die op een model wordt toegepast tijdens de training, inclusief [woordgroeplijsten](#phrase-list) en [entiteiten.](#entity) 

## <a name="domain"></a><a name="domain"></a>Domain

In de LUIS-context is een **domein** een kennisgebied. Uw domein is specifiek voor uw kennisgebied. Dit kan een algemeen gebied zijn, zoals de reisagent-app. Een reisagent-app kan ook specifiek zijn voor alleen de informatiegebieden voor uw bedrijf, zoals specifieke geografische locaties, talen en services.

## <a name="endpoint"></a><a name="endpoint"></a>Eindpunt

De URL [van luis-eindpunt](https://go.microsoft.com/fwlink/?linkid=2092356) is de plaats waar u LUIS-query's indient nadat de [LUIS-app](#luis-app) is geschreven en gepubliceerd. De URL van het eindpunt bevat het gebied van de gepubliceerde app en de app-id. U het eindpunt vinden op de pagina **[Sleutels en eindpunten](luis-how-to-azure-subscription.md)** van uw app, of u de URL van het eindpunt ophalen via de API [App-info ophalen.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)

## <a name="entity"></a><a name="entity"></a>Entiteit

[Entiteiten](luis-concept-entity-types.md) zijn belangrijke woorden in [uitingen](luis-concept-utterance.md) die informatie beschrijven die relevant is voor de [intentie,](luis-concept-intent.md)en soms zijn ze essentieel voor het. Een entiteit is in wezen een gegevenstype in LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>F-maatregel

Bij [batchtesten](luis-interactive-test.md#batch-testing)wordt de nauwkeurigheid van de test gemeten.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Vals negatief (FN)

Bij [batchtests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevenspunten uitingen waarin uw app de afwezigheid van de doelintentie/entiteit onjuist voorspelde.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Vals-positief (FP)

Bij [batchtests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevenspunten uitingen waarin uw app het bestaan van de doelintentie/entiteit onjuist voorspelde.

## <a name="features"></a><a name="features"></a>Functies

In machine learning is een [functie](luis-concept-feature.md) een onderscheidende eigenschap of kenmerk van gegevens die uw systeem observeert.

## <a name="intent"></a><a name="intent"></a>Intentie

Een [intentie](luis-concept-intent.md) vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel of doel uitgedrukt in de input van een gebruiker, zoals het boeken van een vlucht, het betalen van een factuur of het vinden van een nieuwsartikel. In LUIS is de intentievoorspelling gebaseerd op de volledige utterance. Entiteiten, ter vergelijking, zijn stukken van een uiting.

## <a name="labeling"></a><a name="labeling"></a>Labeling

Labelen of markeren is het proces waarbij een woord of woordgroep wordt gekoppeld aan de [utterance](#utterance) van een intentie met een [entiteit](#entity) (gegevenstype).

## <a name="luis-app"></a><a name="luis-app"></a>LUIS-app

Een LUIS-app is een verzameling taalmodellen voor verwerking van natuurlijke talen, waaronder [intents,](#intent) [entiteiten](#entity)en gelabelde [uitingen](#utterance).

## <a name="owner"></a><a name="owner"></a>Eigenaar

Elke app heeft één eigenaar die de persoon is die de app heeft gemaakt. De eigenaar kan [bijdragers](#collaborator)toevoegen.

## <a name="patterns"></a><a name="pattern"></a>Patronen
De vorige functie Patroon wordt vervangen door [Patronen](luis-concept-patterns.md). Gebruik patronen om de nauwkeurigheid van de voorspelling te verbeteren door minder trainingsvoorbeelden te geven.

## <a name="phrase-list"></a><a name="phrase-list"></a>Lijst met zinnen

Een [woordenlijst](luis-concept-feature.md) bevat een groep waarden (woorden of zinnen) die tot dezelfde klasse behoren en op dezelfde manier moeten worden behandeld (bijvoorbeeld namen van steden of producten). Een verwisselbare lijst wordt behandeld als synoniemen.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Vooraf gebouwd domein

Een [vooraf gebouwd domein](luis-how-to-use-prebuilt-domains.md) is een LUIS-app die is geconfigureerd voor een specifiek domein, zoals domotica (Domotica) of restaurantreserveringen (RestaurantReservering). De intenties, uitingen en entiteiten zijn geconfigureerd voor dit domein.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Vooraf gebouwde entiteit

Een [vooraf gebouwde entiteit](luis-prebuilt-entities.md) is een entiteit die LUIS biedt voor veelvoorkomende typen informatie, zoals nummer, URL en e-mail. U kiest ervoor om een vooraf gebouwde entiteit aan uw toepassing toe te voegen.

## <a name="precision"></a><a name="precision"></a>Precisie
In [batchtesten](luis-interactive-test.md#batch-testing)is precisie (ook wel positieve voorspellende waarde genoemd) de fractie van relevante uitingen onder de opgehaalde uitingen.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Programmatische toets

Hernoemd naar [authoring key](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publiceren

Publiceren betekent dat een LUIS-actieve versie beschikbaar wordt gesteld op het endpoint van fasering of [productie.](#endpoint)  

## <a name="quota"></a><a name="quota"></a>Quotum

LUIS-quotum is de beperking van de [Azure-abonnementslaag](https://aka.ms/luis-price-tier). Het LUIS-quotum kan worden beperkt door zowel aanvragen per seconde (HTTP-status 429) als totaalaanvragen in een maand (HTTP-status 403).

## <a name="recall"></a><a name="recall"></a>Herinneren
In [batch testen](luis-interactive-test.md#batch-testing), recall (ook bekend als gevoeligheid), is de mogelijkheid voor LUIS om te generaliseren.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Semantisch woordenboek
Er wordt een semantisch woordenboek weergegeven op de pagina Lijst entiteit en op de pagina Lijst met woordgroepen. Het semantische woordenboek bevat suggesties voor woorden op basis van het huidige bereik.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Sentimentanalyse
Sentimentanalyse geeft positieve of negatieve waarden van de uitingen van [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Het priming van de toespraak

Met spraakpriming kan uw spraakservice worden geprimed met uw LUIS-model.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Spellingcorrectie

Schakel De spellingcontrole van Bing in om verkeerd gespelde woorden in de uitingen te corrigeren voordat deze worden voorspelling.

## <a name="starter-key"></a><a name="starter-key"></a>Startsleutel

Een gratis sleutel om te gebruiken bij de eerste start met LUIS.

## <a name="structure"></a><a name="structure"></a>Structuur

Structuur toevoegen aan een door machines geleerde entiteit om subcomponenten te voorzien van beschrijvingen (functies) en beperkingen (reguliere expressie of lijstentiteiten).

## <a name="subscription-key"></a><a name="subscription-key"></a>Abonnementssleutel

De abonnementssleutel is de **voorspellingseindpuntsleutel** die is gekoppeld aan de LUIS-service [die u in Azure hebt gemaakt.](luis-how-to-azure-subscription.md) Deze sleutel is niet de [ontwerpsleutel.](#programmatic-key) Als u een eindpuntsleutel hebt, moet deze worden gebruikt voor eindpuntaanvragen in plaats van de ontwerpsleutel. U uw huidige eindpuntsleutel zien in de URL van het eindpunt onder aan de pagina [ **Sleutels en eindpunten** ](luis-how-to-azure-subscription.md) op de [LUIS-website.](luis-reference-regions.md) Het is de **subscription-key** waarde van abonnementssleutelnaam/waardepaar.

## <a name="test"></a><a name="test"></a>Test

[Het testen van](luis-interactive-test.md#test-your-app) een LUIS-app betekent het doorgeven van een utterance aan LUIS en het bekijken van de JSON-resultaten.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Tijdzoneverschuiving

Het eindpunt bevat tijdzoneOffset. Dit is het getal in minuten dat u wilt toevoegen of verwijderen uit de vooraf gebouwde entiteit datetimeV2. Als de utterance bijvoorbeeld 'hoe laat is het nu', is de datumtijdV2 geretourneerd de huidige tijd voor de clientaanvraag. Als uw clientverzoek afkomstig is van een bot of andere toepassing die niet hetzelfde is als de gebruiker van uw bot, moet u de verschuiving tussen de bot en de gebruiker doorgeven.

Zie [Tijdzone wijzigen van vooraf gebouwde datumV2-entiteit](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Token
Een token is de kleinste eenheid die in een entiteit kan worden gelabeld. Tokenization is gebaseerd op de [cultuur](luis-language-support.md#tokenization)van de toepassing.

## <a name="train"></a><a name="train"></a>Trein

Training is het proces van het onderwijzen van LUIS over eventuele wijzigingen in de actieve versie sinds de laatste training.

## <a name="true-negative-tn"></a><a name="true-negative"></a>True negative (TN)

Bij [batchtests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevenspunten uitingen waarin uw app de afwezigheid van de doelintentie/entiteit correct voorspelde.

## <a name="true-positive-tp"></a><a name="true-positive"></a>True positive (TP)

Bij [batchtests](luis-interactive-test.md#batch-testing)vertegenwoordigen de gegevenspunten uitingen waarin uw app het bestaan van de doelintentie/entiteit correct voorspelde.

## <a name="utterance"></a><a name="utterance"></a>Utterance

Een uiting is een natuurlijke taal zin zoals "boek 2 tickets naar Seattle volgende week dinsdag". Voorbeelduitingen worden aan de intentie toegevoegd.

## <a name="version"></a><a name="version"></a>Versie

Een [LUIS-versie](luis-how-to-manage-versions.md) is een specifiek gegevensmodel dat is gekoppeld aan een LUIS-app-id en het gepubliceerde eindpunt. Elke LUIS-app heeft ten minste één versie.
