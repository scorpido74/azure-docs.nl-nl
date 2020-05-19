---
title: Entiteits typen-LUIS
description: Een entiteit extraheert gegevens uit een utterance tijdens de Voorspellings runtime. Een _optioneel_, secundair doel is het verhogen van de voor spelling van de intentie of andere entiteiten door gebruik te maken van de entiteit als een functie.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585637"
---
# <a name="extract-data-with-entities"></a>Gegevens ophalen met entiteiten

Een entiteit extraheert gegevens uit een utterance tijdens de Voorspellings runtime. Een _optioneel_, secundair doel is het verhogen van de voor spelling van de intentie of andere entiteiten door gebruik te maken van de entiteit als een functie.

Er zijn verschillende typen entiteiten:

* [Entiteit op basis van machine learning](reference-entity-machine-learned-entity.md)
* Niet-computer-geleerd gebruikt als vereiste [functie](luis-concept-feature.md) : voor exacte tekst overeenkomsten, patroon overeenkomsten of detectie door vooraf gemaakte entiteiten
* [Patroon. any](#patternany-entity) : om vrije-vorm tekst uit een [patroon](reference-entity-pattern-any.md) te halen, zoals Boek titels

Door machines geleerde entiteiten bieden een breed scala aan opties voor gegevens extractie. Niet door machines geleerde entiteiten werken met tekst overeenkomst en worden gebruikt als een [vereiste functie](#design-entities-for-decomposition) voor een door een machine geleerde entiteit of intentie.

## <a name="entities-represent-data"></a>Entiteiten vertegenwoordigen gegevens

Entiteiten zijn gegevens die u wilt ophalen uit de utterance, zoals namen, datums, product namen of een belang rijke groep woorden. Een utterance kan veel entiteiten bevatten of helemaal geen. Een client toepassing heeft _mogelijk_ de gegevens nodig om de taak uit te voeren.

Entiteiten moeten consistent worden gelabeld voor alle trainings uitingen voor elke intentie in een model.

 U kunt uw eigen entiteiten definiëren of vooraf gemaakte entiteiten gebruiken om tijd te besparen op algemene concepten, zoals [datetimeV2](luis-reference-prebuilt-datetimev2.md), [rang telwoord](luis-reference-prebuilt-ordinal.md), [e-mail adres](luis-reference-prebuilt-email.md)en [telefoon nummer](luis-reference-prebuilt-phonenumber.md).

|Utterance|Entiteit|Gegevens|
|--|--|--|
|3 tickets kopen voor New York|Vooraf gebouwd nummer<br>Doel|3<br>New York|


### <a name="entities-are-optional-but-recommended"></a>Entiteiten zijn optioneel, maar aanbevolen

Hoewel [intenties](luis-concept-intent.md) vereist zijn, zijn entiteiten optioneel. U hoeft geen entiteiten te maken voor elk concept in uw app, maar alleen voor degenen waarbij de client toepassing de gegevens moet hebben of de entiteit fungeert als hint of signaal voor een andere entiteit of intentie.

Als uw toepassing zich ontwikkelt en er een nieuwe nood zaak voor gegevens wordt geïdentificeerd, kunt u later de juiste entiteiten aan uw LUIS-model toevoegen.

## <a name="entity-compared-to-intent"></a>Entiteit vergeleken met intentie

De entiteit vertegenwoordigt een gegevens concept _binnen de utterance_. Een intentie classificeert de _hele utterance_.

Houd rekening met de volgende vier uitingen:

|Utterance|Intentie voor speld|Geëxtraheerde entiteiten|Uitleg|
|--|--|--|--|
|Help|Help|-|Niets te extra heren.|
|Iets verzenden|sendSomething|-|Niets te extra heren. Het model bevat geen vereiste functie om `something` in deze context op te halen en er is geen ontvanger opgegeven.|
|Bob een huidige verzenden|sendSomething|`Bob`, `present`|Het model wordt uitgepakt `Bob` door een vereiste functie van vooraf gedefinieerde entiteit toe te voegen `personName` . Er is een door de machine geleerde entiteit gebruikt om uit te pakken `present` .|
|Een doos van Choco lade verzenden|sendSomething|`Bob`, `box of chocolates`|De twee belang rijke gegevens, `Bob` en de `box of chocolates` , zijn geëxtraheerd door door machines geleerde entiteiten.|

## <a name="design-entities-for-decomposition"></a>Entiteiten voor ontleding ontwerpen

Door machines geleerde entiteiten bieden u de mogelijkheid om uw app-schema te ontwerpen voor ontleding en een groot concept te verbreken in subentiteiten.

Als u ontwerpt voor ontleding, kan LUIS de nauw keurigheid van entiteits omzetting naar uw client toepassing retour neren. Op deze manier kan uw client toepassing zich richten op bedrijfs regels en de gegevens omzetting voor LUIS behouden.

Een door de machine geleerde entiteit triggers op basis van de context die is geleerd via voor beeld uitingen.

Door [**machines geleerde entiteiten**](tutorial-machine-learned-entity.md) zijn de extracten op het hoogste niveau. Subentiteiten zijn onderliggende entiteiten van door machines geleerde entiteiten.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typen entiteiten

Een subentiteit naar een bovenliggend item moet een door de machine geleerde entiteit zijn. De subentiteit kan gebruikmaken van een niet door machines geleerde entiteit als een [functie](luis-concept-feature.md).

Kies de entiteit op basis van de manier waarop de gegevens moeten worden geëxtraheerd en hoe deze moeten worden weer gegeven nadat deze is geëxtraheerd.

|Entiteitstype|Doel|
|--|--|
|[**Machine-geleerd**](tutorial-machine-learned-entity.md)|Extraheer geneste, complexe gegevens die zijn geleerd van voor beelden met labels. |
|[**Orderverzamellijst**](reference-entity-list.md)|Lijst met items en de bijbehorende synoniemen die zijn geëxtraheerd met **exact overeenkomende tekst**.|
|[**Patroon. alle**](#patternany-entity)|Entiteit waarvan het einde van de entiteit moeilijk te bepalen is, omdat de entiteit vrije vorm is. Alleen beschikbaar in [patronen](luis-concept-patterns.md).|
|[**Vooraf gedefinieerde**](luis-reference-prebuilt-entities.md)|Al getraind voor het extra heren van specifieke soorten gegevens, zoals URL of e-mail. Sommige van deze vooraf gemaakte entiteiten worden gedefinieerd in het open source- [tekst](https://github.com/Microsoft/Recognizers-Text) project voor herkenning. Als uw specifieke cultuur of entiteit momenteel niet wordt ondersteund, draagt u bij aan het project.|
|[**Reguliere expressie**](reference-entity-regular-expression.md)|Gebruikt reguliere expressie voor **exacte tekst overeenkomst**.|

## <a name="extracting-contextually-related-data"></a>Contextuele gerelateerde gegevens extra heren

Een utterance kan twee of meer exemplaren van een entiteit bevatten waarbij de betekenis van de gegevens is gebaseerd op de context binnen de utterance. Een voor beeld is een utterance voor het reserveren van een vlucht met twee geografische locaties, oorsprong en bestemming.

`Book a flight from Seattle to Cairo`

De twee locaties moeten worden geëxtraheerd op een manier die de client toepassing het type van elke locatie kent om de aankoop van het ticket te volt ooien.

Als u de oorsprong en bestemming wilt extra heren, maakt u twee subentiteiten als onderdeel van de door de computer geleerde entiteit van de ticket order. Maak voor elk van de subentiteiten een vereiste functie die gebruikmaakt van geographyV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>De vereiste functies gebruiken om entiteiten te beperken

Meer informatie over [vereiste onderdelen](luis-concept-feature.md)

## <a name="patternany-entity"></a>Pattern.any-entiteit

Een patroon. alle zijn alleen beschikbaar in een [patroon](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>App-limieten voor entiteiten overschrijden

Neem contact op met de ondersteuning als u meer nodig hebt dan de [limiet](luis-limits.md#model-limits). Als u dit wilt doen, verzamelt u gedetailleerde informatie over uw systeem, gaat u naar de [Luis](luis-reference-regions.md#luis-website) -website en selecteert u vervolgens **ondersteuning**. Als uw Azure-abonnement ondersteunings services bevat, neemt u contact op met de [technische ondersteuning van Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Status van de voor spelling van de entiteit

In de LUIS-portal wordt weer gegeven wanneer de entiteit een andere voor spelling heeft dan de entiteit die u hebt geselecteerd voor een voor beeld-utterance. Deze andere score is gebaseerd op het huidige getrainde model. Gebruik deze informatie om trainings fouten op te lossen met behulp van een of meer van de volgende:
* Een [functie](luis-concept-feature.md) voor de entiteit maken om het concept van de entiteit te identificeren
* Meer [voor beeld-uitingen](luis-concept-utterance.md) en-labels toevoegen aan de entiteit
* [Bekijk actieve Learning-suggesties](luis-concept-review-endpoint-utterances.md) voor alle uitingen die zijn ontvangen op het Voorspellings eindpunt, waarmee u het concept van de entiteit kunt identificeren.

## <a name="next-steps"></a>Volgende stappen

Leer concepten over goede [uitingen](luis-concept-utterance.md).

Zie [entiteiten toevoegen](luis-how-to-add-entities.md) voor meer informatie over het toevoegen van entiteiten aan uw Luis-app.

Zie [zelf studie: gestructureerde gegevens ophalen van gebruikers utterance met door machines geleerde entiteiten in language Understanding (Luis)](tutorial-machine-learned-entity.md) voor meer informatie over het extra heren van gestructureerde gegevens uit een utterance met behulp van de door de machine geleerde entiteit.

