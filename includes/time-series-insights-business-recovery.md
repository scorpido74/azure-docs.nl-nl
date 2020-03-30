---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013886"
---
## <a name="business-disaster-recovery"></a>Bedrijfsnoodherstel

In dit gedeelte worden functies van Azure Time Series Insights beschreven die apps en services draaiende houden, zelfs als zich een ramp voordoet (bekend als *bedrijfsnoodherstel).*

### <a name="high-availability"></a>Hoge beschikbaarheid

Als Azure-service biedt Time Series Insights bepaalde functies *met hoge beschikbaarheid* door redundantie op Azure-regioniveau te gebruiken. Azure ondersteunt bijvoorbeeld mogelijkheden voor noodherstel via de *beschikbaarheidsfunctie voor meerdere regio's van* Azure.

Extra functies met hoge beschikbaarheid die via Azure worden geleverd (en ook beschikbaar voor elk Time Series Insights-exemplaar) zijn:

- **Failover:** Azure biedt [geo-replicatie en taakverdeling.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
- **Herstel** van gegevens en **herstel van opslag:** Azure biedt [verschillende opties voor het bewaren en herstellen van gegevens.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)
- **Azure Site Recovery**: Azure biedt functies voor siteherstel via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Azure Backup:** [Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) ondersteunt zowel on-premises als in-cloud back-up van Azure VM's.

Zorg ervoor dat u de relevante Azure-functies inschakelt om uw apparaten en gebruikers over een hele regio met een hoge beschikbaarheid te bieden.

> [!NOTE]
> Als Azure is geconfigureerd om beschikbaarheid tussen regio's mogelijk te maken, is er geen extra configuratie voor de beschikbaarheid van meerdere regio's vereist in Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT- en eventhubs

Sommige Azure IoT-services bevatten ook ingebouwde functies voor bedrijfsnoodherstel:

- [Azure IoT Hub disaster recovery met hoge beschikbaarheid,](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)inclusief redundantie binnen de regio
- [Azure-gebeurtenishubs-beleid](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redundantie azure-opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Het integreren van Time Series Insights met de andere services biedt extra mogelijkheden voor noodherstel. Telemetrie die naar uw gebeurtenishub wordt verzonden, kan bijvoorbeeld worden gehandhaafd in een back-up van Azure Blob-opslagdatabase.

### <a name="time-series-insights"></a>Time Series Insights

Er zijn verschillende manieren om uw Time Series Insights-gegevens, -apps en -services actief te houden, zelfs als ze worden verstoord. 

U echter bepalen dat een volledige back-up van uw Azure Time Series-omgeving ook vereist is, voor de volgende doeleinden:

- Als *failover-instantie* specifiek voor Time Series Insights om gegevens en verkeer om te leiden naar
- Gegevens bewaren en controlegegevens

In het algemeen is de beste manier om een Time Series Insights-omgeving te dupliceren het maken van een tweede Time Series Insights-omgeving in een Azure-back-upregio. Gebeurtenissen worden ook vanuit uw primaire gebeurtenisbron naar deze secundaire omgeving verzonden. Zorg ervoor dat u een tweede speciale consumentengroep gebruikt. Volg de richtlijnen voor bedrijfsherstel voor bedrijfsrampen, zoals eerder beschreven.

Ga als lid van het werk om een dubbele omgeving te maken:

1. Maak een omgeving in een tweede regio. Lees Voor meer informatie [een nieuwe timeseries insights-omgeving maken in de Azure-portal.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Maak een tweede speciale consumentengroep voor uw gebeurtenisbron.
1. Verbind die gebeurtenisbron met de nieuwe omgeving. Zorg ervoor dat u de tweede speciale consumentengroep aanwijst.
1. Bekijk de documentatie over De Inzichten van de Time Series [en](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) de documentatie [van Event Hubs.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)

Als er een gebeurtenis optreedt:

1. Als uw primaire regio wordt beïnvloed tijdens een rampincident, u bewerkingen omleiden naar de back-up-time-serie insights-omgeving.
1. Gebruik uw tweede regio om een back-up te maken en alle telemetrie- en querygegevens van Time Series Insights te herstellen.

> [!IMPORTANT]
> Als er een failover optreedt:
> 
> * Er kan ook een vertraging optreden.
> * Er kan een tijdelijke piek in de verwerking van berichten optreden, omdat bewerkingen worden omgeleid.
> 
> Lees [Latentie beperken in Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)voor meer informatie.

