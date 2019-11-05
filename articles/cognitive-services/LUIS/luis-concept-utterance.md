---
title: Goed voor beeld van uitingen-LUIS
titleSuffix: Azure Cognitive Services
description: Uitingen zijn invoer van de gebruiker die uw app moet interpreteren. Verzamel zinsdelen die u denkt dat gebruikers worden ingevoerd. Uitingen bevatten die hetzelfde zijn, maar die anders zijn gemaakt in woord lengte en woord plaatsing.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 8069b3b9c9a226e29a3eae3261948ee92291726d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486637"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Begrijpen wat goede uitingen zijn voor uw LUIS-app

**Uitingen** zijn invoer van de gebruiker die uw app moet interpreteren. Om LUIS te trainen om de intenties en entiteiten van hen te extra heren, is het belang rijk dat u voor elke intentie een groot aantal verschillende voor beeld-uitingen vastlegt. Actief leren of het proces van het voortzetten van de nieuwe uitingen is essentieel voor door de machine geleerd intelligentie die LUIS biedt.

Verzamel uitingen die u denkt dat gebruikers worden ingevoerd. Neem uitingen op. Dit betekent hetzelfde als op verschillende manieren:

* Lengte van Utterance-short, medium en Long voor uw client-app
* Lengte van woord en woord groep 
* Plaatsing van woorden: entiteit aan het begin, Midden en einde van utterance
* Gecontroleerd 
* Pluralization
* Als gevolg
* Keuze uit zelfstandig naam woord en werk woord
* Interpunctie: een goed RAS met de juiste, onjuiste en geen grammatica

## <a name="how-to-choose-varied-utterances"></a>Een variabele uitingen kiezen

Wanneer u voor het eerst aan de slag gaat door [bijvoorbeeld uitingen toe te voegen](luis-how-to-add-example-utterances.md) aan uw Luis-model, zijn hier enkele principes die u moet onthouden.

### <a name="utterances-arent-always-well-formed"></a>Uitingen zijn niet altijd goed gevormd

Dit kan een zin zijn, zoals "een ticket aan Parijs voor mij" of een fragment van een zin, zoals "boeken" of "Parijs vlucht".  Gebruikers maken vaak spel fouten. Overweeg bij het plannen van uw app of u [Bing spellingcontrole](luis-tutorial-bing-spellcheck.md) gebruikt om gebruikers invoer te corrigeren voordat u deze aan Luis door gegeven. 

Als u de spelling van de gebruiker uitingen niet wilt controleren, moet u LUIS trainen op uitingen met type-en spel fouten.

### <a name="use-the-representative-language-of-the-user"></a>De representatieve taal van de gebruiker gebruiken

Wanneer u uitingen kiest, moet u er rekening mee houden dat u een veelvoorkomende term of woord groep zou kunnen hebben voor de typische gebruiker van uw client toepassing. Ze hebben mogelijk geen domein ervaring. Wees voorzichtig met het gebruik van termen of zinsdelen die een gebruiker alleen zou zeggen als hij een expert was.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Kies verschillende terminologie en formule ring

U zult merken dat zelfs als u een gevarieerde zin maakt, u nog steeds een woorden lijst moet herhalen.

Doe het volgende voor beeld uitingen:

|Voorbeelden van utterances|
|--|
|Hoe krijg ik een computer?|
|Hoe krijg ik een computer?|
|Ik wil een computer verkrijgen, hoe ga ik daar naartoe?|
|Wanneer kan ik een computer?| 

De basis term hier, ' computer ', is niet gevarieerd. Gebruik alternatieven als desktop computer, laptop, werk station of zelfs alleen machine. LUIS maakt op intelligente wijze synoniemen van context, maar wanneer u uitingen voor de training gaat maken, is het nog steeds beter om ze te variëren.

## <a name="example-utterances-in-each-intent"></a>Voor beeld van uitingen in elke intentie

Elke intentie moet voorbeeld uitingen hebben, ten minste 15. Als u een intentie hebt die geen voorbeeld uitingen heeft, kunt u LUIS niet trainen. Als u een intentie hebt met een of meer voor beeld uitingen, zal LUIS de bedoeling niet nauw keurig voors pellen. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Kleine groepen van 15 uitingen toevoegen voor elke ontwerp herhaling

Voeg in elke herhaling van het model geen grote hoeveelheid uitingen toe. Voeg uitingen toe aan hoeveel heden van 15. [Train](luis-how-to-train.md), [Publiceer](luis-how-to-publish-app.md)en [test](luis-interactive-test.md) het opnieuw.  

LUIS bouwt efficiënte modellen met uitingen die zorgvuldig zijn geselecteerd door de auteur van het LUIS-model. Het is niet waardevol om te veel uitingen toe te voegen, omdat het Verwar ring leidt.  

Het is beter om met een paar uitingen te beginnen en vervolgens [eind punt uitingen te controleren](luis-how-to-review-endpoint-utterances.md) op juiste intentie van voor spelling en extractie van entiteiten.

## <a name="utterance-normalization"></a>Utterance normalisatie

Utterance normalisatie is het proces van het negeren van de effecten van interpunctie en diakritische tekens tijdens training en voor spellingen.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Utterance-normalisatie voor diakritische tekens en interpunctie

Utterance normalisatie wordt gedefinieerd wanneer u de app maakt of importeert, omdat het een instelling in het JSON-bestand van de app is. De utterance normalisatie-instellingen zijn standaard uitgeschakeld. 

Diakritische tekens zijn tekens of tekens in de tekst, bijvoorbeeld: 

```
İ ı Ş Ğ ş ğ ö ü
```

Als uw app normalisatie inschakelt, worden de scores in het **test** deel venster, batch tests en eindpunt query's gewijzigd voor alle uitingen met diakritische tekens of interpunctie.

Schakel utterance normalisatie voor diakritische tekens of interpunctie in voor uw LUIS JSON-app-bestand in de para meter `settings`.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Het normaliseren van **interpunctie** betekent dat voordat uw modellen worden getraind en voordat uw eindpunt query's worden voor speld, wordt interpunctie verwijderd uit de uitingen. 

Als **diakritische** tekens worden genormaliseerd, worden de tekens vervangen door accenten in uitingen. Bijvoorbeeld: `Je parle français` wordt `Je parle francais`. 

Norma Lise ring betekent niet dat er geen lees-en diakritische tekens worden weer geven in uw voor beeld-uitingen of Voorspellings reacties, alleen dat ze worden genegeerd tijdens de training en voor spellingen.


### <a name="punctuation-marks"></a>Lees tekens

Interpunctie is een afzonderlijk token in LUIS. Een utterance die een punt bevat aan het einde en een utterance die geen punt aan het einde bevatten, zijn twee afzonderlijke uitingen en kunnen twee verschillende voor spellingen ontvangen. 

Als interpunctie niet is genormaliseerd, LUIS niet standaard interpunctie markeringen negeren, omdat sommige client toepassingen significant kunnen zijn voor deze markeringen. Zorg ervoor dat uw voor beeld-uitingen zowel interpunctie als geen interpunctie gebruiken voor beide stijlen om dezelfde relatieve scores te retour neren. 

Zorg ervoor dat het model interpunctie verwerkt in het [voor beeld uitingen](luis-concept-utterance.md) (zonder interpunctie) of in de [patronen](luis-concept-patterns.md) waar het gemakkelijker is om interpunctie te negeren met de speciale syntaxis: `I am applying for the {Job} position[.]`

Als interpunctie geen specifieke betekenis heeft in uw client toepassing, kunt u overwegen [interpunctie te negeren](#utterance-normalization) door interpunctie te normaliseren. 

### <a name="ignoring-words-and-punctuation"></a>Woorden en interpunctie negeren

Als u specifieke woorden of interpunctie in patronen wilt negeren, gebruikt u een [patroon](luis-concept-patterns.md#pattern-syntax) met de syntaxis _negeren_ van de vier Kante haken `[]`. 

## <a name="training-utterances"></a>Trainings uitingen

Training is doorgaans niet-deterministisch: de utterance-voor spelling kan enigszins variëren in verschillende versies of apps. U kunt niet-deterministische trainingen verwijderen door de API voor [versie-instellingen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) bij te werken met de `UseAllTrainingData` naam/waarde-paar om alle trainings gegevens te gebruiken.

## <a name="testing-utterances"></a>Uitingen testen 

Ontwikkel aars moeten hun LUIS-toepassing met echt verkeer testen door uitingen naar de URL voor het [Voorspellings eindpunt](luis-how-to-azure-subscription.md) te sturen. Deze uitingen worden gebruikt om de prestaties van de intenties en entiteiten te verbeteren met [beoordeling uitingen](luis-how-to-review-endpoint-utterances.md). Tests die zijn verzonden met het deel venster LUIS website testen, worden niet via het eind punt verzonden en bijdragen dus niet aan actief leren. 

## <a name="review-utterances"></a>Uitingen controleren

Nadat uw model is getraind, gepubliceerd en [endpoint](luis-glossary.md#endpoint) -query's ontvangen, [controleert u de uitingen](luis-how-to-review-endpoint-utterances.md) die door Luis is voorgesteld. LUIS selecteert eind punt uitingen met een lage score voor de intentie of entiteit. 

## <a name="best-practices"></a>Aanbevolen procedures

Bekijk [Aanbevolen procedures](luis-concept-best-practices.md) en pas deze toe als onderdeel van uw reguliere ontwerp cyclus.

## <a name="label-for-word-meaning"></a>Label voor woord betekenis

Als het woord of de rang schikking van Word hetzelfde is, maar niet hetzelfde is, moet u deze niet aan de entiteit labelen. 

De volgende uitingen is het woord `fair` een homograph is. De spelling is hetzelfde, maar heeft een andere betekenis:

|Utterance|
|--|
|Wat voor soort graafschap komt in het gebied Seattle van deze zomer?|
|Is de huidige beoordeling voor de Seattle-beoordeling eerlijk?|

Als u wilt dat een gebeurtenis entiteit alle gebeurtenis gegevens vindt, labelt u het woord `fair` in de eerste utterance, maar niet in de tweede.


## <a name="next-steps"></a>Volgende stappen
Zie [voor beeld uitingen toevoegen](luis-how-to-add-example-utterances.md) voor informatie over het trainen van een Luis-app om inzicht te krijgen in gebruikers uitingen.

