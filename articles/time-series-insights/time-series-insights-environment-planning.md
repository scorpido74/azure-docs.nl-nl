---
title: Uw GA-omgeving plannen - Azure Time Series Insights | Microsoft Documenten
description: Aanbevolen procedures voor het voorbereiden, configureren en implementeren van uw Azure Time Series Insights GA-omgeving.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 972bb2a804057037deedb448674abafcc175b21f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314807"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Uw GA-omgeving met Inzichten in Azure Time Series plannen

In dit artikel wordt beschreven hoe u uw Ga-omgeving (Algemene beschikbaarheid van Azure Time Series Insights) plannen op basis van uw verwachte invallen en uw vereisten voor gegevensbewaring.

## <a name="video"></a>Video

**Bekijk deze video voor meer informatie over gegevensbewaring in Azure Time Series Insights en hoe u dit plannen:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Aanbevolen procedures

Om aan de slag te gaan met Azure Time Series Insights, u het beste weten hoeveel gegevens u per minuut verwacht te pushen en hoe lang u uw gegevens moet opslaan.  

Lees time [series insights prijzen](https://azure.microsoft.com/pricing/details/time-series-insights/)voor meer informatie over capaciteit en retentie voor beide Time Series Insights SKU's.

Als u uw Time Series Insights-omgeving het beste wilt plannen voor succes op lange termijn, moet u rekening houden met de volgende kenmerken:

- [Opslagcapaciteit](#storage-capacity)
- [Bewaarperiode van gegevens](#data-retention)
- [Invallende capaciteit](#ingress-capacity)
- [Uw evenementen vormgeven](#shape-your-events)
- [Ervoor zorgen dat u referentiegegevens hebt](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Opslagcapaciteit

Time Series Insights bewaart standaard gegevens op basis van de hoeveelheid opslagruimte die u indient (eenheden &#215; de hoeveelheid opslagruimte per eenheid) en de inspiraal.

## <a name="data-retention"></a>Bewaartijd van gegevens

U de instelling **voor gegevensbewaring** wijzigen in uw Azure Time Series Insights-omgeving. U maximaal 400 dagen retentie inschakelen. 

Azure Time Series Insights heeft twee modi:

* Eén modus optimaliseert voor de meest up-to-date gegevens. Het dwingt een beleid af om oude gegevens te **zuiveren,** zodat recente gegevens beschikbaar zijn met de instantie. Deze modus is standaard ingeschakeld. 
* De andere optimaliseert gegevens om onder de geconfigureerde bewaarlimieten te blijven. Als u het binnendringen van de instelling **onderbreekt,** wordt voorkomen dat nieuwe gegevens worden binnengevallen wanneer deze zijn geselecteerd als de **opslaglimiet die het gedrag overschrijdt.**

U het bewaren en schakelen tussen de twee modi op de configuratiepagina van de omgeving in de Azure-portal aanpassen.

> [!IMPORTANT]
> U maximaal 400 dagen gegevensbewaren configureren in uw Azure Time Series Insights GA-omgeving.

### <a name="configure-data-retention"></a>Gegevensretentie configureren

1. Selecteer in de [Azure-portal](https://portal.azure.com)de informatieomgeving timeseries insights.

1. Selecteer in het **omgevingsvenster van Time Series Insights** onder **Instellingen**de optie **Opslagconfiguratie**.

1. Voer in het vak **Gegevensbewaartijd (in dagen)** een waarde tussen 1 en 400 in.

   [![Bewaarbehoud configureren](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Lees Voor meer informatie over het implementeren van een geschikt beleid voor het bewaren van gegevens , [leest u Bewaarbehoud configureren.](./time-series-insights-how-to-configure-retention.md)

## <a name="ingress-capacity"></a>Invallende capaciteit

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Ruimtelijke ordening

Het tweede gebied waarop u zich concentreren voor het plannen van uw Time Series Insights-omgeving, is de invallende capaciteit. Invallen de capaciteit is een afgeleide van de toewijzing per minuut.

Vanuit een beperkingsperspectief wordt een ingevallen gegevenspakket met een pakketgrootte van 32 KB behandeld als 32 gebeurtenissen, elk 1 KB groot. De maximaal toegestane gebeurtenisgrootte is 32 KB. Gegevenspakketten groter dan 32 KB worden afgekapt.

U de capaciteit van een S1- of S2 SKU verhogen tot 10 eenheden in één omgeving. U niet migreren van een S1-omgeving naar een S2. U niet migreren van een S2-omgeving naar een S1.

Voor binnendringende capaciteit bepaalt u eerst de totale ingress die u per maand nodig hebt. Bepaal vervolgens wat uw behoeften per minuut zijn. 

Beperking en latentie spelen een rol in de capaciteit per minuut. Als u een piek in uw gegevensingress hebt die minder dan 24 uur duurt, kan Time Series Insights "inhalen" met een invallend tarief van twee keer de tarieven die in de vorige tabel worden vermeld.

Als u bijvoorbeeld één S1 SKU hebt, u gegevens binnendringt met een snelheid van 720 gebeurtenissen per minuut en de gegevenssnelheid voor minder dan een uur met een snelheid van 1.440 gebeurtenissen of minder, is er geen merkbare latentie in uw omgeving. Als u echter meer dan 1.440 gebeurtenissen per minuut gedurende meer dan een uur overschrijdt, zult u waarschijnlijk latentie ervaren in gegevens die zijn gevisualiseerd en beschikbaar zijn voor query's in uw omgeving.

U weet misschien niet van tevoren hoeveel gegevens u verwacht te pushen. In dit geval u gegevenstelemetrie voor [Azure IoT Hub](../iot-hub/iot-hub-metrics.md) en [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) vinden in uw Azure-portalabonnement. De telemetrie kan u helpen bepalen hoe u uw omgeving interichten. Gebruik het deelvenster **Metrische gegevens** in de Azure-portal voor de desbetreffende gebeurtenisbron om de telemetrie weer te geven. Als u uw gebeurtenisbronstatistieken begrijpt, u uw Time Series Insights-omgeving effectiever plannen en inrichten.

### <a name="calculate-ingress-requirements"></a>Binnenkomende vereisten berekenen

Ga als het gaat om het berekenen van uw invallende vereisten:

- Controleer of uw invallende capaciteit boven uw gemiddelde tarief per minuut ligt en of uw omgeving groot genoeg is om uw verwachte binnendringen te verwerken, gelijk aan twee keer uw capaciteit gedurende minder dan een uur.

- Als er invallenpieken optreden die langer dan 1 uur duren, gebruikt u de pieksnelheid als uw gemiddelde. Een omgeving inrichten met de capaciteit om de pieksnelheid te verwerken.

### <a name="mitigate-throttling-and-latency"></a>Beperking en latentie beperken

Lees [Latentie en beperking beperken voor](time-series-insights-environment-mitigate-latency.md)informatie over het voorkomen van beperking en latentie.

## <a name="shape-your-events"></a>Geef vorm aan uw evenementen

Het is belangrijk om ervoor te zorgen dat de manier waarop u gebeurtenissen naar Time Series Insights verzendt, de grootte van de omgeving ondersteunt die u indient. (Omgekeerd u de grootte van de omgeving toewijzen aan het aantal gebeurtenissen dat Time Series Insights leest en de grootte van elke gebeurtenis.) Het is ook belangrijk om na te denken over de kenmerken die u zou willen gebruiken om te snijden en te filteren op wanneer u uw gegevens opvraagt.

> [!TIP]
> Bekijk de JSON-documentatie voor het vormgeven van gebeurtenissen in [het verzenden van gebeurtenissen](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Zorg ervoor dat u referentiegegevens hebt

Een *referentiegegevensset* is een verzameling items die de gebeurtenissen uit uw gebeurtenisbron vergroten. De Time Series Insights-ingress-engine voegt elke gebeurtenis van uw gebeurtenisbron samen met de bijbehorende gegevensrij in uw referentiegegevensset. De augmented event is dan beschikbaar voor query. De join is gebaseerd op de kolommen **Primaire sleutel** die zijn gedefinieerd in de referentiegegevensset.

> [!NOTE]
> Referentiegegevens worden niet met terugwerkende kracht samengevoegd. Alleen huidige en toekomstige instroomgegevens worden gekoppeld aan de referentiegegevensset nadat deze is geconfigureerd en geüpload. Als u van plan bent een grote hoeveelheid historische gegevens naar Time Series Insights te verzenden en niet eerst referentiegegevens uploadt of maakt in Time Series Insights, moet u mogelijk uw werk opnieuw doen (hint: niet leuk).  

Lees onze [documentatie over de referentiegegevens voor](time-series-insights-add-reference-data-set.md)meer informatie over het maken, uploaden en beheren van uw referentiegegevens in Time Series Insights.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag door [een nieuwe Time Series Insights-omgeving te maken in de Azure-portal.](time-series-insights-get-started.md)

- Meer informatie over het [toevoegen van een gebeurtenisbron van Event Hubs](time-series-insights-how-to-add-an-event-source-eventhub.md) aan Inzichten in de time-serie.

- Lees meer over het [configureren van een IoT Hub-gebeurtenisbron](time-series-insights-how-to-add-an-event-source-iothub.md).
