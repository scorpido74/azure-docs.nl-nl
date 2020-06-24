---
title: Ondersteuning voor lang gegevens type toevoegen | Microsoft Docs
description: Ondersteuning voor lang gegevens type
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: b4ddff197ff41ee9ddee129fae4699e18af8d0d0
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898029"
---
# <a name="adding-support-for-long-data-type"></a>Ondersteuning voor lang gegevens type toevoegen

Deze wijzigingen worden alleen toegepast op de preview-omgevingen (PAYG). Als u een omgeving met een standaard-SKU-TSI hebt, kunt u deze wijzigingen negeren.

We gaan wijzigingen aanbrengen in de manier waarop we numerieke gegevens opslaan en indexeren in Azure Time Series Insights preview die van invloed kan zijn op u. Als u een van de onderstaande gevallen gebruikt, moet u zo snel mogelijk de benodigde wijzigingen aanbrengen. Uw gegevens worden vanaf 29 juni tot en met 30 juni 2020 geïndexeerd, afhankelijk van uw regio. Als u vragen hebt of problemen hebt met betrekking tot deze wijziging, verzendt u een ondersteunings ticket via de Azure Portal en vermeldt u deze communicatie.

Deze wijziging is van invloed op de volgende gevallen:

1. Als u momenteel time series-model variabelen gebruikt en alleen integrale gegevens typen in uw telemetriegegevens verzendt.
1. Als u momenteel time series-model variabelen gebruikt en zowel integrale als niet-integrale gegevens typen in uw telemetriegegevens verzendt.
1. Als u Categorische-variabelen gebruikt om gehele waarden toe te wijzen aan categorieën.
1. Als u de Java script-SDK gebruikt om een aangepaste front-end-toepassing te maken.
1. Als u de naam limiet van 1.000 in warme Store (WS) nadert en zowel integrale als niet-integrale gegevens verzendt, kan het aantal eigenschappen worden weer gegeven als een metrische waarde in het [Azure Portal](https://portal.azure.com/).

Als een van de bovenstaande gevallen van toepassing is op u, moet u wijzigingen aanbrengen in uw model om deze wijziging aan te passen. Werk de time series-expressie in de definitie van de variabele in zowel de TSI-Verkenner als in een aangepaste client met behulp van onze Api's met de aanbevolen wijzigingen. Zie hieronder voor meer informatie.

Afhankelijk van uw IoT-oplossing en beperkingen, hebt u mogelijk niet de zicht baarheid van de gegevens die naar uw TSI PAYG-omgeving worden verzonden. Als u niet zeker weet of uw gegevens alleen integraal of zowel integraal als niet-integraal zijn, hebt u een aantal opties. U kunt wachten tot de functie is vrijgegeven en vervolgens uw onbewerkte gebeurtenissen in de Explorer-gebruikers interface verkennen om te begrijpen welke eigenschappen zijn opgeslagen in twee afzonderlijke kolommen. U kunt de onderstaande wijzigingen voor alle numerieke Tags preventief of tijdelijk een subset van gebeurtenissen naar de opslag sturen om uw schema beter te begrijpen en te verkennen. Als u gebeurtenissen wilt opslaan, schakelt u [gebeurtenis vastleggen](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) in voor Event hubs of [routet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) u van uw IOT hub naar Azure Blob Storage. Gegevens kunnen ook worden waargenomen via de [Event hub Verkenner](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)of door de [Event processor host](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)te gebruiken. Als u IoT Hub gebruikt, raadpleegt u de documentatie [hier](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) voor toegang tot het ingebouwde eind punt.

Houd er rekening mee dat als u deze wijzigingen ondervindt en u deze niet op de bovenstaande datums kunt door lopen, u mogelijk een onderbreking ondervindt waarbij de beïnvloede tijd reeks variabelen die worden gebruikt via de query-Api's of Time Series Insights Explorer *Null* retourneert (dat wil zeggen dat er geen gegevens in de Explorer worden weer gegeven).

## <a name="recommended-changes"></a>Aanbevolen wijzigingen

Case 1 & 2: het **gebruik van tijdreeks model variabelen en verzenden van alleen integrale gegevens typen of het verzenden van zowel integrale als niet-integrale typen in telemetriegegevens.**

Als u momenteel telemetriegegevens van een geheel getal verzendt, worden uw gegevens onderverdeeld in twee kolommen: ' propertyValue_double ' en ' propertyValue_long '.

De gegevens van uw geheel getal worden geschreven naar ' propertyValue_long ' wanneer de wijzigingen van kracht worden en eerder zijn opgenomen (en toekomstige opgenomen) numerieke gegevens in ' propertyValue_double ' worden niet gekopieerd.

Als u gegevens over deze twee kolommen wilt opvragen voor de eigenschap propertyValue, moet u de functie *Coalesce ()* scalair gebruiken in uw TSX. De functie accepteert argumenten van hetzelfde gegevens type en retourneert de eerste niet-null-waarde in de lijst met argumenten (Lees [hier](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)meer over het gebruik).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Variabele definitie in time series Explorer-numeriek

*Vorige variabele definitie:*

[![Vorige variabele definitie](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nieuwe definitie van variabele:*

[![Nieuwe definitie van variabele](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

U kunt ook *' Coalesce ($Event. PropertyValue. double, toDouble ($Event. PropertyValue. Long))* gebruiken als de expressie voor de aangepaste [tijd reeks.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Definitie van inline-variabele met Time Series-query-Api's-numeriek

*Vorige variabele definitie:*

    "PropertyValueVariable": {

        "kind": "numeric",

        "value": {

            "tsx": "$event.propertyValue.Double"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

*Nieuwe definitie van variabele:*

    "PropertyValueVariable ": {

        "kind": "numeric",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

U kunt ook *' Coalesce ($Event. PropertyValue. double, toDouble ($Event. PropertyValue. Long))* gebruiken als de expressie voor de aangepaste [tijd reeks.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> U wordt aangeraden deze variabelen bij te werken op alle locaties die kunnen worden gebruikt (Time Series model, opgeslagen query's, Power BI-connector query's).

Voor beeld 3: **Categorische-variabelen gebruiken om gehele waarden toe te wijzen aan categorieën**

Als u momenteel Categorische-variabelen gebruikt die gehele waarden toewijzen aan categorieën, zult u waarschijnlijk de functie toLong gebruiken om gegevens van het type Double te converteren naar een lang type. Net als in de bovenstaande gevallen moet u de kolommen met dubbele en lange gegevens typen samenvoegd.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Variabele definitie in time series Explorer-categorische

*Vorige variabele definitie:*

[![Vorige variabele definitie](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nieuwe definitie van variabele:*

[![Nieuwe definitie van variabele](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

U kunt ook *' Coalesce ($Event. PropertyValue. double, toDouble ($Event. PropertyValue. Long))* gebruiken als de expressie voor de aangepaste [tijd reeks.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

Categorische-variabelen vereisen nog steeds een waarde van het type geheel getal. Het gegevens type van alle argumenten in Coalesce () moet van het type Long zijn in de aangepaste [Time Series-expressie.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Definitie van inline-variabele met behulp van Time Series-query-Api's-categorische

*Vorige variabele definitie:*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "tolong($event.propertyValue.Double)"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

*Nieuwe definitie van variabele:*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

Categorische-variabelen vereisen nog steeds een waarde van het type geheel getal. Het gegevens type van alle argumenten in Coalesce () moet van het type Long zijn in de aangepaste [Time Series-expressie.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> U wordt aangeraden deze variabelen bij te werken op alle locaties die kunnen worden gebruikt (Time Series model, opgeslagen query's, Power BI-connector query's).

Case 4: **de Java script-SDK gebruiken om een aangepaste front-end-toepassing te maken**

Als u van invloed is op bovenstaande gevallen 1-3 hierboven en aangepaste toepassingen bouwt, moet u uw query's bijwerken om de functie *Coalesce ()* te gebruiken, zoals wordt getoond in de bovenstaande voor beelden.

Case 5: de **eigenschaps limiet van de warme Store 1.000 bijna bereikt**

Als u een warme winkel gebruiker bent met een groot aantal eigenschappen en denkt dat deze wijziging uw omgeving zou pushen over de limiet van de eigenschaps naam van 1.000 WS, stuurt u een ondersteunings ticket via de Azure Portal en vermeldt u deze communicatie.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel over [ingangs-en opslag](time-series-insights-update-storage-ingress.md) voor meer informatie over de ondersteunde gegevens typen.
