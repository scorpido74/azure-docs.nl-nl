---
title: Limieten-LUIS
titleSuffix: Azure Cognitive Services
description: In dit artikel bevat de bekende beperkingen van Azure Cognitive Services Language Understanding (LUIS). LUIS heeft verschillende gebieden van de grens. Model grens bepaalt intents, entiteiten en functies van LUIS. De quotalimieten op basis van het type sleutel. Toetscombinatie bepaalt de LUIS-website.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d584b00caef628eb9dfd085b1fdce2bb7b353988
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273507"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Grenzen voor uw LUIS-model en-sleutels
LUIS heeft verschillende gebieden van de grens. De eerste is de [model grens](#model-boundaries), waarmee de intenties, entiteiten en functies in Luis worden beheerd. Het tweede gebied is [quotum limieten](#key-limits) op basis van het sleutel type. Een derde gebied met grenzen is de [toetscombinatie voor het beheren van de Luis](#keyboard-controls) -website. Een vierde gebied is de [wereld wijde toewijzing](luis-reference-regions.md) tussen de Luis-ontwerp website en de Luis- [eindpunt](luis-glossary.md#endpoint) api's.


## <a name="model-boundaries"></a>Model grenzen

Als uw app de limieten en grenzen van het LUIS-model overschrijdt, kunt u overwegen een [Luis-verzend](luis-concept-enterprise.md#dispatch-tool-and-model) -app te gebruiken of een [Luis-container](luis-container-howto.md)te gebruiken.

|Onderwerp|Limiet|
|--|:--|
| [Naam van app][luis-get-started-create-app] | \* Standaard teken max |
| Toepassingen| 500-toepassingen per Azure-ontwerp bron |
| [Batch testen][batch-testing]| 10 gegevenssets, 1000 uitingen per gegevensset|
| Expliciete lijst | 50 per toepassing|
| Externe entiteiten | geen limieten |
| [Intents][intents]|500 per toepassing: 499 aangepaste intenties en de vereiste _geen_ intentie.<br>[Op verzen ding gebaseerde](https://aka.ms/dispatch-tool) toepassing heeft corresponderende 500-verzend bronnen.|
| [Entiteiten weer geven](./luis-concept-entity-types.md) | Bovenliggende: 50, onderliggende: maximaal 20.000 items. Canonieke naam is * standaard teken max. Synoniem waarden hebben geen lengtebeperking. |
| Door [machines geleerde entiteiten + rollen](./luis-concept-entity-types.md):<br> werken<br>Simple<br>entiteits rol|Een limiet van 100 bovenliggende entiteiten of 330 entiteiten, afhankelijk van het aantal gebruikers dat het eerst voor komt. Een rol telt als een entiteit voor het doel van deze grens. Een voor beeld is een samen stelling met een eenvoudige entiteit, die twee rollen heeft: 1 composiet + 1 eenvoudige + 2 rollen = 4 van de 330 entiteiten.<br>Subonderdelen kunnen Maxi maal vijf niveaus worden genest.|
|Model als onderdeel| Het maximum aantal modellen dat kan worden gebruikt als een descriptor (functie) voor een specifiek model om 10 modellen te zijn. Het maximum aantal woordgroepen lijsten dat als een descriptor (functie) voor een specifiek model wordt gebruikt, is 10 woordgroepen lijsten.|
| [Preview-dynamische lijst entiteiten](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 lijsten met een eindpunt aanvraag van ongeveer 1 KB per query|
| [Daarin](luis-concept-patterns.md)|500 patronen per toepassing.<br>Maximale lengte van het patroon is 400 tekens.<br>3 Pattern.any entiteiten per patroon<br>Maximaal 2 geneste optionele tekst in het patroon|
| [Patroon. alle](./luis-concept-entity-types.md)|100 per toepassing, 3 pattern.any entiteiten per patroon |
| [Woordgroepen lijst][phrase-list]|500 woordgroepen lijsten. Niet-verwisselbaar phraselist heeft een maximum van 5.000 zinsdelen. Interchangeable Phraselist heeft een maximum van 50.000 zinsdelen. Maximum aantal woord groepen per toepassing van 500.000-zinsdelen.|
| [Vooraf gemaakte entiteiten](./luis-prebuilt-entities.md) | geen limiet|
| [Entiteiten van reguliere expressie](./luis-concept-entity-types.md)|20 entiteiten<br>Max. 500 tekens. per entiteit patroon van reguliere expressie|
| [Rollen](luis-concept-roles.md)|300 rollen per toepassing. 10 rollen per entiteit|
| [Utterance][utterances] | 500 tekens bevatten|
| [Uitingen][utterances] | 15.000 per toepassing: er is geen limiet voor het aantal uitingen per intentie|
| [Lager](luis-concept-version.md)| 100 versies per toepassing |
| [Versie naam][luis-how-to-manage-versions] | beperkt tot alleen alfanumerieke tekens en periode 10 tekens (.) |

\* Standaard teken maximaal is 50 tekens.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Unieke naam

Gebruik de volgende naamgevings regels voor uniekheid.

Het volgende moet uniek zijn in een LUIS-app:

* Versie naam
* bedoeling
* entiteit
* rolls

Het volgende moet uniek zijn binnen het bereik dat wordt toegepast:

* woordgroepen lijst

## <a name="object-naming"></a>Naamgeving van objecten

Gebruik niet de volgende tekens in de volgende namen.

|Object|Tekens uitsluiten|
|--|--|
|De namen van intentie, entiteit en rollen|`:`<br>`$` <br> `&`|
|Versie naam|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Sleutelgebruik

Taal kennis heeft afzonderlijke sleutels, één type voor ontwerpen en één type voor het uitvoeren van query's op het Voorspellings eindpunt. Zie voor meer informatie over de verschillen tussen sleutel typen [ontwerpen en query prediction-eindpunt sleutels in Luis](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Limieten voor de resource sleutel

De resource sleutels hebben verschillende limieten voor ontwerpen en eind punten. De eindpunt sleutel van de LUIS-Voorspellings query is alleen geldig voor eindpunt query's.

* 500-toepassingen per Azure-ontwerp bron

|Sleutel|Ontwerpen|Eindpunt|Doel|
|--|--|--|--|
|Starter|1 miljoen/maand, 5 per seconde|1-1000/maand, 5 per seconde|Uw LUIS-app ontwerpen|
|F0-gratis laag |1 miljoen/maand, 5 per seconde|10 duizend/maand, 5 per seconde|Uitvoeren van query's uw LUIS-eindpunt|
|S0-Basic-laag|-|50 per seconde|Uitvoeren van query's uw LUIS-eindpunt|
|S0-Standard-laag|-|50 per seconde|Uitvoeren van query's uw LUIS-eindpunt|
|[Integratie van sentiment-analyse](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Het toevoegen van sentiment-informatie, inclusief sleutel woordgroepen gegevens extractie, wordt verstrekt zonder dat er een andere Azure-resource nodig is. |
|[Spraak integratie](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 eindpunt aanvragen per eenheids kosten|Gesproken utterance converteren naar tekst utterance en LUIS resultaten geretourneerd|

[Meer informatie over prijzen.][pricing]

## <a name="keyboard-controls"></a>Toetsenbord

|Toetsenbordinvoer | Beschrijving |
|--|--|
|Besturingselement + E|Hiermee schakelt u tussen tokens en entiteiten in de lijst met uitingen|

## <a name="website-sign-in-time-period"></a>Tijds periode website aanmelding

Uw aanmeldings toegang is **60 minuten**. Na deze periode ontvangt u deze fout. U moet zich opnieuw aanmelden.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
