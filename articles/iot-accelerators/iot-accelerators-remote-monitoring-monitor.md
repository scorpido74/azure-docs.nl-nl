---
title: Monitor apparaten in de oplossing voor bewaking op afstand - Azure | Microsoft Documenten
description: In deze zelfstudie leert u hoe u uw IoT-apparaten kunt bewaken met behulp van de verbetering voor de externe bewakingsoplossing.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 400a71b11fde210b889d938041e88c5ebe73c1dc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890874"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Zelfstudie: Uw IoT-apparaten bewaken

In deze zelfstudie gebruikt u de verbetering voor de externe bewakingsoplossing om de verbonden IoT-apparaten te bewaken. Gebruik het dashboard van de oplossing om telemetrie, apparaatgegevens, waarschuwingen en KPI's weer te geven.

In de zelfstudie wordt gebruikgemaakt van twee gesimuleerde apparaten voor trucks die telemetriegegevens over de locatie, de snelheid en de temperatuur van de lading verzenden. De trucks worden beheerd door een organisatie met de naam Contoso en zijn verbonden met de verbetering voor de externe bewakingsoplossing. Als operator van Contoso is het uw taak om de locatie en het gedrag van een van uw trucks (truck-02) in het veld te bewaken.

In deze zelfstudie hebt u:

>[!div class="checklist"]
> * De apparaten filteren in het dashboard
> * Telemetrie in realtime weergeven
> * Apparaatdetails weergeven
> * Meldingen van uw apparaten weergeven
> * De systeem-KPI's weergeven

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Kies de weer te geven apparaten

Gebruik filters om te selecteren welke verbonden apparaten worden weergegeven op de pagina **Dashboard**. Om alleen de **Truck**-apparaten weer te geven, kiest u het ingebouwde **Trucks**-filter in de vervolgkeuzelijst met filters:

[![Filter voor vrachtwagens op het dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Wanneer u een filter toepast, worden alleen de apparaten die voldoen aan de filtervoorwaarden weergegeven op de kaart en in het deelvenster Telemetrie. U kunt zien dat er twee trucks zijn verbonden met de oplossingsversnellers, waaronder vrachtwagen-02:

[![Alleen vrachtwagens worden op de kaart weergegeven](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Als u filters wilt maken, bewerken en verwijderen, klikt u op **Apparaatgroepen beheren**.

## <a name="view-real-time-telemetry"></a>Telemetrie in realtime weergeven

De oplossingsversneller plot in realtime telemetrie in de grafiek op de pagina **Dashboard**. Boven in de telemetriegrafiek ziet u de beschikbare telemetrietypen voor de apparaten, waaronder truck-02, die met het huidige filter zijn geselecteerd. Standaard wordt in de grafiek de breedtegraad van de trucks weergegeven, en truck-02 lijkt stil te staan:

[![Vrachtwagentelemetrietypen](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Als u voor de trucks temperatuurtelemetrie wilt weergeven, klikt u op **Temperatuur**: U kunt zien hoe de temperatuur voor truck-02 in het afgelopen uur is veranderd:

[![De temperatuurtelemetrieperceel van de vrachtwagen](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>De kaart weergeven

De kaart bevat informatie over de gesimuleerde trucks die met het huidige filter zijn geselecteerd. U kunt de kaart inzoomen en pannen om locaties met meer of minder details weer te geven. De kleur van een apparaatpictogram op de kaart geeft aan of er voor het apparaat **Meldingen** (donkerblauw) of **Waarschuwingen** (rood) actief zijn. Een samenvatting van het aantal **Meldingen** en **Waarschuwingen** wordt links van de kaart weergegeven.

Als u de details voor truck-02 wilt bekijken, zoomt en pant u de kaart om naar de truck te zoeken en selecteert u deze op de kaart. Klik vervolgens op het apparaatlabel om het deelvenster **Apparaatdetails** te openen. De apparaatdetails omvatten:

* Recente telemetriewaarden
* Methoden die door het apparaat worden ondersteund
* Apparaateigenschappen

[![Apparaatgegevens weergeven op het dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Waarschuwingen weergeven

In het deelvenster **Waarschuwingen** worden gedetailleerde informatie weergegeven over de meest recente waarschuwingen van uw apparaten. De waarschuwingen voor truck-02 geven aan dat de temperatuur van de lading hoger dan normaal is:

[![Apparaatwaarschuwingen weergeven op het dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

U kunt een filter gebruiken om de tijdsduur voor recente meldingen aan te passen. Standaard worden in het deelvenster waarschuwingen van het afgelopen uur weergegeven.

## <a name="view-the-system-kpis"></a>De systeem-KPI's weergeven

De pagina **Dashboard** geeft systeem-KPI's weer die zijn berekend door de oplossingsversneller in het deelvenster **Analyse**:

[![Dashboard-KPI's](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Het dashboard geeft drie KPI's weer voor de meldingen die door de huidige filters voor apparaten en tijdsduur zijn geselecteerd:

* Het aantal actieve meldingen voor de regels die de meeste meldingen hebben geactiveerd.
* Het deel van de meldingen op basis van apparaattype.
* Het percentage meldingen dat uit kritieke meldingen bestaat.

Voor truck-02 bestaan alle meldingen uit waarschuwingen die betrekking hebben op de temperatuur van de lading die hoger is dan normaal.

Dezelfde filters die de tijdsduur instellen voor meldingen en regelen welke apparaten worden weergegeven, bepalen hoe de KPI's worden samengevoegd. Standaard worden in het deelvenster KPI's weergegeven die in de loop van het afgelopen uur zijn samengevoegd.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de pagina **Dashboard** in de verbetering voor de externe bewakingsoplossing gebruikt om de gesimuleerde trucks te filteren en bewaken. Voor informatie over het gebruik van de oplossingsversneller voor het detecteren van problemen met uw verbonden apparaten, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Problemen detecteren met apparaten die zijn verbonden met uw bewakingsoplossing](iot-accelerators-remote-monitoring-automate.md)