---
title: Uw preview-omgeving plannen - Azure Time Series Insights | Microsoft Documenten
description: Aanbevolen procedures voor het configureren, beheren, plannen en implementeren van uw Azure Time Series Insights Preview-omgeving.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045719"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Uw Azure Time Series Insights Preview-omgeving plannen

In dit artikel worden aanbevolen procedures beschreven om snel te plannen en aan de slag te gaan met Azure Time Series Insights Preview.

> [!NOTE]
> Lees [De algemene beschikbaarheidsomgeving](time-series-insights-environment-planning.md)van Azure Time Series Insights voor aanbevolen procedures voor het plannen van een exemplaar met algemene beschikbaarheid timeseries insights.

## <a name="best-practices-for-planning-and-preparation"></a>Best practices voor planning en voorbereiding

Best practices rond planning voor en voorbereiding van uw omgeving worden verder beschreven in de volgende artikelen:

* Wat u krijgt wanneer u [een Time Series Insights Preview-omgeving indient.](#the-preview-environment)
* Wat uw [time series id's en timestamp eigenschappen zijn.](#configure-time-series-ids-and-timestamp-properties)
* Wat het nieuwe [Time Series Model is,](#understand-the-time-series-model)en hoe je je eigen te bouwen.
* Hoe evenementen efficiënt te [verzenden in JSON](#shape-your-events).
* Time Series Insights [business disaster recovery opties](#business-disaster-recovery).

Azure Time Series Insights maakt gebruik van een pay-as-you-go business model. Lees de prijzen van [Time Series Insights voor](https://azure.microsoft.com/pricing/details/time-series-insights/)meer informatie over kosten en capaciteit.

## <a name="the-preview-environment"></a>De voorbeeldomgeving

Wanneer u een Time Series Insights Preview-omgeving indient, maakt u twee Azure-bronnen:

* Een Azure Time Series Insights Preview-omgeving
* Een V1-account voor algemene doeleinden van Azure Storage

Als onderdeel van het inrichtingsproces geeft u op of u een warme winkel wilt inschakelen. Warme winkel biedt u een gelaagde query-ervaring. Als u dit hebt ingeschakeld, moet u een bewaartermijn tussen 7 en 30 dagen opgeven. Query's die binnen de bewaarperiode van warme winkels worden uitgevoerd, bieden over het algemeen snellere responstijden. Wanneer een query zich uitstrekt over de bewaarperiode voor warme winkels, wordt deze geserveerd vanuit het koelhuis.

Vragen over warme winkel zijn gratis, terwijl vragen over koelhuis kosten met zich meebrengen. Het is belangrijk om uw querypatronen te begrijpen en uw warme winkelconfiguratie dienovereenkomstig te plannen. We raden aan dat interactieve analyses van de meest recente gegevens zich bevinden in uw warme winkel en patroonanalyse en langetermijntrends in de kou staan.

> [!NOTE]
> Lees de [API-naslaginformatie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)voor meer informatie over het opvragen van uw warme gegevens.

Om te beginnen heb je drie extra items nodig:

* Een [tijdseriemodel](./time-series-insights-update-tsm.md)
* Een [gebeurtenisbron die is verbonden met inzichten uit de time-serie](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Gebeurtenissen die naar de gebeurtenisbron stromen](./time-series-insights-send-events.md) en die zowel aan het model zijn toegewezen als in geldige JSON-indeling zijn

## <a name="review-preview-limits"></a>Voorbeeldlimieten bekijken

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Tijdreeks-iD's en tijdstempeleigenschappen configureren

Als u een nieuwe Time Series Insights-omgeving wilt maken, selecteert u een time-series-id. Dit werkt als een logische partitie voor uw gegevens. Zoals opgemerkt, zorg ervoor dat uw Time Series ID's klaar.

> [!IMPORTANT]
> Time Series *ID's kunnen later niet worden gewijzigd.* Controleer elk voor de definitieve selectie en het eerste gebruik.

U maximaal drie toetsen selecteren om uw resources op een unieke manier te differentiëren. Lees [Aanbevolen procedures voor het kiezen van een time-series-id](./time-series-insights-update-how-to-id.md) en opslag en [invallen](./time-series-insights-update-storage-ingress.md)voor meer informatie.

De eigenschap **Timestamp** is ook belangrijk. U deze eigenschap aanwijzen wanneer u gebeurtenisbronnen toevoegt. Elke gebeurtenisbron heeft een optionele eigenschap Timestamp die wordt gebruikt om gebeurtenisbronnen in de loop van de tijd bij te houden. Tijdstempelwaarden zijn hoofdlettergevoelig en moeten worden opgemaakt volgens de afzonderlijke specificatie van elke gebeurtenisbron.

> [!TIP]
> Controleer de vereisten voor opmaak en ontlijning voor uw gebeurtenisbronnen.

Wanneer deze leeg blijft, wordt de gebeurtenis-enqueuetijd van een gebeurtenisbron gebruikt als gebeurtenistijdstempel. Als u historische gegevens of batched-gebeurtenissen verzendt, is het aanpassen van de eigenschap Timestamp handiger dan de standaardgebeurteniswachtrijtijd. Lees voor meer informatie over het [toevoegen van gebeurtenisbronnen in Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Begrijp het Model van de tijdreeks

U nu het Time Series Insights-model van uw Time Series Insights-omgeving configureren. Het nieuwe model maakt het gemakkelijk om IoT-gegevens te vinden en te analyseren. Het maakt de curatie, het onderhoud en de verrijking van tijdreeksgegevens mogelijk en helpt bij het voorbereiden van gegevenssets die klaar zijn voor de consument. Het model maakt gebruik van time-series-id's, die toewijzen aan een instantie die de unieke bron koppelt aan variabelen, ook wel typen en hiërarchieën genoemd. Lees meer over het nieuwe [Time Series Model](./time-series-insights-update-tsm.md).

Het model is dynamisch, dus het kan op elk moment worden gebouwd. Om snel aan de slag te gaan, bouw en upload je deze voordat je gegevens in Time Series Insights wilt pushen. Als u uw model wilt bouwen, leest [u Het Model van de tijdreeks gebruiken](./time-series-insights-update-how-to-tsm.md).

Voor veel klanten wordt het Time Series Model toegewezen aan een bestaand assetmodel of ERP-systeem dat al bestaat. Als u geen bestaand model hebt, wordt een vooraf gebouwde gebruikerservaring [geboden](https://github.com/Microsoft/tsiclient) om snel aan de slag te gaan. Als u zich wilt voorstellen hoe een model u kan helpen, bekijkt u de [demo-omgeving van](https://insights.timeseries.azure.com/preview/demo)het voorbeeld.

## <a name="shape-your-events"></a>Geef vorm aan uw evenementen

U controleren hoe u gebeurtenissen naar Time Series Insights stuurt. Idealiter worden uw evenementen goed en efficiënt gedenormaliseerd.

Een goede vuistregel:

* Sla metagegevens op in uw tijdseriemodel.
* Zorg ervoor dat de tijdreeksmodus, instantievelden en gebeurtenissen alleen noodzakelijke informatie bevatten, zoals een time-series-id of eigenschap Timestamp.

Lees Voor meer informatie [Shape-gebeurtenissen](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Volgende stappen

- Bekijk [Azure Advisor](../advisor/advisor-overview.md) om uw configuratieopties voor bedrijfsherstel te plannen.
- Lees meer over [opslag en invallen in](./time-series-insights-update-storage-ingress.md) de Time Series Insights Preview.
- Meer informatie over [gegevensmodellering](./time-series-insights-update-tsm.md) in de Time Series Insights Preview.
