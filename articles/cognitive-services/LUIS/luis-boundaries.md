---
title: Limieten - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat de bekende limieten van Azure Cognitive Services Language Understanding (LUIS). LUIS heeft verschillende grensgebieden. Modelgrens besturingselementen intents, entiteiten en functies in LUIS. Quotalimieten op basis van sleuteltype. Toetsenbordcombinatie regelt de LUIS-website.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220081"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Grenzen voor uw LUIS-model en sleutels
LUIS heeft verschillende grensgebieden. De eerste is de [modelgrens](#model-boundaries), die intents, entiteiten en functies in LUIS beheert. Het tweede gebied is [quotalimieten](#key-limits) op basis van sleuteltype. Een derde gebied van grenzen is de [toetsenbordcombinatie](#keyboard-controls) voor het besturen van de LUIS-website. Een vierde gebied is de [wereldregio mapping](luis-reference-regions.md) tussen de LUIS authoring website en de LUIS [endpoint](luis-glossary.md#endpoint) API's.


## <a name="model-boundaries"></a>Modelgrenzen

Als uw app de grenzen en grenzen van het LUIS-model overschrijdt, u overwegen een [LUIS-verzendapp te](luis-concept-enterprise.md#dispatch-tool-and-model) gebruiken of een [LUIS-container](luis-container-howto.md)te gebruiken.

|Onderwerp|Limiet|
|--|:--|
| [App-naam][luis-get-started-create-app] | *Standaardteken max. |
| Toepassingen| 500 toepassingen per Azure-ontwerpbron |
| [Batchgewijs testen][batch-testing]| 10 gegevenssets, 1000 uitingen per gegevensset|
| Expliciete lijst | 50 per aanvraag|
| Externe entiteiten | geen grenzen |
| [Intents][intents]|500 per toepassing: 499 aangepaste intents en de vereiste _Geen_ intentie.<br>[Dispatch-gebaseerde](https://aka.ms/dispatch-tool) toepassing heeft overeenkomstige 500 verzendbronnen.|
| [Entiteiten vermelden](./luis-concept-entity-types.md) | Ouder: 50, kind: 20.000 items. Canonieke naam is *standaardteken max. Synoniemwaarden hebben geen lengtebeperking. |
| [Machine-geleerde entiteiten + rollen:](./luis-concept-entity-types.md)<br> Samengestelde<br>Eenvoudige<br>entiteitsrol|Een limiet van 100 bovenliggende entiteiten of 330 entiteiten, welke limiet de gebruiker het eerst bereikt. Een rol telt als een entiteit voor het doel van deze grens. Een voorbeeld is een compositie met een eenvoudige entiteit, die 2 rollen heeft is: 1 composiet + 1 eenvoudige + 2 rollen = 4 van de 330 entiteiten.<br>Subcomponenten kunnen tot 5 niveaus worden genest.|
|Model als functie| Maximaal aantal modellen dat kan worden gebruikt als beschrijving (functie) voor een specifiek model om 10 modellen te zijn. Het maximum aantal woordgroeplijsten dat als beschrijving (functie) wordt gebruikt voor een specifiek model als 10 woordgroeplijsten.|
| [Voorbeeld - Dynamische lijstentiteiten](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 lijsten van ~ 1k per query voorspelling eindpunt aanvraag|
| [Patronen](luis-concept-patterns.md)|500 patronen per toepassing.<br>Maximale lengte van het patroon is 400 tekens.<br>3 Patroon.alle entiteiten per patroon<br>Maximaal 2 geneste optionele teksten in patroon|
| [Patroon.elk](./luis-concept-entity-types.md)|100 per toepassing, 3 patroon.alle entiteiten per patroon |
| [Lijst met zinnen][phrase-list]|500 zinnenlijsten. Niet-verwisselbare phraselist heeft max van 5.000 zinnen. Verwisselbare Phraselist heeft maximaal 50.000 zinnen. Maximum aantal zinnen per toepassing van 500.000 zinnen.|
| [Vooraf gebouwde entiteiten](./luis-prebuilt-entities.md) | geen limiet|
| [Entiteiten in de vorm van reguliere expressies](./luis-concept-entity-types.md)|20 entiteiten<br>500 karakter max. per entiteitspatroon voor reguliere expressies|
| [Rollen](luis-concept-roles.md)|300 rollen per toepassing. 10 rollen per entiteit|
| [Utterance][utterances] | 500 tekens|
| [Utterances][utterances] | 15.000 per toepassing - er is geen limiet aan het aantal uitingen per intentie|
| [Versies](luis-concept-version.md)| 100 versies per toepassing |
| [Versienaam][luis-how-to-manage-versions] | 10 tekens beperkt tot alfanumerieke en periode (.) |

*Standaardteken max is 50 tekens.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Naam uniciteit

Gebruik de volgende naamuniciteitsregels.

In een LUIS-app moet het volgende uniek zijn:

* versienaam
* Bedoeling
* Entiteit
* rolls

Binnen het toegepaste toepassingsgebied moeten de volgende vermeldingen uniek zijn:

* woordenlijst

## <a name="object-naming"></a>Objectnaamgeving

Gebruik de volgende tekens niet in de volgende namen.

|Object|Tekens uitsluiten|
|--|--|
|Intentie-, entiteits- en rolnamen|`:`<br>`$` <br> `&`|
|Versienaam|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Sleutelgebruik

Language Understand heeft afzonderlijke toetsen, één type voor het ontwerpen en één type voor het opvragen van het voorspellingseindpunt. Zie [Eindpuntsleutels voor ontwerp- en queryvoorspelling in LUIS](luis-concept-keys.md)voor meer informatie over de verschillen tussen sleuteltypen.

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Limieten voor resourcesleutels

De resourcesleutels hebben verschillende limieten voor het ontwerpen en eindpunt. De eindpuntsleutel luis-voorspellingsquery is alleen geldig voor eindpuntquery's.

* 500 toepassingen per Azure-ontwerpbron

|Sleutel|Ontwerpen|Eindpunt|Doel|
|--|--|--|--|
|Starter|1 miljoen/maand, 5/seconde|1 duizend/maand, 5/seconde|Uw LUIS-app ontwerpen|
|F0 - Gratis laag |1 miljoen/maand, 5/seconde|10 duizend/maand, 5/seconde|Uw LUIS-eindpunt opvragen|
|S0 - Basislaag|-|50/seconde|Uw LUIS-eindpunt opvragen|
|S0 - Standaardlaag|-|50/seconde|Uw LUIS-eindpunt opvragen|
|[Sentimentanalyse integratie](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Het toevoegen van sentimentsinformatie, waaronder gegevensextractie met sleutelzin, wordt geleverd zonder dat er nog een Azure-bron nodig is. |
|[Spraakintegratie](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1 duizend eindpuntaanvragen per kostenper eenheid|Gesproken utterance converteren naar tekstuiting en LUIS-resultaten retourneren|

[Meer informatie over prijzen.][pricing]

## <a name="keyboard-controls"></a>Toetsenbordbesturingselementen

|Toetsenbordinvoer | Beschrijving |
|--|--|
|Control+E|schakelt tussen tokens en entiteiten op de lijst met uitingen|

## <a name="website-sign-in-time-period"></a>Aanmeldingsperiode voor website

Uw aanmeldingstoegang duurt **60 minuten.** Na deze periode krijgt u deze fout. Je moet je opnieuw aanmelden.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
