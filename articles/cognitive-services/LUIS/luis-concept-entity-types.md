---
title: Entiteitstypen - LUIS
titleSuffix: Azure Cognitive Services
description: 'Entiteiten extraheren gegevens uit de utterance. Entiteitstypen geven u voorspelbare extractie van gegevens. Er zijn twee soorten entiteiten: machine-geleerd en niet-machine-geleerd. Het is belangrijk om te weten met welk type entiteit u werkt in uitingen.'
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221027"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entiteiten en hun doel in LUIS

Het primaire doel van entiteiten is om de clienttoepassing voorspelbare extractie van gegevens te geven. Een _optioneel_secundair doel is het stimuleren van de voorspelling van de intentie of andere entiteiten met beschrijvingen.

Er zijn twee soorten entiteiten:

* machine-geleerd - uit de context
* niet-machine-learned - voor exacte tekstovereenkomsten, patroonovereenkomsten of detectie door vooraf gebouwde entiteiten

Door machines geleerde entiteiten bieden een breed scala aan opties voor gegevensextractie. Niet-machine-geleerde entiteiten werken door tekstmatching en kunnen onafhankelijk of als [beperking](#design-entities-for-decomposition) worden gebruikt voor een door de machine geleerde entiteit.

## <a name="entities-represent-data"></a>Entiteiten vertegenwoordigen gegevens

Entiteiten zijn gegevens die u uit de utterance wilt halen, zoals namen, datums, productnamen of een belangrijke groep woorden. Een uiting kan veel entiteiten of helemaal geen bevatten. Een clienttoepassing _heeft mogelijk_ de gegevens nodig om de taak uit te voeren.

Entiteiten moeten consistent worden gelabeld voor alle trainingsuitingen voor elke intentie in een model.

 U uw eigen entiteiten definiëren of vooraf gebouwde entiteiten gebruiken om tijd te besparen voor algemene concepten zoals [datetimeV2,](luis-reference-prebuilt-datetimev2.md) [ordinal](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md)en [telefoonnummer](luis-reference-prebuilt-phonenumber.md).

|Utterance|Entiteit|Gegevens|
|--|--|--|
|Koop 3 tickets naar New York|Vooraf opgebouwd nummer<br>Locatie.Bestemming|3<br>New York|
|Koop een ticket van New York naar Londen op 5 maart|Locatie.Oorsprong<br>Locatie.Bestemming<br>Vooraf gebouwde datetimeV2|New York<br>Londen<br>5 maart 2018|

### <a name="entities-are-optional"></a>Entiteiten zijn optioneel

Hoewel intenties vereist zijn, zijn entiteiten optioneel. U hoeft niet voor elk concept in uw app entiteiten te maken, maar alleen voor de entiteiten die nodig zijn om actie te ondernemen.

Als uw uitingen geen gegevens bevatten die de clienttoepassing vereist, hoeft u geen entiteiten toe te voegen. Naarmate uw toepassing zich ontwikkelt en er een nieuwe behoefte aan gegevens wordt geïdentificeerd, u later de juiste entiteiten toevoegen aan uw LUIS-model.

## <a name="entity-compared-to-intent"></a>Entiteit in vergelijking met intentie

De entiteit vertegenwoordigt een gegevensconcept in de utterance die u wilt extraheren.

Een utterance kan optioneel entiteiten bevatten. Ter vergelijking, de voorspelling van de intentie voor een uiting is _vereist_ en vertegenwoordigt de gehele uiting. LUIS vereist voorbeelduitingen die zijn opgenomen in een intentie.

Houd rekening met de volgende 4 uitingen:

|Utterance|Intentie voorspeld|Uitgewonnen entiteiten|Uitleg|
|--|--|--|--|
|Help|Help|-|Niets om uit te halen.|
|Stuur iets|verzendenSomething|-|Niets om uit te halen. Het model is niet `something` getraind om te extraheren in deze context, en er is ook geen ontvanger.|
|Stuur Bob een cadeautje|verzendenSomething|`Bob`, `present`|Het model is getraind met de [voorgebouwde entiteit personName,](luis-reference-prebuilt-person.md) die de naam `Bob`heeft geëxtraheerd. Een machine-geleerde entiteit is `present`gebruikt om te extraheren .|
|Stuur Bob een doos bonbons|verzendenSomething|`Bob`, `box of chocolates`|De twee belangrijke stukken `Bob` van `box of chocolates`gegevens, en de , zijn geëxtraheerd door entiteiten.|

## <a name="design-entities-for-decomposition"></a>Ontwerpentiteiten voor ontbinding

Het is een goed entiteitontwerp om van uw entiteit op het hoogste niveau een door de machine geleerde entiteit te maken. Hierdoor kunnen wijzigingen in uw entiteitsontwerp in de loop van de tijd en het gebruik van **subcomponenten** (onderliggende entiteiten), optioneel met **beperkingen** en **beschrijvingen,** de entiteit op het hoogste niveau ontleden in de onderdelen die de clienttoepassing nodig heeft.

Ontwerpen voor ontleding stelt LUIS in staat om een diepe mate van entiteitsresolutie terug te geven aan uw clienttoepassing. Hierdoor kan uw clienttoepassing zich richten op bedrijfsregels en gegevensoplossing aan LUIS overlaten.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Machine-aangeleerde entiteiten zijn primaire gegevensverzamelingen

[**Machine-aangeleerde entiteiten**](tutorial-machine-learned-entity.md) zijn de gegevenseenheid op het hoogste niveau. Subcomponenten zijn onderliggende entiteiten van door machines geleerde entiteiten.

Een door de machine geleerde entiteit wordt geactiveerd op basis van de context die wordt geleerd door middel van trainingsuitingen. **Beperkingen** zijn optionele regels die worden toegepast op een door machines geleerde entiteit die de triggering verder beperkt op basis van de definitie van exact-tekstmatching van een entiteit die niet door de machine is aangeleerd, zoals een [lijst](reference-entity-list.md) of [Regex](reference-entity-regular-expression.md). Een `size` door machines geleerde entiteit kan bijvoorbeeld `sizeList` een beperking van `size` een lijstentiteit hebben die de `sizeList` entiteit beperkt om alleen te activeren wanneer waarden in de entiteit worden aangetroffen.

[**Descriptoren**](luis-concept-feature.md) zijn functies die worden toegepast om de relevantie van de woorden of zinnen voor de voorspelling te vergroten. Ze worden *beschrijvingen* genoemd omdat ze worden gebruikt om een intentie of entiteit te *beschrijven.* Descriptoren beschrijven onderscheidende eigenschappen of kenmerken van gegevens, zoals belangrijke woorden of zinnen die LUIS observeert en doorleert.

Wanneer u een functie voor woordgroeplijst maakt in uw LUIS-app, is deze standaard wereldwijd ingeschakeld en wordt deze gelijkmatig van toepassing op alle intenties en entiteiten. Als u de woordgroeplijst echter toepast als een beschrijving (functie) van een door de machine geleerde entiteit (of *model),* wordt het bereik ervan verminderd om alleen op dat model toe te passen en wordt deze niet meer gebruikt met alle andere modellen. Het gebruik van een woordgroep lijst als een beschrijving van een model helpt ontbinding door te helpen met de nauwkeurigheid voor het model wordt toegepast op.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typen entiteiten

Kies de entiteit op basis van hoe de gegevens moeten worden geëxtraheerd en hoe deze moeten worden weergegeven nadat deze is geëxtraheerd.

|Entiteitstype|Doel|
|--|--|
|[**Machine-geleerd**](tutorial-machine-learned-entity.md)|Machine-geleerde entiteiten leren van de context in de utterance. Bovenliggende groepering van entiteiten, ongeacht het type entiteit. Dit maakt variatie van plaatsing in voorbeelduitingen aanzienlijk. |
|[**Lijst**](reference-entity-list.md)|Lijst met items en hun synoniemen geëxtraheerd met **exacte tekst overeenkomst**.|
|[**Patroon.elk**](reference-entity-pattern-any.md)|Entiteit waar het einde van de entiteit moeilijk te bepalen is. |
|[**Voorgebouwde**](luis-reference-prebuilt-entities.md)|Al getraind om specifieke soorten gegevens zoals URL of e-mail te extraheren. Sommige van deze vooraf gebouwde entiteiten worden gedefinieerd in het open-source [Recognizers-Text-project.](https://github.com/Microsoft/Recognizers-Text) Als uw specifieke cultuur of entiteit momenteel niet wordt ondersteund, draagt u bij aan het project.|
|[**Reguliere expressie**](reference-entity-regular-expression.md)|Hiermee wordt de reguliere expressie gebruikt voor **exacte tekstovereenkomst**.|

## <a name="extracting-contextually-related-data"></a>Contextueel gerelateerde gegevens extraheren

Een utterance kan twee of meer exemplaren van een entiteit bevatten waarbij de betekenis van de gegevens is gebaseerd op de context binnen de utterance. Een voorbeeld is een uiting voor het boeken van een vlucht met twee locaties, herkomst en bestemming.

`Book a flight from Seattle to Cairo`

De twee voorbeelden `location` van een entiteit moeten worden geëxtraheerd. De client-applicatie moet weten welk type locatie voor elk van deze locatie is om de ticketaankoop te voltooien.

Er zijn twee technieken voor het extraheren van contextueel gerelateerde gegevens:

 * De `location` entiteit is een door de machine geleerde entiteit `origin` en `destination` gebruikt twee subcomponententiteiten om de entiteit en (voorkeur) vast te leggen
 * De `location` entiteit **roles** gebruikt `origin` twee rollen van en`destination`

Meerdere entiteiten kunnen bestaan in een utterance en kunnen worden geëxtraheerd zonder gebruik te maken van ontbinding of rollen als de context waarin ze worden gebruikt geen betekenis heeft. Als de utterance bijvoorbeeld een lijst met `I want to travel to Seattle, Cairo, and London.`locaties bevat, is dit een lijst waarbij elk item geen extra betekenis heeft.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Subcomponententiteiten van een door machines geleerde entiteit gebruiken om context te definiëren

U een [**door de machine geleerde entiteit**](tutorial-machine-learned-entity.md) gebruiken om de gegevens te extraheren die de actie van het boeken van een vlucht beschrijven en vervolgens de entiteit op het hoogste niveau te ontleden in de afzonderlijke onderdelen die de clienttoepassing nodig heeft.

In dit `Book a flight from Seattle to Cairo`voorbeeld kan de entiteit `travelAction` op het hoogste `flight from Seattle to Cairo`niveau worden gelabeld als uittreksel . Vervolgens worden twee subcomponententiteiten `origin` `destination`gemaakt, aangeroepen en beide `geographyV2` met een beperking toegepast op de vooraf gebouwde entiteit. In de opleidingsuitingen, `origin` `destination` worden en geëtiketteerd e.a.v.

### <a name="using-entity-role-to-define-context"></a>Entiteitsrol gebruiken om context te definiëren

Een rol is een benoemde alias voor een entiteit op basis van context binnen de utterance. Een rol kan worden gebruikt met elk vooraf gebouwd of aangepast entiteitstype en wordt gebruikt in zowel voorbeelduitingen als patronen. In dit voorbeeld `location` heeft de `origin` entiteit `destination` twee rollen van en beide moeten worden gemarkeerd in de voorbeelduitingen.

Als LUIS `location` de functie vindt, maar de rol niet kan bepalen, wordt de locatieentiteit nog steeds geretourneerd. De clienttoepassing moet een vervolg geven aan een vraag om te bepalen welk type locatie de gebruiker bedoelde.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Als u meer dan het maximum aantal entiteiten nodig hebt

Als je meer dan de limiet nodig hebt, neem dan contact op met de ondersteuning. Als u dit wilt doen, verzamelt u gedetailleerde informatie over uw systeem, gaat u naar de [LUIS-website](luis-reference-regions.md#luis-website) en selecteert u **Ondersteuning.** Neem contact op met [azure-technische ondersteuning](https://azure.microsoft.com/support/options/)als uw Azure-abonnement ondersteuningsservices bevat.

## <a name="entity-prediction-status"></a>Entiteitsvoorspellingsstatus

De LUIS-portal geeft aan wanneer de entiteit in een voorbeeldutterance een andere entiteitsvoorspelling heeft dan de entiteit die u hebt geselecteerd. Deze verschillende score is gebaseerd op het huidige getrainde model.

## <a name="next-steps"></a>Volgende stappen

Concepten over goede [uitingen leren](luis-concept-utterance.md).

Zie [Entiteiten toevoegen](luis-how-to-add-entities.md) voor meer informatie over het toevoegen van entiteiten aan uw LUIS-app.

Zie [Zelfstudie: Haal gestructureerde gegevens uit de uiting van de gebruiker met door machines geleerde entiteiten in Language Understanding (LUIS)](tutorial-machine-learned-entity.md) om te leren hoe u gestructureerde gegevens uit een utterance extraheren met behulp van de door de machine geleerde entiteit.
 
