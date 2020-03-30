---
title: Apparaatgegevens analyseren in uw Azure IoT Central-toepassing | Microsoft Documenten
description: Analyseer apparaatgegevens in uw Azure IoT Central-toepassing.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 40460b58ede0ca0da8fe25475906bdbe41bfffe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158279"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Analytics gebruiken om apparaatgegevens te analyseren

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*



Azure IoT Central biedt uitgebreide analysemogelijkheden om historische trends te analyseren en verschillende telemetrieën van uw apparaten te correleren. Ga om aan de slag te gaan naar **Analytics** in het linkerdeelvenster.

## <a name="understanding-the-analytics-ui"></a>Inzicht in de gebruikersinterface van Analytics
Analytics-gebruikersinterface bestaat uit drie hoofdcomponenten:
- **Deelvenster Gegevensconfiguratie:** Selecteer in het configuratiepaneel de apparaatgroep waarvoor u de gegevens wilt analyseren. Selecteer vervolgens de telemetrie die u wilt analyseren en selecteer de aggregatiemethode voor elke telemetrie. **Split** by-besturingselement helpt bij het groeperen van de gegevens door de eigenschappen van het apparaat als afmetingen te gebruiken.

- **Tijdscontrole:** Tijdsbesturingselement wordt gebruikt om de duur te selecteren waarvoor u de gegevens wilt analyseren. U een van beide eindpunten van de tijdschuifregelaar slepen om de tijdspanne te selecteren. Tijdbeheer heeft ook een schuifregelaar **voor intervalgrootte** die de bucket of de intervalgrootte regelt die wordt gebruikt om de gegevens samen te voegen. 

- **Grafiekbesturingselement:** Grafiekbesturingselement visualiseert de gegevens als een lijndiagram. U de zichtbaarheid van specifieke lijnen inschakelen door interactie met de grafieklegenda. 


  ![Overzicht van de gebruikersinterface van Analytics](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Uw gegevens opvragen

U moet beginnen met het kiezen van een **apparaatgroep**en de telemetrie die u wilt analyseren. Zodra u klaar bent, selecteert u **Analyseren** om te beginnen met het visualiseren van uw gegevens.

- **Apparaatgroep:** Een [apparaatgroep](tutorial-use-device-groups.md) is een door de gebruiker gedefinieerde groep van uw apparaten. Bijvoorbeeld, alle koelkasten in Oakland, of Alle versie 2.0 windturbines.

- **Telemetrie:** Selecteer de telemetrie die u wilt analyseren en verkennen. U meerdere telemetrieën selecteren om samen te analyseren. Standaardaggregatiemethode is ingesteld op Gemiddeld voor respectievelijk numeriek en Aantal voor tekenreeksgegevenstype. Ondersteunde aggregatiemethoden voor numerieke gegevenstypen zijn Gemiddeld, Maximaal, Minimum, Aantal en Som.  Ondersteunde aggregatiemethoden voor tekenreeksgegevenstype zijn geteld.

- **Gesplitst door:** 'Split by'-besturingselement helpt bij het groeperen van de gegevens door de eigenschappen van het apparaat als afmetingen te gebruiken. Waarden van het apparaat en de cloudeigenschappen worden samengevoegd met de telemetrie zodra het apparaat wordt verzonden. Als de eigenschap cloud of apparaat is bijgewerkt, ziet u de telemetrie gegroepeerd op verschillende waarden in de grafiek.

    > [!TIP]
    > Als u gegevens voor elk apparaat afzonderlijk wilt weergeven, selecteert u Apparaat-id in het besturingselement 'Splitsen door'.

## <a name="interacting-with-your-data"></a>Interactie met uw gegevens

Zodra u uw gegevens hebt opgevraagd, u beginnen met het visualiseren ervan in het lijndiagram. U telemetrie weergeven/verbergen, de tijdsduur wijzigen, telemetrie in een gegevensraster weergeven.

- **Deelvenster Tijdeditor:** Standaard halen we gegevens op van de afgelopen dag. U een van beide eindpunten van de schuifregelaar slepen om de tijdsduur te wijzigen. U het agendabesturingselement ook gebruiken om een van de vooraf gedefinieerde tijdbuckets te selecteren of een aangepast tijdbereik te selecteren. Tijdbeheer heeft ook een schuifregelaar **voor intervalgrootte** die de bucket of de intervalgrootte regelt die wordt gebruikt om de gegevens samen te voegen.

    ![Tijdeditor](media/howto-create-analytics/timeeditorpanel.png)

    - **Schuifregelaar Binnendatumbereik:** gebruik de twee eindpuntbesturingselementen door ze over de gewenste tijdspanne te slepen. Dit bereik van de binnendatum wordt beperkt door het schuifregelaarbesturingselement voor de buitenste datumbereik.
    
   
    - **Schuifregelaars voor het buitenste datumbereik**: Gebruik de eindpuntbesturingselementen om het buitenste datumbereik te selecteren, dat beschikbaar is voor uw besturingselement voor het binnenste datumbereik.

    - **Knoppen voor datumbereik vergroten en verkleinen:** verhoog of verlaag uw tijdspanne door een van beide knoppen te selecteren voor het gewenste interval.

    - **Schuifregelaar tussen intervalgrootte:** gebruik deze om in en uit intervallen in dezelfde tijdspanne in en uit te zoomen. Deze actie zorgt voor een nauwkeurigere controle van de beweging tussen grote plakjes tijd. U het gebruiken om gedetailleerde weergaven met hoge resolutie van uw gegevens te bekijken, zelfs tot milliseconden. Het standaardbeginpunt van de schuifregelaar is ingesteld als de meest optimale weergave van de gegevens uit uw selectie, die resolutie, querysnelheid en granulariteit in evenwicht brengt.
    
    - **Datumbereikkiezer:** Met dit webbesturingselement u eenvoudig de gewenste datum en tijdsbereiken selecteren. U het besturingselement ook gebruiken om te schakelen tussen verschillende tijdzones. Nadat u de wijzigingen hebt aangebracht die u wilt toepassen op uw huidige werkruimte, selecteert u Opslaan.

    > [!TIP]
    > De intervalgrootte wordt dynamisch bepaald op basis van de geselecteerde tijdspanne. Kleinere tijdspannes maken het mogelijk om de gegevens in zeer gedetailleerde intervallen van maximaal enkele seconden te verzamelen.


- **Grafieklegenda:** Grafieklegenda toont de geselecteerde telemetrie in de grafiek. U over elk item op de legenda zweven om het in focus op de grafiek te brengen. Bij gebruik van 'Split By' wordt de telemetrie gegroepeerd op de respectievelijke waarden van de geselecteerde dimensie. U de zichtbaarheid van elke specifieke telemetrie of de hele groep inschakelen door op de groepsnaam te klikken.  


- **Y-as formaat besturingselement:** y-as modus cycli door de beschikbare y-as weergave opties. Dit besturingselement is alleen beschikbaar wanneer verschillende telemetries worden gevisualiseerd. U de y-as instellen door uit een van de drie modi te kiezen:

    - **Gestapeld:** Een grafiek voor elke telemetrie wordt gestapeld en elk van de grafieken hebben hun eigen y-as. Deze modus is standaard ingesteld.
    - **Gedeeld:** Een grafiek voor elke telemetrie wordt uitgezet tegen dezelfde y-as.
    - **Overlapping:** Gebruik deze om meerdere lijnen op dezelfde y-as te stapelen, waarbij de y-asgegevens veranderen op basis van de geselecteerde regel.

  ![Gegevens over een y-as rangschikken met verschillende visualisatiemodi](media/howto-create-analytics/yaxiscontrol.png)

- **Zoombesturingselement:** Met Zoom u verder inzoomen op uw gegevens. Als u een periode vindt waarop u zich wilt concentreren binnen de resultaatset, gebruikt u de muisaanwijzer om het gebied te grijpen en sleept u het vervolgens naar het eindpunt van uw keuze. Klik vervolgens met de rechtermuisknop op het geselecteerde gebied en klik op Zoomen.

  ![Inzoomen op de gegevens](media/howto-create-analytics/zoom.png)

Onder de ellips zijn er meer grafiekbesturingselementen om met de gegevens te communiceren.

- **Weergaveraster:** Uw resultaten zijn beschikbaar in een tabelindeling, zodat u de specifieke waarde voor elk gegevenspunt weergeven.

- **Een markering laten vallen:** Het besturingselement 'Drop Marker' biedt een manier om bepaalde gegevenspunten in de grafiek te verankeren. Het is handig wanneer u gegevens voor meerdere regels over verschillende perioden probeert te vergelijken.

  ![De rasterweergave voor uw analyses weergeven](media/howto-create-analytics/additionalchartcontrols.png)