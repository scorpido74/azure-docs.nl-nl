---
title: Het controleren en verminderen van de beperking in Azure Time Series Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u prestatie problemen bewaken, diagnosticeert en verhelpt die latentie en beperking in Azure Time Series Insights veroorzaken.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 08/27/2019
ms.custom: seodec18
ms.openlocfilehash: 275eff59c56229f45a131e107668b8fefab24536
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123804"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Beperking controleren en beperken om de latentie in Azure Time Series Insights te verminderen

Wanneer de hoeveelheid binnenkomende gegevens de configuratie van uw omgeving overschrijdt, kan er latentie of beperking in Azure Time Series Insights optreden.

U kunt latentie en beperking voor komen door uw omgeving correct te configureren voor de hoeveelheid gegevens die u wilt analyseren.

U hebt hoogstwaarschijnlijk latentie en beperking bij het volgende:

- Voeg een gebeurtenis bron toe die oude gegevens bevat die mogelijk de toegewezen ingangs snelheden overschrijden (Time Series Insights moet worden opgevangen).
- Voeg meer gebeurtenis bronnen toe aan een omgeving, wat resulteert in een piek van aanvullende gebeurtenissen (wat de capaciteit van uw omgeving kan overschrijden).
- Grote hoeveel heden historische gebeurtenissen naar een gebeurtenis bron pushen, wat resulteert in een vertraging (Time Series Insights moet worden opgevangen).
- Koppel referentie gegevens met telemetrie, wat resulteert in een grotere gebeurtenis grootte.  Vanuit een beperkings perspectief wordt een ingangs gegevens pakket met een pakket grootte van 32 KB behandeld als 32-gebeurtenissen, die elk een grootte hebben van 1 KB. De Maxi maal toegestane gebeurtenis grootte is 32 KB; gegevens pakketten groter dan 32 KB worden afgekapt.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Meer informatie over het gedrag van Time Series Insights voor het inkomen van gegevens en hoe u het kunt plannen.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Latentie controleren en beperken met waarschuwingen

Waarschuwingen kunnen u helpen bij het vaststellen en oplossen van latentie problemen die door uw omgeving worden veroorzaakt.

1. Selecteer in de Azure Portal **waarschuwingen**.

   [![Berichten](media/environment-mitigate-latency/add-alerts.png)](media/environment-mitigate-latency/add-alerts.png#lightbox)

1. Het deel venster **regel maken** wordt weer gegeven. Selecteer **toevoegen** onder **voor waarde**.

   [![Waarschuwing toevoegen](media/environment-mitigate-latency/alert-pane.png)](media/environment-mitigate-latency/alert-pane.png#lightbox)

1. Vervolgens configureert u de exacte voor waarden voor de signaal logica.

   [![Signaal logica configureren](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Hier kunt u waarschuwingen configureren aan de hand van een van de volgende voor waarden:

   |Gegevens  |Description  |
   |---------|---------|
   |**Ontvangen bytes van binnenkomend verkeer**     | Aantal onbewerkte bytes dat uit gebeurtenis bronnen is gelezen. Aantal onbewerkte items bevat doorgaans de naam en waarde van de eigenschap.  |  
   |**Ongeldige berichten ontvangen**     | Aantal ongeldige berichten dat is gelezen uit alle Azure Event Hubs-of Azure IoT Hub-gebeurtenis bronnen.      |
   |**Ontvangen berichten met ingang**   | Aantal berichten dat is gelezen uit alle gebeurtenis bronnen van Event Hubs of IoT hubs.        |
   |**In ingangs opgeslagen bytes**     | De totale grootte van de opgeslagen en beschik bare gebeurtenissen voor de query. Grootte wordt alleen berekend op de waarde van de eigenschap.        |
   |**Opgeslagen gebeurtenissen** in ingangs    |   Aantal samengevoegde gebeurtenissen dat is opgeslagen en beschikbaar is voor query's.      |
   |**Tijds vertraging van ontvangen berichten**   |  Verschil in seconden tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij is geplaatst en de tijd die wordt verwerkt in binnenkomend verkeer.      |
   |**Vertraging van ontvangen berichten aantal aangekomen items**   |  Het verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van het bericht dat wordt verwerkt in ingress.      |

   Selecteer **Done**.

1. Nadat u de gewenste signaal logica hebt geconfigureerd, controleert u de gekozen waarschuwings regel visueel.

   [![Ingangs](media/environment-mitigate-latency/ingress.png)](media/environment-mitigate-latency/ingress.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Beperkings-en ingangs beheer

* Als u een beperking hebt, ziet u een waarde voor de *tijds vertraging*voor het ontvangen van berichten, waarmee u kunt zien hoeveel seconden achter uw TSI ligt vanaf de werkelijke tijd dat het bericht de gebeurtenis bron opvraagt (exclusief indexerings tijd van appx). 30-60 seconden).  

  De vertraging bij het *Ontvangen van berichten* van het aantal inkomende berichten moet ook een waarde hebben, zodat u kunt bepalen hoeveel achterstanden er achter u zijn.  De eenvoudigste manier om aan de slag te gaan is om de capaciteit van uw omgeving te verg Roten tot een grootte waardoor u het verschil kunt oplossen.  

  Als u bijvoorbeeld een S1-omgeving met één eenheid hebt en u ziet dat er sprake is van een vertraging van 5.000.000 berichten, kunt u de grootte van uw omgeving verg Roten tot zes eenheden om ongeveer een dag te krijgen.  U kunt nog meer verg Roten om sneller te kunnen werken. De ophaal periode is een veelvoorkomende gebeurtenis bij het inrichten van een omgeving, met name wanneer u deze verbindt met een gebeurtenis bron die al gebeurtenissen bevat of wanneer u grote hoeveel heden historische gegevens uploadt.

* Een andere techniek is het instellen van een waarschuwing over een **opgeslagen ingangs gebeurtenissen** > = een drempel waarde die iets lager is dan de totale capaciteit van de omgeving voor een periode van twee uur.  Deze waarschuwing helpt u te begrijpen als u voortdurend op capaciteit werkt, wat een hoge kans op latentie aangeeft. 

  Als u bijvoorbeeld drie S1-eenheden hebt ingericht (of 2100 gebeurtenissen per minuut ingangs capaciteit), kunt u voor twee uur een waarschuwing voor de **opgeslagen gebeurtenissen** van de ingang instellen voor > = 1900 gebeurtenissen. Als u deze drempel waarde voortdurend overschrijdt en u de waarschuwing hebt geactiveerd, bent u waarschijnlijk onder provisiond.  

* Als u vermoedt dat u wordt vertraagd, kunt u uw **ontvangen berichten** vergelijken met de egressed-berichten van uw gebeurtenis bron.  Als binnenkomend in uw event hub groter is dan uw **berichten ontvangen**, worden uw time series Insights waarschijnlijk beperkt.

## <a name="improving-performance"></a>Prestaties verbeteren

Om het beperken of de latentie te verminderen, is het de beste manier om dit te corrigeren door de capaciteit van uw omgeving te verg Roten.

U kunt latentie en beperking voor komen door uw omgeving correct te configureren voor de hoeveelheid gegevens die u wilt analyseren. Zie [uw omgeving schalen](time-series-insights-how-to-scale-your-environment.md)voor meer informatie over het toevoegen van capaciteit aan uw omgeving.

## <a name="next-steps"></a>Volgende stappen

- Voor aanvullende stappen voor probleem oplossing kunt [u problemen in uw time series Insights omgeving vaststellen en oplossen](time-series-insights-diagnose-and-solve-problems.md).

- Als u meer hulp nodig hebt, start u een gesprek op het [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) of [stack overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). U kunt ook contact opnemen met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/) voor ondersteunings opties.
