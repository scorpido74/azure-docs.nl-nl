---
title: Problemen diagnosticeren, problemen oplossen en oplossen-Azure Time Series Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u veelvoorkomende problemen in uw Azure Time Series Insights omgeving kunt vaststellen en oplossen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 35b330f27ba87aa18ce2c2f275a7b19fdae3cb65
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024412"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Problemen in uw Time Series Insights omgeving diagnosticeren en oplossen

In dit artikel worden enkele problemen beschreven die kunnen optreden in uw Azure Time Series Insights omgeving. Het artikel bevat mogelijke oorzaken en oplossingen voor oplossing.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Meer informatie over veelvoorkomende uitdagingen en oplossingen voor klanten Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Probleem: er worden geen gegevens weer gegeven

Er kunnen om verschillende redenen geen gegevens in de [Azure time series Insights Explorer](https://insights.timeseries.azure.com) optreden:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Oorzaak van een: gebeurtenis bron gegevens bevindt zich niet in JSON-indeling

Azure Time Series Insights ondersteunt alleen JSON-gegevens. Lees voor JSON-voor beelden [ondersteunde JSON-vormen](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Oorzaak B: er ontbreekt een vereiste machtiging voor de bron sleutel van de gebeurtenis

* Voor een IoT-hub in azure IoT Hub moet u de sleutel opgeven die **service Connect** -machtigingen heeft. Selecteer de **iothubowner** of het **service** beleid, want beide hebben **service CONNECT** -machtigingen.

   [verbindings machtigingen voor ![IoT Hub-service](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Voor een Event Hub in azure Event Hubs moet u de sleutel met de machtiging **Luis teren** opgeven. Een van de beleids regels voor **lezen** of **beheren** werkt, omdat deze beide over **Luister** machtigingen beschikken.

   [Luister machtigingen voor de Event hub ![](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Oorzaak C: de verleende consumenten groep is niet exclusief Time Series Insights

Wanneer u een IoT-hub registreert of een Event Hub, is het belang rijk om de Consumer groep in te stellen die u wilt gebruiken voor het lezen van de gegevens. Deze consumenten groep *kan niet worden gedeeld*. Als de Consumer groep wordt gedeeld, wordt de onderliggende IoT-hub of Event Hub automatisch en wille keurig losgekoppeld van een van de lezers. Geef een unieke consumenten groep op voor de Time Series Insights om te lezen.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Oorzaak D: de omgeving is zojuist ingericht

De gegevens worden binnen een paar minuten nadat de omgeving en de gegevens voor het eerst zijn gemaakt in uw Time Series Insights Explorer weer gegeven.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Probleem: sommige gegevens worden weer gegeven, maar er ontbreken gegevens

Wanneer gegevens slechts gedeeltelijk worden weer gegeven en de gegevens zichtbaar zijn, moet u rekening houden met verschillende mogelijkheden.

### <a name="cause-a-your-environment-is-being-throttled"></a>Oorzaak van een: uw omgeving wordt beperkt

[Beperking](time-series-insights-environment-mitigate-latency.md) is een veelvoorkomend probleem bij het inrichten van omgevingen nadat u een gebeurtenis bron hebt gemaakt die gegevens bevat. Azure IoT Hub-en Azure Events-gegevens opslag zijn Maxi maal zeven dagen. Time Series Insights altijd beginnen met de oudste gebeurtenis in de bron van de gebeurtenis (First-in, first-out of *FIFO*).

Als u bijvoorbeeld 5.000.000 gebeurtenissen in een gebeurtenis bron hebt wanneer u verbinding maakt met een S1-Time Series Insights omgeving met één eenheid, wordt door Time Series Insights ongeveer 1.000.000 gebeurtenissen per dag gelezen. Het lijkt erop dat Time Series Insights vijf latenties ondervindt. Maar wat er gebeurt, is dat de omgeving wordt beperkt.

Als u oude gebeurtenissen in uw gebeurtenis bron hebt, kunt u op een van de volgende twee manieren beperken:

- Wijzig de Bewaar limieten van uw gebeurtenis bron om oude gebeurtenissen die u niet wilt weer geven in Time Series Insights te verwijderen.
- Richt een grotere omgevings grootte (aantal eenheden) in om de door Voer van oude gebeurtenissen te verg Roten. Als u in het voor gaande voor beeld dezelfde S1-omgeving tot vijf eenheden voor één dag verhoogt, moet de omgeving binnen een dag worden opgevangen. Als de productie van de vaste-toestand gebeurtenis 1.000.000 of minder gebeurtenissen per dag is, kunt u de capaciteit van de gebeurtenis beperken tot één eenheid nadat deze is opgevangen.

De beperkings limiet wordt afgedwongen op basis van het SKU-type en de capaciteit van de omgeving. Alle gebeurtenis bronnen in de omgeving delen deze capaciteit. Als de gebeurtenis bron voor uw IoT-hub of Event Hub gegevens na de afgedwongen limieten pusht, treden er vertragingen en vertraging op.

In de volgende afbeelding ziet u een Time Series Insights omgeving met een SKU van S1 en een capaciteit van 3. Het kan 3.000.000 gebeurtenissen per dag inkomen.

[huidige capaciteit van de SKU van ![-omgeving](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Als voor beeld wordt ervan uitgegaan dat een omgeving berichten van een Event Hub opneemt. Het dagelijkse ingangs tempo is ~ 67.000 berichten. Dit aantal wordt elke minuut op ongeveer 46 berichten omgezet. 

* Als elke Event Hub-bericht wordt afgevlakt tot één Time Series Insights gebeurtenis, treedt er geen beperking op. 
* Als elke Event Hub-bericht wordt afgevlakt tot 100 Time Series Insights gebeurtenissen, moeten 4.600-gebeurtenissen elke minuut worden opgenomen. 

Een S1-SKU-omgeving met een capaciteit van 3 kan per minuut slechts 2.100 gebeurtenissen binnenkomen (1.000.000 gebeurtenissen per dag = 700 gebeurtenissen per minuut bij drie eenheden = 2.100 gebeurtenissen per minuut). 

Voor een gedetailleerde uitleg over hoe logische afvlakking werkt, leest u [ondersteunde JSON-vormen](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Aanbevolen oplossingen voor buitensporige beperking

Als u de vertraging wilt herstellen, verg root u de SKU-capaciteit van uw omgeving. Lees [uw time series Insights-omgeving schalen](time-series-insights-how-to-scale-your-environment.md)voor meer informatie.

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Oorzaak B: de eerste opname van historische gegevens vertraagt de ingang

Als u een bestaande gebeurtenis bron verbindt, is het waarschijnlijk dat uw IoT-hub of Event Hub al gegevens bevat. De omgeving begint met het ophalen van gegevens uit het begin van de Bewaar periode van de gebeurtenis bron. Deze standaard verwerking kan niet worden overschreven. U kunt bandbreedte beperking inschakelen. Het kan even duren voordat de beperking is opgetreden tijdens het opnemen van historische gegevens.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Aanbevolen oplossingen voor grote eerste opname

De vertraging oplossen:

1. Verhoog de capaciteit van de SKU tot de Maxi maal toegestane waarde (10, in dit geval). Nadat u de capaciteit hebt verhoogd, begint het ingangs proces veel sneller. Er worden kosten in rekening gebracht voor de toegenomen capaciteit. Als u wilt visualiseren hoe snel u opvangt, kunt u de beschikbaarheids grafiek weer geven in de [Time Series Insights Explorer](https://insights.timeseries.azure.com).

2. Wanneer de vertraging is opgevangen, verlaagt u de SKU-capaciteit tot uw normale ingangs rente.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Probleem: het instellen van de eigenschaps naam van de tijds tempel van mijn gebeurtenis is niet mogelijk

Zorg ervoor dat de naam van de tijds tempel eigenschap en de waarde voldoen aan de volgende regels:

* De naam van de tijds tempel eigenschap is hoofdletter gevoelig.
* De waarde van de tijds tempel eigenschap die afkomstig is van uw gebeurtenis bron als JSON-teken reeks moet de notatie _jjjj-mm-ddTuu: mm: SS hebben. FFFFFFFK_. Een voor beeld is **2008-04-12T12:53Z**.

De eenvoudigste manier om ervoor te zorgen dat de naam van de tijds tempel eigenschap wordt vastgelegd en correct werkt, is door de Time Series Insights Explorer te gebruiken. Selecteer in de Time Series Insights Explorer, in de grafiek, een periode nadat u de naam van de tijds tempel eigenschap hebt ingevoerd. Klik met de rechter muisknop op de selectie en selecteer vervolgens de optie **gebeurtenissen verkennen** .

De eerste kolomkop moet de naam van de eigenschap time stamp zijn. Naast het **tijds tempel**van het woord **($TS)** wordt weer gegeven.

De volgende waarden worden niet weer gegeven:

- *(ABC)* : geeft aan dat Time Series Insights de gegevens waarden leest als teken reeksen.
- *Kalender pictogram*: geeft aan dat Time Series Insights de gegevens waarde als *datum/tijd*leest.
- *#* : geeft aan dat de gegevens waarden worden gelezen time series Insights als een geheel getal.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het oplossen van latentie in azure time series Insights](time-series-insights-environment-mitigate-latency.md).

- Meer informatie [over het schalen van uw time series Insights-omgeving](time-series-insights-how-to-scale-your-environment.md).