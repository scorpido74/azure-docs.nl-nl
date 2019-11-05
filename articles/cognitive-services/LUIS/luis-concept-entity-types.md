---
title: Entiteits typen-LUIS
titleSuffix: Azure Cognitive Services
description: 'Entiteiten halen gegevens op uit de utterance. Entiteits typen bieden u voorspel bare extractie van gegevens. Er zijn twee typen entiteiten: door de machine geleerd en niet-machine geleerde. Het is belang rijk om te weten met welk type entiteit u werkt in uitingen.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487598"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entiteiten en hun doel in LUIS

Het belangrijkste doel van entiteiten is het geven van de voorspel bare extractie van gegevens door de client toepassing. Een _optioneel_, secundair doel is het verhogen van de voor spelling van de intentie met descriptors. 

Er zijn twee typen entiteiten: 

* machine-geleerd-van context
* niet-computer-geleerd-voor exacte tekst overeenkomsten

Begin altijd met een door de machine geleerde entiteit, omdat dit het breedste bereik aan opties voor gegevens extractie biedt.

## <a name="entity-compared-to-intent"></a>Entiteit vergeleken met intentie

De entiteit vertegenwoordigt een gegevens concept in de utterance die u wilt uitpakken. 

Houd rekening met de volgende drie uitingen:

|Utterance|Geëxtraheerde gegevens|Uitleg|
|--|--|--|
|`Help`|-|Niets te extra heren.|
|`Send Bob a present`|Bob, aanwezig|Bob is zeker belang rijk voor het volt ooien van de taak. Dit kan voldoende informatie bevatten of de bot moet wellicht verduidelijken wat de huidige is met een vervolg vraag.|
|`Send Bob a box of chocolates.`|De twee belang rijke stukjes gegevens, Bob en het vak van chocolade, zijn belang rijk voor het volt ooien van de aanvraag van de gebruiker.|

Een utterance kan veel entiteiten bevatten of helemaal geen. Een client toepassing heeft de entiteit _mogelijk_ nodig om de taak uit te voeren. 

Ter vergelijking is de voor spelling van de intentie voor een utterance _vereist_ en vertegenwoordigt het hele utterance. LUIS vereist voor beeld-uitingen zijn opgenomen in een intentie. Als de primaire intentie van de utterance niet belang rijk is voor de client toepassing, voegt u alle uitingen toe aan de geen intentie. 

Als u later in de app-levens cyclus vindt, wilt u de uitingen opdelen. u kunt dit eenvoudig doen. Zo kunt u de uitingen ordenen terwijl u ontwerpt, of kunt u het voorspelde voor deel van de client toepassing gebruiken. 

Het is niet vereist om de voorspelde intentie te gebruiken in de client toepassing, maar deze wordt geretourneerd als onderdeel van de reactie van het Voorspellings eindpunt.

## <a name="entities-represent-data"></a>Entiteiten vertegenwoordigen gegevens

Entiteiten zijn gegevens die u wilt ophalen uit de utterance. Dit kan een naam, datum, product naam of een wille keurige groep woorden zijn. 

|Utterance|Entiteit|Gegevens|
|--|--|--|
|3 tickets kopen voor New York|Vooraf gebouwd nummer<br>Locatie. doel|3<br>New York|
|Een ticket van New York aan Londen kopen op 5 maart|Locatie. Origin<br>Locatie. doel<br>Prebuild datetimeV2|New York<br>Londen<br>5 maart 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entiteiten zijn optioneel, maar worden nadrukkelijk aanbevolen

Hoewel intenties vereist zijn, zijn entiteiten optioneel. U hoeft geen entiteiten te maken voor elk concept in uw app, maar alleen voor de toepassingen die vereist zijn voor het uitvoeren van een actie door de client toepassing. 

Als uw uitingen niet de details bevat die uw bot nodig heeft, hoeft u deze niet toe te voegen. Als uw app is gerijpt, kunt u deze later toevoegen. 

Als u niet zeker weet hoe u de informatie zou gebruiken, voegt u enkele algemene vooraf gedefinieerde entiteiten toe, zoals [datetimeV2](luis-reference-prebuilt-datetimev2.md), [rang telwoord](luis-reference-prebuilt-ordinal.md), [e-mail adres](luis-reference-prebuilt-email.md)en [telefoon nummer](luis-reference-prebuilt-phonenumber.md).

## <a name="design-entities-for-decomposition"></a>Entiteiten voor ontleding ontwerpen

Begin uw ontwerp van de entiteit met een door de machine geleerde entiteit. Zo kunt u eenvoudig de groei van het ontwerp en wijzigingen van uw entiteit gedurende een bepaalde periode vereenvoudigen. Voeg **subonderdelen** (onderliggende entiteiten) met **beperkingen** en **descriptoren** toe om het entiteits ontwerp te volt ooien. 

Als u ontwerpt voor ontleding, kan LUIS de nauw keurigheid van entiteits omzetting naar uw client toepassing retour neren. Op deze manier kan uw client toepassing zich richten op bedrijfs regels en de gegevens omzetting voor LUIS behouden.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Door machines geleerde entiteiten zijn primaire gegevens verzamelingen

Door machines geleerde entiteiten zijn de gegevens eenheid op het hoogste niveau. Subonderdelen zijn onderliggende entiteiten van door machines geleerde entiteiten. 

**Beperkingen** zijn exacte-tekst overeenkomende entiteiten waarmee regels worden toegepast om gegevens te identificeren en op te halen. **Descriptors** zijn functies die worden toegepast om de relevantie van de woorden of zinsdelen voor de voor spelling te verhogen.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typen entiteiten

Kies de entiteit op basis van de manier waarop de gegevens moeten worden geëxtraheerd en hoe deze moeten worden weer gegeven nadat deze is geëxtraheerd.

|Entiteits type|Doel|
|--|--|
|[**Machine-geleerd**](#composite-entity)|Bovenliggende groepering van entiteiten, ongeacht het entiteits type. Door de machine geleerd entiteiten leren van de context in het utterance. Dit maakt variatie van de plaatsing in voor beeld uitingen aanzienlijk. |
|[**Orderverzamellijst**](#list-entity)|Lijst met items en de bijbehorende synoniemen die zijn geëxtraheerd met **exact overeenkomende tekst**.|
|[**Patroon. alle**](#patternany-entity)|Entiteit waarvan het einde van de entiteit moeilijk te bepalen is. |
|[**Vooraf gedefinieerde**](#prebuilt-entity)|Al getraind voor het extra heren van specifieke soorten gegevens, zoals URL of e-mail. Sommige van deze vooraf gemaakte entiteiten worden gedefinieerd in het open source- [tekst](https://github.com/Microsoft/Recognizers-Text) project voor herkenning. Als uw specifieke cultuur of entiteit momenteel niet wordt ondersteund, draagt u bij aan het project.|
|[**Reguliere expressie**](#regular-expression-entity)|Gebruikt reguliere expressie voor **exacte tekst overeenkomst**.|

### <a name="entity-role-defines-context"></a>Entiteit rol definieert context

De rol van een entiteit is de alias met de naam op basis van de context binnen de utterance. Een voor beeld is een utterance voor het reserveren van een vlucht met twee locaties, oorsprong en bestemming.

`Book a flight from Seattle to Cairo`

De twee voor beelden van een `location` entiteit moeten worden geëxtraheerd. De client-app moet weten welk type locatie voor elk van deze is, om de aankoop van het ticket te volt ooien. De `location`-entiteit heeft twee rollen van `origin` en `destination` nodig en beide moeten worden gemarkeerd in het voor beeld uitingen. 

Als LUIS de `location` vindt maar de rol niet kan bepalen, wordt de locatie-entiteit nog steeds geretourneerd. De client toepassing moet aan een vraag worden opgevolgd om te bepalen welk type locatie de gebruiker had. 

Er kunnen meerdere entiteiten in een utterance bestaan en kunnen worden geëxtraheerd zonder gebruik van rollen. Als de context van de zin de waarde van de entiteit aangeeft, moet een rol worden gebruikt.

Als de utterance een lijst met locaties bevat, `I want to travel to Seattle, Cairo, and London.`, is dit een lijst waarin elk item geen andere betekenis heeft. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Als u meer dan het maximum aantal entiteiten wilt 

Neem contact op met de ondersteuning als u meer nodig hebt dan de limiet. Als u dit wilt doen, verzamelt u gedetailleerde informatie over uw systeem, gaat u naar de [Luis](luis-reference-regions.md#luis-website) -website en selecteert u vervolgens **ondersteuning**. Als uw Azure-abonnement ondersteunings services bevat, neemt u contact op met de [technische ondersteuning van Azure](https://azure.microsoft.com/support/options/). 

## <a name="entity-prediction-status"></a>Status van de voor spelling van de entiteit

De LUIS-portal laat zien wanneer de entiteit, in een voor beeld utterance, een andere voor spelling van de entiteit heeft dan de entiteit die u hebt geselecteerd. Deze andere score is gebaseerd op het huidige getrainde model. 

## <a name="next-steps"></a>Volgende stappen

Leer concepten over goede [uitingen](luis-concept-utterance.md). 

Zie [entiteiten toevoegen](luis-how-to-add-entities.md) voor meer informatie over het toevoegen van entiteiten aan uw Luis-app.
