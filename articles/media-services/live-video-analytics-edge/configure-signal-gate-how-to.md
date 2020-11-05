---
title: Een signaal Gate configureren voor het op gebeurtenissen gebaseerde video-opname-Azure
description: Dit artikel bevat richt lijnen voor het configureren van een signaal poort in een media grafiek.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380174"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Een signaal poort configureren voor video-opname op basis van gebeurtenissen

Binnen een media grafiek kunt u met een [signaal poort-processor knooppunt](media-graph-concept.md#signal-gate-processor) media door sturen van het ene naar het andere knoop punt, wanneer de poort wordt geactiveerd door een gebeurtenis. Als deze wordt geactiveerd, wordt de poort geopend en kan de media stroom gedurende een opgegeven duur worden uitgevoerd. Als er geen gebeurtenissen zijn om de Gate te activeren, wordt de poort gesloten en stopt de media stroom. De Signal Gate-processor is van toepassing op video-opname op basis van gebeurtenissen.
In dit artikel vindt u informatie over het configureren van een Signal Gate-processor.

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen
-   [Mediagrafiek](media-graph-concept.md)
-   [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)


## <a name="problem"></a>Probleem
Het kan zijn dat de gebruiker een bepaald tijdstip moet vastleggen voordat of nadat de poort is geactiveerd door een gebeurtenis. De gebruiker weet de acceptabele latentie binnen hun systeem, zodat de gebruiker de latentie van de signaal Gate-processor wil opgeven. De gebruiker wil het kortste en het langst opgeven dat de duur van de registratie kan zijn, ongeacht het aantal nieuwe gebeurtenissen dat is ontvangen.
 
### <a name="use-case-scenario"></a>Use-casescenario
Stel dat u video wilt opnemen wanneer de voor deur van uw gebouw wordt geopend. U wilt dat de **X** seconden voordat de deur wordt geopend, wordt opgenomen in de opname. U wilt dat de opname ten minste **Y** seconden duurt, als de deur niet opnieuw wordt geopend. U wilt dat de opname Maxi maal **Z** seconden duurt, als de deur herhaaldelijk wordt geopend. U weet dat uw deur sensor een latentie van **K** seconden heeft en u de kans te verkleinen dat gebeurtenissen worden genegeerd ("late aankomsts"), zodat u ten minste **K** seconden wilt toestaan dat de gebeurtenissen binnenkomen.


## <a name="solution"></a>Oplossing

**_Signaal Gate processor-para meters wijzigen_* _

Een Signal Gate-processor wordt geconfigureerd met 4 para meters:
- _ *activerings evaluatie venster**
- **offset van activerings signaal**
- **minimale activerings venster**
- **maximum activerings venster**. 

Wanneer de signaal Gate-processor wordt geactiveerd, blijft deze geopend gedurende de minimale activerings tijd. De activerings gebeurtenis begint bij het tijds tempel voor de eerste gebeurtenis, plus de offset van het activerings signaal. Als de signaal Gate-processor opnieuw wordt geactiveerd terwijl deze is geopend, wordt de timer opnieuw ingesteld en blijft de poort ten minste de minimale activerings tijd geopend. De signaal Gate-processor blijft nooit langer open dan de maximale activerings tijd. Een gebeurtenis **(gebeurtenis 1)** die zich voordoet vóór een andere gebeurtenis **(gebeurtenis 2)** , op basis van de tijds tempels van de media, is onderhevig aan de voor waarde dat de systeem-lags en **gebeurtenis 1** bij de signaal Gate-processor worden ontvangen na **gebeurtenis 2**. Als **gebeurtenis 1** niet wordt aangeleverd tussen de ontvangst van **gebeurtenis 2** en het **activerings evaluatie venster** , wordt **gebeurtenis 1** genegeerd en niet door gegeven via de signaal poort processor. Correlatie-Id's worden ingesteld voor elke gebeurtenis. Deze Id's worden ingesteld op basis van de eerste gebeurtenis en zijn opeenvolgend voor elke volgende gebeurtenis.

> [!IMPORTANT]
> De tijd van de media is gebaseerd op het tijds tempel van de media wanneer een gebeurtenis zich voordeed op de media. De volg orde van gebeurtenissen die aan de signaal gate arriveren, mag niet overeenkomen met de volg orde van gebeurtenissen die in de media tijd arriveren.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>Para meters: (gebaseerd op wanneer gebeurtenissen in fysieke tijd arriveren naar de signaal poort)

* **minimumActivationTime (kortst mogelijke duur van een opname)** = het minimale aantal seconden dat de signaal Gate-processor open blijft nadat deze is geactiveerd om nieuwe gebeurtenissen te ontvangen, tenzij wordt onderbroken door de **maximumActivationTime**
* **maximumActivationTime (langst mogelijke duur van een opname)** = het maximum aantal seconden vanaf de eerste gebeurtenis dat de signaal Gate-processor open blijft nadat deze is geactiveerd om nieuwe gebeurtenissen te ontvangen, ongeacht welke gebeurtenissen er worden ontvangen
* **activationSignalOffset** = het aantal seconden tussen de activering van de signaal Gate-processor en het moment waarop de video-opname begint, meestal is deze waarde negatief, om de opname te starten voordat de gebeurtenis wordt geactiveerd
* **activationEvaluationWindow** = het aantal seconden vanaf de eerste activerings gebeurtenis, waarbij een gebeurtenis die heeft plaatsgevonden vóór de eerste gebeurtenis, in de media tijd, aan de signaal Gate-processor moet arriven voordat deze wordt genegeerd en als "late aankomst" wordt beschouwd

> [!NOTE]
> Een late aankomst is een gebeurtenis die aankomt zodra het evaluatie venster voor activering is verstreken, maar deze gebeurtenis vóór de eerste gebeurtenis in de media tijd is aangekomen.

### <a name="limits-of-parameters"></a>Limieten van para meters

* **activationEvaluationWindow: 0 seconden tot 10 seconden**

* **activationSignalOffset:-1 minuut tot 1 minuut**

* **minimumActivationTime: 1 seconde tot 1 uur**

* **maximumActivationTime: 1 seconde tot 1 uur**


Op basis van de use-case worden de para meters als volgt ingesteld:

* **activationEvaluationWindow = K SEC**
* **activationSignalOffset =-X SEC**
* **minimumActivationWindow = Y SEC**
* **maximumActivationWindow = Z SEC**


Hier volgt een voor beeld van de sectie signaal Gate-processor knooppunt in een media grafiek topologie voor de volgende parameter waarden:
* **activationEvaluationWindow = 1 seconde**
* **activationSignalOffset =-5 seconden**
* **minimumActivationTime = 20 seconden**
* **maximumActivationTime = 40 seconden**

> [!IMPORTANT]
> De [ISO 8601-duur notatie](https://en.wikipedia.org/wiki/ISO_8601#Durations
) wordt verwacht voor elke parameter waarde. 
**Bijvoorbeeld: PT1S = 1 seconde**


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Laten we eens kijken hoe deze signaal Gate-processor configuratie in verschillende opname scenario's werkt.


**1 gebeurtenis van 1 bron ( *normale activering* )**

Een Signal Gate-processor die één gebeurtenis ontvangt, resulteert in een record die de offset van het activerings signaal (5) seconden start voordat de gebeurtenis op de poort is aangekomen. De rest van de opname is ' minimale activerings tijd (20) seconden lang omdat er geen andere gebeurtenissen arriveren voordat de minimale activerings tijd is voltooid om de poort opnieuw te activeren.

Voorbeeld diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Normale activering":::

* Duur van de opname =-offset + minimumActivationTime = [E1 + verschuiving, E1 + minimumActivationTime]


**2 gebeurtenissen van 1 bron (opnieuw *geactiveerde activering* )**

Een Signal Gate-processor die twee gebeurtenissen ontvangt, resulteert in een opname die de ' activerings signaal offset ' (vijf) seconden vóór de eerste gebeurtenis op de poort heeft ontvangen. De tweede gebeurtenis arriveert vijf seconden na de eerste gebeurtenis, wat vóór de seconden ' minimale activerings tijd (20) seconde van de eerste gebeurtenis is voltooid, daarom wordt de poort opnieuw geactiveerd om open te blijven. De rest van de opname is ' minimale activerings tijd (20) seconden lang, omdat er geen andere gebeurtenissen arriveren voordat de minimale activerings tijd van de tweede gebeurtenis is voltooid om de Gate opnieuw te activeren.

Voorbeeld diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Activering opnieuw geactiveerd":::

* Duur van de opname =-offset + (aankomst van 2e gebeurtenis-aankomst van 1e gebeurtenis) + minimumActivationTime


**N gebeurtenissen van 1 bron ( *maximum activering* )**

Een Signal Gate-processor die N gebeurtenissen ontvangt, zou leiden tot een opname die "activerings signaal offset" (5) seconden begint voordat de eerste gebeurtenis op de poort is aangekomen. Wanneer elke gebeurtenis arriveert voordat de ' minimale activerings tijd (20) seconden van de vorige gebeurtenis is voltooid, wordt de poort continu opnieuw geactiveerd en blijft de maximale activerings tijd (40) seconden na de eerste gebeurtenis ongewijzigd, waarbij de poort wordt gesloten en worden er geen nieuwe gebeurtenissen meer geaccepteerd.

Voorbeeld diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Max. activering":::
 
* Duur van de opname =-offset + maximumActivationTime

> [!IMPORTANT]
> Diagrammen nemen aan dat elke gebeurtenis in de fysieke en media-tijd arriveert op hetzelfde exemplaar. (Geen late aankomsten)

## <a name="next-steps"></a>Volgende stappen

### <a name="try-it-out"></a>Uitproberen

[Zelf studie over video-opname op basis van gebeurtenissen](event-based-video-recording-tutorial.md)

Met de zelf studie voor het opnemen van video op basis van gebeurtenissen, bewerkt u de [topology.jsop](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), wijzigt u de para meters voor het knoop punt signalgateProcessor en volgt u de rest van de zelf studie. Bekijk de video-opnames om het effect van de para meters te analyseren.



