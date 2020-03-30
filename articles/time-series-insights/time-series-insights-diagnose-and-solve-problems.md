---
title: Problemen diagnosticeren, oplossen en oplossen - Azure Time Series Insights | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u veelvoorkomende problemen in uw Azure Time Series Insights-omgeving diagnosticeren, oplossen en oplossen.
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
ms.openlocfilehash: 209df97169c71d910677ffdb2e2b12593882445b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152575"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Problemen in uw Time Series Insights-omgeving diagnosticeren en oplossen

In dit artikel worden enkele problemen beschreven die u mogelijk tegenkomt in uw Azure Time Series Insights-omgeving. Het artikel biedt mogelijke oorzaken en oplossingen voor de oplossing.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Meer informatie over algemene uitdagingen en oplossingen voor time series Insights van klanten.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Probleem: er worden geen gegevens weergegeven

Er kunnen om verschillende veelvoorkomende redenen geen gegevens in de [Azure Time Series Insights-verkenner](https://insights.timeseries.azure.com) voorkomen:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Oorzaak A: gebeurtenisbrongegevens zijn niet in JSON-indeling

Azure Time Series Insights ondersteunt alleen JSON-gegevens. Lees voor JSON-voorbeelden [Ondersteunde JSON-vormen](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Oorzaak B: de gebeurtenisbronsleutel mist een vereiste toestemming

* Voor een IoT-hub in Azure IoT Hub moet u de sleutel met **serviceverbindingsmachtigingen** opgeven. Selecteer het **iothub-eigenaar-** of **servicebeleid,** omdat beide **serviceverbindingsmachtigingen** hebben.

   [![Machtigingen voor verbinding met IoT Hub-service](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Voor een gebeurtenishub in Azure Event Hubs moet u de sleutel opgeven met **luistermachtigingen.** Een van de **beleidsregels lezen** of **beheren** werkt omdat ze beide **luistermachtigingen** hebben.

   [![Machtigingen voor luisteren naar gebeurtenishub](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Oorzaak C: de meegeleverde consumentengroep is niet exclusief voor Time Series Insights

Wanneer u een IoT-hub of een gebeurtenishub registreert, is het belangrijk om de consumentengroep in te stellen die u wilt gebruiken om de gegevens te lezen. Deze consumentengroep *kan niet worden gedeeld.* Als de consumentengroep wordt gedeeld, koppelt de onderliggende IoT-hub of gebeurtenishub automatisch en willekeurig een van de lezers de verbinding. Bied een unieke consumentengroep voor Time Series Insights om uit te lezen.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Oorzaak D: het milieu is net ingericht

Gegevens worden binnen enkele minuten nadat de omgeving en de gegevens zijn gemaakt, weergegeven in uw Time Series Insights-verkenner.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Probleem: sommige gegevens worden weergegeven, maar gegevens ontbreken

Wanneer gegevens slechts gedeeltelijk worden weergegeven en de gegevens achterlijken te lopen, moet u verschillende mogelijkheden overwegen.

### <a name="cause-a-your-environment-is-being-throttled"></a>Oorzaak A: uw omgeving wordt beperkt

[Beperking](time-series-insights-environment-mitigate-latency.md) is een veelvoorkomend probleem wanneer omgevingen worden ingericht nadat u een gebeurtenisbron met gegevens hebt gemaakt. Azure IoT Hub en Azure Events Hubs slaan gegevens maximaal zeven dagen op. Time Series Insights beginnen altijd met de oudste gebeurtenis in de gebeurtenisbron (first-in, first-out of *FIFO).*

Als u bijvoorbeeld 5 miljoen gebeurtenissen in een gebeurtenisbron hebt wanneer u verbinding maakt met een S1-time-serie Insights-omgeving, leest Time Series Insights ongeveer 1 miljoen gebeurtenissen per dag. Het lijkt erop dat Time Series Insights vijf dagen latentie ondervindt. Wat er echter gebeurt, is dat de omgeving wordt beperkt.

Als u oude gebeurtenissen in uw gebeurtenisbron hebt, u beperking op twee manieren benaderen:

- Wijzig de bewaarlimieten van je gebeurtenisbron om oude gebeurtenissen te verwijderen die je niet wilt weergeven in Time Series Insights.
- Inrichten van een grotere omgevingsgrootte (aantal eenheden) om de doorvoer van oude gebeurtenissen te verhogen. Als u het voorgaande voorbeeld gebruikt, moet de omgeving binnen een dag inhalen als u dezelfde S1-omgeving verhoogt tot vijf eenheden. Als uw productie van steady-state gebeurtenissen 1 miljoen of minder gebeurtenissen per dag is, u de capaciteit van de gebeurtenis beperken tot één eenheid nadat deze is ingehaald.

De beperkingslimiet wordt afgedwongen op basis van het SKU-type en de capaciteit van de omgeving. Alle gebeurtenisbronnen in het milieu delen deze capaciteit. Als de gebeurtenisbron voor uw IoT-hub of gebeurtenishub gegevens de afgedwongen limieten overschrijdt, krijgt u te maken met beperking en vertraging.

De volgende figuur toont een Time Series Insights-omgeving met een SKU van S1 en een capaciteit van 3. Het kan binnendringen 3 miljoen gebeurtenissen per dag.

[![Milieu SKU huidige capaciteit](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Neem bijvoorbeeld aan dat een omgeving berichten van een gebeurtenishub inneemt. Het dagelijkse ingresstarief is ~ 67.000 berichten. Dit tarief vertaalt zich naar ongeveer 46 berichten per minuut. 

* Als elk gebeurtenishubbericht wordt afgevlakt naar één gebeurtenis Time Series Insights, vindt beperking niet plaats. 
* Als elk bericht van de gebeurtenishub wordt afgevlakt tot 100 Time Series Insights-gebeurtenissen, moeten 4.600 gebeurtenissen elke minuut worden ingenomen. 

Een S1 SKU-omgeving met een capaciteit van 3 kan slechts 2.100 gebeurtenissen per minuut binnendringen (1 miljoen gebeurtenissen per dag = 700 gebeurtenissen per minuut bij drie eenheden = 2.100 gebeurtenissen per minuut). 

Lees [Ondersteunde JSON-shapes](./how-to-shape-query-json.md)voor een goed inzicht in hoe de afvlakkinglogica werkt.

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Aanbevolen resoluties voor overmatige beperking

Als u de vertraging wilt oplossen, verhoogt u de SKU-capaciteit van uw omgeving. Lees Voor meer informatie [de omgeving Van Inzicht in uw tijdreeksschalen](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Oorzaak B: initiële opname van historische gegevens vertraagt binnendringen

Als u een bestaande gebeurtenisbron aansluit, bevat uw IoT-hub of gebeurtenishub waarschijnlijk al gegevens. De omgeving begint gegevens op te halen vanaf het begin van de periode van het bewaren van berichten van de gebeurtenisbron. Deze standaardverwerking kan niet worden overschreven. Je throttling gebruiken. Beperking kan een tijdje duren om in te halen als het inneemt historische gegevens.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Aanbevolen resoluties voor grote inname

Ga als het gaat om het oplossen van de vertraging:

1. Verhoog de SKU-capaciteit tot de maximaal toegestane waarde (10, in dit geval). Nadat u de capaciteit hebt vergroot, begint het ingressproces veel sneller in te halen. U wordt in rekening gebracht voor de verhoogde capaciteit. Als u wilt visualiseren hoe snel u een inhaalslag maakt, u het beschikbaarheidsdiagram bekijken in de [Time Series Insights-verkenner.](https://insights.timeseries.azure.com)

2. Wanneer de vertraging wordt ingehaald, verlaagt u de SKU-capaciteit tot uw normale ingressrate.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Probleem: gegevens werden eerder weergegeven, maar worden niet meer weergegeven

TSI neemt geen gegevens meer op, maar gebeurtenissen worden nog steeds gestreamd naar Iot Hub of Event Hub

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-updating"></a>Oorzaak A: uw hubtoegangssleutel is geregenereerd en uw omgeving moet worden bijgewerkt

Dit probleem treedt op wanneer de sleutel die wordt verstrekt bij het maken van uw gebeurtenisbron niet langer geldig is. U ziet telemetrie in uw hub, maar geen ingress ontvangen berichten in Time Series Insights. Als u niet zeker weet of de sleutel opnieuw is gegenereerd, u het activiteitenlogboek van uw gebeurtenishubs doorzoeken op 'Autorisatieregels voor naamruimte maken of bijwerken' of zoeken op 'IotHub-bron maken of bijwerken' voor iot-hub.

Als u uw Time Series Insights-omgeving wilt bijwerken met de nieuwe sleutel, opent u uw hubbron in de Azure-portal en kopieert u de nieuwe sleutel. Navigeer naar uw TSI-bron en klik op Gebeurtenisbronnen. 

   [![Update-toets.](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Selecteer de gebeurtenisbron(s) waarvan de inname is gestopt, plak de nieuwe sleutel en klik op Opslaan.

   [![Update-toets.](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Probleem: de instelling van de eigenschap timestamp-eigenschap van mijn gebeurtenisbron werkt niet

Controleer of de naam en de waarde van de eigenschap tijdstempel voldoen aan de volgende regels:

* De naam van de eigenschap tijdstempel is hoofdlettergevoelig.
* De eigenschapswaarde tijdstempel die afkomstig is van uw gebeurtenisbron als JSON-tekenreeks moet het formaat _yyyy-MM-ddTHH:mm:ss hebben. FFFFFFFK_. Een voorbeeld is **2008-04-12T12:53Z**.

De eenvoudigste manier om ervoor te zorgen dat de naam van uw timestamp-eigenschap wordt vastgelegd en goed werkt, is door de Time Series Insights-verkenner te gebruiken. Selecteer in de verkenner Time Series Insights met de grafiek een periode nadat u de eigenschapsnaam tijdstempel hebt ingevoerd. Klik met de rechtermuisknop op de selectie en selecteer de optie **Gebeurtenissen verkennen.**

De eerste kolomkop moet de naam van uw timestamp-eigenschap zijn. Naast het woord **Timestamp**wordt **($ts)** weergegeven.

De volgende waarden worden niet weergegeven:

- *(abc)*: Geeft aan dat Time Series Insights de gegevenswaarden als tekenreeksen leest.
- *Pictogram Agenda:* geeft aan dat Time Series Insights de gegevenswaarde als *datumtijd*leest.
- *#*: Geeft aan dat Time Series Insights de gegevenswaarden leest als een geheel getal.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over [hoe u de latentie beperken in Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Meer informatie over [het schalen van uw Time Series Insights-omgeving.](time-series-insights-how-to-scale-your-environment.md)