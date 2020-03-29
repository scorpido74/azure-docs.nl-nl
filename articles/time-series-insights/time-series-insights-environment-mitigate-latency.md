---
title: Beperking controleren en verminderen - Azure Time Series Insights | Microsoft Documenten
description: Lees hoe u prestatieproblemen die latentie en beperking veroorzaken, controleren, diagnosticeren en beperken in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 245a0b18187ff1c1b226e94b03374f2c071e51c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314824"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Beperking controleren en beperken om de latentie in Azure Time Series Insights te verminderen

Wanneer de hoeveelheid binnenkomende gegevens de configuratie van uw omgeving overschrijdt, u latentie of beperking ervaren in Azure Time Series Insights.

U latentie en beperking voorkomen door uw omgeving correct te configureren voor de hoeveelheid gegevens die u wilt analyseren.

De kans is groot dat u latentie en beperking ervaart wanneer u:

- Voeg een gebeurtenisbron toe die oude gegevens bevat die uw toegewezen ingressrate kunnen overschrijden (Time Series Insights moet een inhaalslag maken).
- Voeg meer gebeurtenisbronnen toe aan een omgeving, wat resulteert in een piek van extra gebeurtenissen (die de capaciteit van uw omgeving kunnen overschrijden).
- Duw grote hoeveelheden historische gebeurtenissen naar een gebeurtenisbron, wat resulteert in een vertraging (Time Series Insights moet een inhaalslag maken).
- Deel referentiegegevens met telemetrie, wat resulteert in een grotere gebeurtenisgrootte. Vanuit een beperkingsperspectief wordt een ingevallen gegevenspakket met een pakketgrootte van 32 KB behandeld als 32 gebeurtenissen, elk formaat 1 KB. De maximaal toegestane gebeurtenisgrootte is 32 KB; gegevenspakketten groter dan 32 KB worden afgekapt.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Meer informatie over time series Insights-gegevens die het gedrag binnendringen en hoe u dit plannen.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Latentie en beperking controleren met waarschuwingen

Waarschuwingen kunnen u helpen bij het diagnosticeren en beperken van latentieproblemen die zich in uw omgeving voordoen.

1. Selecteer in de Azure-portal de informatieomgeving timeseries insights. Selecteer vervolgens **Waarschuwingen**.

   [![Een waarschuwing toevoegen aan uw Time Series Insights-omgeving](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Selecteer **+ Nieuwe waarschuwingsregel**. Het deelvenster **Regel maken** wordt vervolgens weergegeven. Selecteer **Toevoegen** onder **VOORWAARDE**.

   [![Waarschuwingsvenster toevoegen](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Configureer vervolgens de exacte voorwaarden voor de signaallogica.

   [![Signaallogica configureren](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Van daaruit u waarschuwingen configureren met behulp van enkele van de volgende voorwaarden:

   |Gegevens  |Beschrijving  |
   |---------|---------|
   |**Ontvangen bytes binnenvallen**     | Aantal ruwe bytes dat is gelezen uit gebeurtenisbronnen. Het aantal ruwe gegevens omvat meestal de naam en waarde van de eigenschap.  |  
   |**Ongeldige berichten die niet worden ontvangen**     | Aantal ongeldige berichten dat wordt gelezen uit alle Azure Event Hubs of Azure IoT Hub-gebeurtenisbronnen.      |
   |**Ontvangen berichten binnendringen**   | Aantal berichten dat wordt gelezen uit alle gebeurtenisbronnen van Gebeurtenishubs of IoT Hubs.        |
   |**Opgeslagen bytes binnenlaten**     | Totale grootte van gebeurtenissen die zijn opgeslagen en beschikbaar zijn voor query's. Grootte wordt alleen berekend op basis van de eigenschapswaarde.        |
   |**Opgeslagen gebeurtenissen binnendringen**    |   Aantal afgevlakte gebeurtenissen die zijn opgeslagen en beschikbaar zijn voor query's.      |
   |**Tijdvertraging ontvangen bericht binnenvallen**   |  Verschil in seconden tussen het tijdstip waarop het bericht in de gebeurtenisbron is geplaatst en de tijd dat het wordt verwerkt in Ingress.      |
   |**Achterstand bij het aantal ontvangen berichten binnenvallen**   |  Verschil tussen het volgordenummer van het laatst in de wachtrij staande bericht in de gebeurtenisbronpartitie en het volgordenummer van het bericht dat wordt verwerkt in Ingress.      |

   Selecteer **Done**.

1. Nadat u de gewenste signaallogica hebt geconfigureerd, controleert u de gekozen waarschuwingsregel visueel.

   [![Latentieweergave en grafieken](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Beperking en ingress management

* Als u wordt beperkt, wordt een waarde voor de vertraging van het *ontvangen bericht* van binnenkomen geregistreerd en wordt u geïnformeerd over het aantal seconden dat achter loopt op uw TIme Series Insights-omgeving vanaf het moment waarop het bericht de gebeurtenisbron raakt (exclusief indexeringstijd van appx. 30-60 seconden).  

  *Inbinnendringen Ontvangen Bericht Telling Lag* moet ook een waarde hebben, zodat u bepalen hoeveel berichten achter je bent.  De eenvoudigste manier om ingehaald te raken is om de capaciteit van uw omgeving te verhogen tot een grootte die u in staat zal stellen om het verschil te overwinnen.  

  Als uw S1-omgeving bijvoorbeeld een vertraging van 5.000.000 berichten aantoont, u de grootte van uw omgeving verhogen tot zes eenheden voor ongeveer een dag om ingehaald te worden.  Je zou nog verder kunnen stijgen om sneller in te halen. De inhaalperiode komt vaak voor bij het inrichten van een omgeving, vooral wanneer u deze koppelt aan een gebeurtenisbron die al gebeurtenissen in de omgeving heeft of wanneer u veel historische gegevens uploadt.

* Een andere techniek is het instellen van een **waarschuwing voor ingress stored events** >= een drempel waarde die iets onder uw totale omgevingscapaciteit ligt gedurende een periode van 2 uur.  Deze waarschuwing kan u helpen te begrijpen of u voortdurend op capaciteit bent, wat wijst op een grote kans op latentie. 

  Als u bijvoorbeeld drie S1-eenheden hebt ingericht (of 2100 gebeurtenissen per minuut invallen), u een waarschuwing **voor opgeslagen gebeurtenissen instellen** voor >= 1900 gebeurtenissen gedurende 2 uur. Als u deze drempel steeds overschrijdt en daarom uw waarschuwing activeert, bent u waarschijnlijk onder-ingericht.  

* Als u vermoedt dat u wordt gewurgd, u uw **ontvangen ingevallen berichten** vergelijken met de verzonden berichten van uw gebeurtenisbron.  Als de invallen in uw gebeurtenishub groter zijn dan uw **ontvangen berichten in de ingress,** worden uw Time Series Insights waarschijnlijk beperkt.

## <a name="improving-performance"></a>Betere prestaties

Om beperking of het ervaren van latentie te verminderen, is de beste manier om deze te corrigeren het vergroten van de capaciteit van uw omgeving.

U latentie en beperking voorkomen door uw omgeving correct te configureren voor de hoeveelheid gegevens die u wilt analyseren. Lees [Uw omgeving](time-series-insights-how-to-scale-your-environment.md)schalen voor meer informatie over het toevoegen van capaciteit aan uw omgeving.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over [Het diagnosticeren en oplossen van problemen in uw Time Series Insights-omgeving.](time-series-insights-diagnose-and-solve-problems.md)

- Meer informatie over [het schalen van uw Time Series Insights-omgeving.](time-series-insights-how-to-scale-your-environment.md)