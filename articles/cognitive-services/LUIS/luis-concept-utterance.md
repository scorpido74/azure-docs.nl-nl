---
title: Goed voorbeeld uitingen - LUIS
titleSuffix: Azure Cognitive Services
description: Uitingen zijn invoer van de gebruiker die uw app nodig heeft voor de interpretatie. Verzamel zinnen waarvan u denkt dat gebruikers deze zullen invoeren. Neem uitingen op die hetzelfde betekenen, maar anders zijn geconstrueerd in woordlengte en woordplaatsing.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219916"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Begrijpen wat goede uitingen zijn voor uw LUIS-app

**Uitingen** zijn invoer van de gebruiker die uw app moet interpreteren. Als u LUIS wilt trainen om intenties en entiteiten eruit te halen, is het belangrijk om verschillende voorbeelduitingen voor elke intentie vast te leggen. Actief leren, of het proces van blijven trainen op nieuwe uitingen, is essentieel voor machine-geleerde intelligentie die LUIS biedt.

Verzamel uitingen waarvan u denkt dat gebruikers deze zullen invoeren. Neem uitingen op, die hetzelfde betekenen, maar op verschillende manieren zijn opgebouwd:

* Utterance lengte - kort, gemiddeld en lang voor uw client-applicatie
* Woord- en woordgroeplengte 
* Tekstplaatsing - entiteit aan het begin, midden en einde van utterance
* Grammatica 
* Pluralisering
* Stemming
* Keuze voor zelfstandige naamwoord en werkwoord
* Interpunctie - een goede variëteit met behulp van correct, onjuist en geen grammatica

## <a name="how-to-choose-varied-utterances"></a>Verschillende uitingen kiezen

Wanneer u voor het eerst aan de slag gaat door [voorbeelduitingen toe](luis-how-to-add-example-utterances.md) te voegen aan uw LUIS-model, zijn hier enkele principes om in gedachten te houden.

### <a name="utterances-arent-always-well-formed"></a>Uitingen zijn niet altijd goed gevormd

Het kan een zin zijn, zoals "Boek een ticket naar Parijs voor mij", of een fragment van een zin, zoals 'Boeken' of 'Parijs-vlucht'.  Gebruikers maken vaak spelfouten. Wanneer u uw app plant, moet u overwegen of u [Bing Spell Check](luis-tutorial-bing-spellcheck.md) gebruikt om de invoer van gebruikers te corrigeren voordat u deze doorgeeft aan LUIS. 

Als u de uitingen van gebruikers niet spellingt, moet u LUIS trainen op uitingen die typefouten en spelfouten bevatten.

### <a name="use-the-representative-language-of-the-user"></a>Gebruik de representatieve taal van de gebruiker

Houd er bij het kiezen van uitingen rekening mee dat wat u denkt dat een veelvoorkomende term of zinniet correct is voor de typische gebruiker van uw clienttoepassing. Ze hebben mogelijk geen domeinervaring. Wees voorzichtig bij het gebruik van termen of zinnen die een gebruiker alleen zou zeggen als ze een expert.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Kies zowel voor gevarieerde terminologie als voor frasering

U zult merken dat zelfs als u inspanningen levert om gevarieerde zinspatronen te creëren, u nog wat woordenschat zult herhalen.

Neem deze voorbeelduitingen:

|Voorbeelden van utterances|
|--|
|hoe krijg ik een computer?|
|Waar haal ik een computer vandaan?|
|Ik wil een computer te krijgen, hoe ga ik over het?|
|Wanneer kan ik een computer hebben?| 

De kernterm hier, 'computer', is niet gevarieerd. Gebruik alternatieven zoals desktopcomputer, laptop, werkstation of zelfs gewoon machine. LUIS kan op intelligente wijze synoniemen uit de context afleiden, maar wanneer u uitingen maakt voor training, is het altijd beter om ze te variëren.

## <a name="example-utterances-in-each-intent"></a>Voorbeelduitingen in elke intentie

Elke intentie moet voorbeelduitingen hebben, ten minste 15. Als u een intentie hebt die geen voorbeelduitingen heeft, u LUIS niet trainen. Als u een intentie hebt met een of zeer weinig voorbeelduitingen, kan LUIS de intentie niet nauwkeurig voorspellen. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Kleine groepen van 15 uitingen toevoegen voor elke iteratie van het ontwerpen

Voeg in elke iteratie van het model geen grote hoeveelheid uitingen toe. Uitingen toevoegen in hoeveelheden van 15. [Trainen,](luis-how-to-train.md) [publiceren](luis-how-to-publish-app.md)en opnieuw [testen.](luis-interactive-test.md)  

LUIS bouwt effectieve modellen met uitingen die zorgvuldig zijn geselecteerd door de luis-modelauteur. Het toevoegen van te veel uitingen is niet waardevol omdat het verwarring introduceert.

Het is beter om te beginnen met een paar uitingen, dan [beoordelen eindpunt uitingen](luis-how-to-review-endpoint-utterances.md) voor de juiste intentie voorspelling en entiteit extractie.

## <a name="utterance-normalization"></a>Utterance-normalisatie

Utterance normalisatie is het proces van het negeren van de effecten van interpunctie en diacritics tijdens de training en voorspelling.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Utterance normalisatie voor diacritics en interpunctie

Utterance-normalisatie wordt gedefinieerd wanneer u de app maakt of importeert omdat het een instelling is in het JSON-bestand van de app. De instellingen voor utterancenormalisatie zijn standaard uitgeschakeld. 

Diacritics zijn tekens of tekens in de tekst, zoals: 

```
İ ı Ş Ğ ş ğ ö ü
```

Als uw app normalisatie inschakelt, worden scores in het **deelvenster Testen,** batchtests en eindpuntquery's gewijzigd voor alle uitingen met behulp van diacritics of interpunctie.

Schakel utterancenormalisatie in voor diacritics of interpunctie in het `settings` LUIS JSON-appbestand in de parameter.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Het normaliseren van **interpunctie** betekent dat voordat uw modellen worden getraind en voordat uw eindpuntquery's worden voorspeld, interpunctie uit de uitingen wordt verwijderd. 

Normalisering **diacritics** vervangt de tekens door diacritics in uitingen met gewone tekens. Bijvoorbeeld: `Je parle français` wordt `Je parle francais`. 

Normalisatie betekent niet dat u geen interpunctie en diacritics zult zien in uw voorbeelduitingen of voorspellingsreacties, alleen dat ze worden genegeerd tijdens training en voorspelling.


### <a name="punctuation-marks"></a>Leestekens

Interpunctie is een apart token in LUIS. Een utterance die een periode aan het einde versus een utterance bevat die geen periode aan het einde bevat, zijn twee afzonderlijke uitingen en kan twee verschillende voorspellingen krijgen. 

Als interpunctie niet wordt genormaliseerd, negeert LUIS standaard geen leestekens omdat sommige clienttoepassingen betekenis op deze markeringen kunnen plaatsen. Zorg ervoor dat uw voorbeelduitingen zowel interpunctie als geen interpunctie gebruiken om ervoor te zorgen dat beide stijlen dezelfde relatieve scores retourneren. 

Zorg ervoor dat het model interpunctie verwerkt in de voorbeelduitingen (met en geen interpunctie heeft) of in de [patronen](luis-concept-patterns.md) waar het gemakkelijker is om interpunctie met de speciale syntaxis te negeren:`I am applying for the {Job} position[.]`

Als interpunctie geen specifieke betekenis heeft in uw clienttoepassing, u overwegen [interpunctie te negeren](#utterance-normalization) door interpunctie te normaliseren. 

### <a name="ignoring-words-and-punctuation"></a>Woorden en interpunctie negeren

Als u specifieke woorden of interpunctie in patronen wilt negeren, gebruikt u `[]`een [patroon](luis-concept-patterns.md#pattern-syntax) met de _syntaxis negeren_ van vierkante haakjes. 

## <a name="training-utterances"></a>Uitingen trainen

Training is over het algemeen niet-deterministisch: de utterance voorspelling kan enigszins variëren tussen versies of apps. U niet-deterministische training verwijderen door de `UseAllTrainingData` API voor [versie-instellingen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) bij te werken met het naam/waardepaar om alle trainingsgegevens te gebruiken.

## <a name="testing-utterances"></a>Uitingen testen 

Ontwikkelaars moeten beginnen met het testen van hun LUIS-toepassing met echt verkeer door uitingen naar de URL van het [voorspellingseindpunt](luis-how-to-azure-subscription.md) te verzenden. Deze uitingen worden gebruikt om de prestaties van de intents en entiteiten te verbeteren met [Uitingen controleren.](luis-how-to-review-endpoint-utterances.md) Tests die zijn ingediend met het testvenster voor luis-website, worden niet via het eindpunt verzonden en dragen dus niet bij aan actief leren. 

## <a name="review-utterances"></a>Uitingen controleren

Nadat uw model is getraind, [endpoint](luis-glossary.md#endpoint) gepubliceerd en eindpuntquery's ontvangt, [controleert u de uitingen die](luis-how-to-review-endpoint-utterances.md) door LUIS worden voorgesteld. LUIS selecteert eindpuntuitingen met lage scores voor de intentie of entiteit. 

## <a name="best-practices"></a>Aanbevolen procedures

Bekijk [aanbevolen procedures](luis-concept-best-practices.md) en pas ze toe als onderdeel van uw reguliere ontwerpcyclus.

## <a name="label-for-word-meaning"></a>Label voor woordbetekenis

Als het woord keuze of woord regeling is hetzelfde, maar betekent niet hetzelfde, niet label met de entiteit. 

De volgende uitingen, `fair` het woord is een homograaf. Het is hetzelfde gespeld, maar heeft een andere betekenis:

|Utterance|
|--|
|Wat voor soort county beurzen gebeuren in de omgeving van Seattle deze zomer?|
|Is de huidige beoordeling voor de Seattle review eerlijk?|

Als u wilt dat een gebeurtenisentiteit alle `fair` gebeurtenisgegevens zou vinden, labelt u het woord in de eerste utterance, maar niet in de tweede.


## <a name="next-steps"></a>Volgende stappen
Zie [Voorbeelduitingen toevoegen](luis-how-to-add-example-utterances.md) voor informatie over het trainen van een LUIS-app om gebruikersuitingen te begrijpen.

