---
title: Uw Azure Time Series Insights-voorbeeld omgeving plannen | Microsoft Docs
description: Plan uw Azure Time Series Insights preview-omgeving.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: 780e639ad6e1ee721820749da2df9ea6efa07bd6
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273719"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Uw Azure Time Series Insights-voorbeeld omgeving plannen

In dit artikel worden aanbevolen procedures beschreven voor het snel plannen en aan de slag gaan met behulp van de Azure Time Series Insights preview.

> [!NOTE]
> Zie [uw Azure time series Insights algemene beschikbaarheids omgeving plannen](time-series-insights-environment-planning.md)voor aanbevolen procedures voor het plannen van een algemene Beschik baarheid time series Insights-exemplaar.

## <a name="best-practices-for-planning-and-preparation"></a>Aanbevolen procedures voor planning en voor bereiding

Als u aan de slag wilt gaan met Time Series Insights, kunt u het beste het volgende weten:

* Wat u krijgt wanneer u [een time series Insights preview-omgeving inricht](#the-preview-environment).
* Wat uw [tijd reeks-id's en time stamp-eigenschappen zijn](#configure-time-series-ids-and-timestamp-properties).
* Wat het nieuwe [Time Series-model is](#understand-the-time-series-model)en hoe u er een kunt maken.
* Het [efficiënt verzenden van gebeurtenissen in JSON](#shape-your-events).
* Time Series Insights [Opties voor herstel na nood](#business-disaster-recovery)geval.

Azure Time Series Insights maakt gebruik van een zakelijk model voor betalen per gebruik. Zie voor meer informatie over kosten en capaciteit [Time Series Insights prijzen](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>De voorbeeld omgeving

Wanneer u een Time Series Insights preview-omgeving inricht, maakt u twee Azure-resources:

* Een Azure Time Series Insights preview-omgeving
* Een Azure Storage v1-account voor algemeen gebruik

Als u wilt beginnen, hebt u drie extra items nodig:

* Een [Time Series-model](./time-series-insights-update-tsm.md)
* Een [gebeurtenis bron die is verbonden met Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Gebeurtenissen die in de gebeurtenis bron stromen](./time-series-insights-send-events.md) die beide zijn toegewezen aan het model en in een geldige JSON-indeling zijn

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Time Series-Id's en time stamp-eigenschappen configureren

Selecteer een tijd reeks-ID om een nieuwe Time Series Insights omgeving te maken. Als u dit doet, fungeert dit als een logische partitie voor uw gegevens. Zorg ervoor dat uw time series-Id's gereed zijn.

> [!IMPORTANT]
> Time Series-Id's zijn *onveranderbaar* en *kunnen later niet meer worden gewijzigd*. Verifieer elk één voor de uiteindelijke selectie en het eerste gebruik.

U kunt Maxi maal drie sleutels selecteren om uw resources uniek te onderscheiden. Lees voor meer informatie [Aanbevolen procedures voor het kiezen van een tijd reeks-id](./time-series-insights-update-how-to-id.md) en [opslag en ingang](./time-series-insights-update-storage-ingress.md).

De tijds tempel eigenschap is ook belang rijk. U kunt deze eigenschap aanwijzen wanneer u gebeurtenis bronnen toevoegt. Elke gebeurtenis bron heeft een optionele time stamp-eigenschap die wordt gebruikt om gebeurtenis bronnen na verloop van tijd bij te houden. Time Stamp-waarden zijn hoofdletter gevoelig en moeten worden opgemaakt als afzonderlijke specificatie van elke bron van de gebeurtenis.

> [!TIP]
> Controleer de vereisten voor de indeling en het parseren van uw gebeurtenis bronnen.

Als dit veld leeg blijft, wordt de tijd voor het plaatsen van gebeurtenissen van een gebeurtenis bron gebruikt als tijds tempel van de gebeurtenis. Als u historische gegevens of batch gebeurtenissen verzendt, is het aanpassen van de tijds tempel eigenschap handiger dan de standaard tijd voor het in de wachtrij plaatsen van gebeurtenissen. Lees voor meer informatie over het toevoegen van [gebeurtenis bronnen in Azure IOT hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Meer informatie over het time series-model

U kunt nu het time series-model van uw Time Series Insights omgeving configureren. Het nieuwe model maakt het eenvoudig om IoT-gegevens te vinden en analyseren. Hiermee wordt de inschakeling, het onderhoud en de verrijking van tijdreeks gegevens mogelijk en helpt u gegevens sets te voorbereiden. Het model maakt gebruik van Time Series-Id's, die zijn toegewezen aan een exemplaar dat de unieke resource koppelt aan variabelen, zoals typen en hiërarchieën. Meer informatie over het nieuwe [Time Series-model](./time-series-insights-update-tsm.md).

Het model is dynamisch, zodat het op elk gewenst moment kan worden gebouwd. Als u snel aan de slag wilt gaan, bouwt en uploadt u deze voordat u gegevens naar Time Series Insights pusht. Zie [het time series-model gebruiken](./time-series-insights-update-how-to-tsm.md)om uw model te bouwen.

Voor veel klanten is het time series-model gekoppeld aan een bestaand Asset model of ERP-systeem dat al aanwezig is. Als u geen bestaand model hebt, wordt een vooraf ontwikkelde gebruikers ervaring [gegeven](https://github.com/Microsoft/tsiclient) om snel aan de slag te gaan. Als u wilt bepalen hoe een model u kan helpen, bekijkt u de voor [beeld-demo omgeving](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Uw gebeurtenissen vorm geven

U kunt controleren hoe u gebeurtenissen naar Time Series Insights verzendt. In het ideale geval zijn uw gebeurtenissen ongebruikelijk en efficiënt.

Een goede vuist regel:

* Sla meta gegevens op in uw time series-model.
* De modus Time Series, instance Fields en Events bevatten alleen de benodigde informatie, zoals een tijd reeks-ID of tijds tempel.

Zie [gebeurtenissen vorm](./time-series-insights-send-events.md#supported-json-shapes)geven voor meer informatie.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Volgende stappen

- Bekijk [Azure Advisor](../advisor/advisor-overview.md) om de configuratie opties voor uw bedrijfs herstel te plannen.

- Meer informatie over [opslag en](./time-series-insights-update-storage-ingress.md) inkomend verkeer vindt u in de preview-versie van Time Series Insights.

- Meer informatie over [gegevens modellering](./time-series-insights-update-tsm.md) in de time series Insights preview.