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
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221027"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entiteiten en hun doel in LUIS

Het belangrijkste doel van entiteiten is het geven van de voorspel bare extractie van gegevens door de client toepassing. Een _optioneel_, secundair doel is het verhogen van de voor spelling van de intentie of andere entiteiten met descriptors.

Er zijn twee typen entiteiten:

* machine-geleerd-van context
* niet-computer geleerd: voor exacte tekst overeenkomsten, patroon overeenkomsten of detectie door vooraf gemaakte entiteiten

Door machines geleerde entiteiten bieden een breed scala aan opties voor gegevens extractie. Niet door machines geleerde entiteiten werken met tekst overeenkomst en kunnen onafhankelijk of als een [beperking](#design-entities-for-decomposition) worden gebruikt voor een door de machine geleerde entiteit.

## <a name="entities-represent-data"></a>Entiteiten vertegenwoordigen gegevens

Entiteiten zijn gegevens die u wilt ophalen uit de utterance, zoals namen, datums, product namen of een belang rijke groep woorden. Een utterance kan bevatten veel entiteiten of geen helemaal. Een client toepassing heeft _mogelijk_ de gegevens nodig om de taak uit te voeren.

Entiteiten moeten consistent worden gelabeld voor alle trainings uitingen voor elke intentie in een model.

 U kunt uw eigen entiteiten definiëren of vooraf gemaakte entiteiten gebruiken om tijd te besparen op algemene concepten, zoals [datetimeV2](luis-reference-prebuilt-datetimev2.md), [rang telwoord](luis-reference-prebuilt-ordinal.md), [e-mail adres](luis-reference-prebuilt-email.md)en [telefoon nummer](luis-reference-prebuilt-phonenumber.md).

|Utterance|Entiteit|Gegevens|
|--|--|--|
|3 tickets naar de New York kopen|Vooraf gedefinieerde getal<br>Location.Destination|3<br>New York|
|Een ticket van de New York naar Londen kopen op 5 maart|Location.Origin<br>Location.Destination<br>Vooraf gedefinieerde datetimeV2|New York<br>Londen<br>5 maart 2018|

### <a name="entities-are-optional"></a>Entiteiten zijn optioneel

Intents zijn vereist, zijn entiteiten optioneel. U hoeft geen entiteiten te maken voor elk concept in uw app, maar alleen voor de toepassingen die vereist zijn voor het uitvoeren van een actie door de client toepassing.

Als uw uitingen geen gegevens voor de client toepassing heeft, hoeft u geen entiteiten toe te voegen. Als uw toepassing zich ontwikkelt en er een nieuwe nood zaak voor gegevens wordt geïdentificeerd, kunt u later de juiste entiteiten aan uw LUIS-model toevoegen.

## <a name="entity-compared-to-intent"></a>Entiteit in vergelijking met opzet

De entiteit vertegenwoordigt een gegevens concept in de utterance die u wilt uitpakken.

Een utterance kan eventueel entiteiten bevatten. Ter vergelijking is de voor spelling van de intentie voor een utterance _vereist_ en vertegenwoordigt het hele utterance. LUIS vereist voor beeld-uitingen zijn opgenomen in een intentie.

Houd rekening met de volgende vier uitingen:

|Utterance|Bedoeling voorspeld|Geëxtraheerde entiteiten|Uitleg|
|--|--|--|--|
|Help|Help|-|Niets te extra heren.|
|Iets verzenden|sendSomething|-|Niets te extra heren. Het model is niet getraind om `something` in deze context uit te pakken en er is geen ontvanger.|
|Bob een huidige verzenden|sendSomething|`Bob`, `present`|Het model is getraind met de vooraf opgebouwde [persoonnaam](luis-reference-prebuilt-person.md) , die de naam heeft geëxtraheerd `Bob`. Er is een door de machine geleerde entiteit gebruikt om `present`uit te pakken.|
|Een doos van Choco lade verzenden|sendSomething|`Bob`, `box of chocolates`|De twee belang rijke stukjes gegevens, `Bob` en de `box of chocolates`, zijn geëxtraheerd door entiteiten.|

## <a name="design-entities-for-decomposition"></a>Entiteiten voor ontleding ontwerpen

Het is een goede entiteits ontwerp om de entiteit op het hoogste niveau een entiteit te maken die door de machine wordt geleerd. Hierdoor kunnen wijzigingen aan het ontwerp van uw entiteit gedurende een bepaalde periode en het gebruik van **subonderdelen** (onderliggende entiteiten), eventueel met **beperkingen** en **descriptoren**, de entiteit op het hoogste niveau afbreken in de onderdelen die nodig zijn voor de client toepassing.

Als u ontwerpt voor ontleding, kan LUIS de nauw keurigheid van entiteits omzetting naar uw client toepassing retour neren. Op deze manier kan uw client toepassing zich richten op bedrijfs regels en de gegevens omzetting voor LUIS behouden.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Door machines geleerde entiteiten zijn primaire gegevens verzamelingen

Door [**machines geleerde entiteiten**](tutorial-machine-learned-entity.md) zijn de gegevens eenheid op het hoogste niveau. Subonderdelen zijn onderliggende entiteiten van door machines geleerde entiteiten.

Een door de machine geleerde entiteits triggers op basis van de context die is geleerd via uitingen training. **Beperkingen** zijn optionele regels die worden toegepast op een door een machine geleerde entiteit die de trigger beperkt op basis van de exacte definitie van de tekst die overeenkomt met een niet door de machine geleerde entiteit, zoals een [lijst](reference-entity-list.md) of [regex](reference-entity-regular-expression.md). Een `size` door de machine geleerde entiteit kan bijvoorbeeld een beperking hebben van een `sizeList` lijst entiteit die de `size` entiteit beperkt om alleen te activeren wanneer de waarden in de `sizeList` entiteit worden aangetroffen.

[**Descriptors**](luis-concept-feature.md) zijn functies die worden toegepast om de relevantie van de woorden of zinsdelen voor de voor spelling te verhogen. Ze worden *descriptors* genoemd, omdat ze worden gebruikt om een intentie of entiteit te *beschrijven* . Descriptoren beschrijven het onderscheiden van eigenschappen of kenmerken van gegevens, zoals belang rijke woorden of zinsdelen die LUIS observeert en leert.

Wanneer u een woordgroepen lijst functie in uw LUIS-app maakt, wordt deze standaard globaal ingeschakeld en toegepast op alle intenten en entiteiten. Als u echter de woordgroepen lijst toepast als een descriptor (functie) van een door de machine geleerde entiteit (of *model*), vermindert het bereik van de groep alleen voor het model en wordt het niet meer gebruikt met alle andere modellen. Het gebruik van een woordgroepen lijst als een descriptor van een model helpt de ontleding te verhelpen door de nauw keurigheid te helpen van het model waarop het wordt toegepast.

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
|[**Machine-geleerd**](tutorial-machine-learned-entity.md)|Door de machine geleerd entiteiten leren van de context in het utterance. Bovenliggende groepering van entiteiten, ongeacht het entiteits type. Dit maakt variatie van de plaatsing in voor beeld uitingen aanzienlijk. |
|[**Orderverzamellijst**](reference-entity-list.md)|Lijst met items en de bijbehorende synoniemen die zijn geëxtraheerd met **exact overeenkomende tekst**.|
|[**Patroon. alle**](reference-entity-pattern-any.md)|Entiteit waarvan het einde van de entiteit moeilijk te bepalen is. |
|[**Vooraf gedefinieerde**](luis-reference-prebuilt-entities.md)|Al getraind voor het extra heren van specifieke soorten gegevens, zoals URL of e-mail. Sommige van deze vooraf gemaakte entiteiten worden gedefinieerd in het open source- [tekst](https://github.com/Microsoft/Recognizers-Text) project voor herkenning. Als uw specifieke cultuur of de entiteit wordt momenteel niet ondersteund, dragen bij aan het project.|
|[**Reguliere expressie**](reference-entity-regular-expression.md)|Gebruikt reguliere expressie voor **exacte tekst overeenkomst**.|

## <a name="extracting-contextually-related-data"></a>Contextuele gerelateerde gegevens extra heren

Een utterance kan twee of meer exemplaren van een entiteit bevatten waarbij de betekenis van de gegevens is gebaseerd op de context binnen de utterance. Een voor beeld is een utterance voor het reserveren van een vlucht met twee locaties, oorsprong en bestemming.

`Book a flight from Seattle to Cairo`

De twee voor beelden van een `location` entiteit moeten worden geëxtraheerd. De client-app moet weten welk type locatie voor elk van deze is, om de aankoop van het ticket te volt ooien.

Er zijn twee technieken voor het uitpakken van context afhankelijke gerelateerde gegevens:

 * De entiteit `location` is een door de machine geleerde entiteit en maakt gebruik van twee subcomponent entiteiten voor het vastleggen van de `origin` en `destination` (voor keur)
 * De `location` entiteit gebruikt twee **rollen** van `origin` en `destination`

Meerdere entiteiten kunnen bestaan in een utterance en kunnen worden geëxtraheerd zonder ontleding of rollen te gebruiken als de context waarin ze worden gebruikt, geen betekenis heeft. Als de utterance bijvoorbeeld een lijst met locaties bevat `I want to travel to Seattle, Cairo, and London.`, is dit een lijst waarin elk item geen extra betekenis heeft.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Subonderdeel entiteiten van een door de machine geleerde entiteit gebruiken om context te definiëren

U kunt een door een [**machine geleerde entiteit**](tutorial-machine-learned-entity.md) gebruiken om de gegevens op te halen die de actie van het reserveren van een vlucht beschrijven en vervolgens de entiteit op het hoogste niveau afbreken in de afzonderlijke onderdelen die nodig zijn voor de client toepassing.

In dit voor beeld kan `Book a flight from Seattle to Cairo`de entiteit op het hoogste niveau `travelAction` zijn en is voorzien van het label om `flight from Seattle to Cairo`uit te pakken. Vervolgens worden er twee subonderdelen gemaakt, met de naam `origin` en `destination`, beide met een beperking die van de vooraf gebouwde `geographyV2` entiteit wordt toegepast. In de training uitingen zijn de `origin` en `destination` op de juiste wijze gelabeld.

### <a name="using-entity-role-to-define-context"></a>De rol van entiteit gebruiken om de context te definiëren

Een rol is een benoemde alias voor een entiteit op basis van de context binnen de utterance. Een rol kan worden gebruikt met een vooraf samengesteld of aangepast entiteits type en wordt gebruikt in beide voor beelden van uitingen en patronen. In dit voor beeld heeft de `location`-entiteit twee rollen van `origin` en `destination` nodig en beide moeten worden gemarkeerd in het voor beeld uitingen.

Als LUIS de `location` vindt maar de rol niet kan bepalen, wordt de locatie-entiteit nog steeds geretourneerd. De client toepassing moet aan een vraag worden opgevolgd om te bepalen welk type locatie de gebruiker had.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Als u meer dan het maximum aantal entiteiten

Neem contact op met de ondersteuning als u meer nodig hebt dan de limiet. Als u dit wilt doen, verzamelt u gedetailleerde informatie over uw systeem, gaat u naar de [Luis](luis-reference-regions.md#luis-website) -website en selecteert u vervolgens **ondersteuning**. Als uw Azure-abonnement ondersteunings services bevat, neemt u contact op met de [technische ondersteuning van Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Status van de voor spelling van de entiteit

De LUIS-portal laat zien wanneer de entiteit, in een voor beeld utterance, een andere voor spelling van de entiteit heeft dan de entiteit die u hebt geselecteerd. Deze andere score is gebaseerd op het huidige getrainde model.

## <a name="next-steps"></a>Volgende stappen

Leer concepten over goede [uitingen](luis-concept-utterance.md).

Zie [entiteiten toevoegen](luis-how-to-add-entities.md) voor meer informatie over het toevoegen van entiteiten aan uw Luis-app.

Zie [zelf studie: gestructureerde gegevens ophalen van gebruikers utterance met door machines geleerde entiteiten in language Understanding (Luis)](tutorial-machine-learned-entity.md) voor meer informatie over het extra heren van gestructureerde gegevens uit een utterance met behulp van de door de machine geleerde entiteit.
 
