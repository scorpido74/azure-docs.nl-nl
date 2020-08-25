---
title: Een analyse uitvoeren van de hoofdoorzaak van een waarschuwing - Azure | Microsoft Docs
description: In deze zelfstudie leest u hoe u met behulp van Azure Time Series Insights een analyse van de hoofdoorzaak uitvoert wanneer een waarschuwing wordt weergegeven.
author: Philmea
ms.author: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: c3cb940583677d813873c07fbfa679fdcc1dff59
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "77565465"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Zelfstudie: Een analyse uitvoeren van de hoofdoorzaak van een waarschuwing

In deze zelfstudie leert u hoe u de hoofdoorzaak van een waarschuwing kunt diagnosticeren met behulp van de verbetering voor de externe bewakingsoplossing. U ziet dat er een waarschuwing is geactiveerd in het dashboard van de externe bewakingsoplossing; gebruik vervolgens de Azure Time Series Insights-verkenner om de hoofdoorzaak te onderzoeken.

In de zelfstudie wordt gebruikgemaakt van twee gesimuleerde apparaten voor vrachtwagens die telemetriegegevens over de locatie, de hoogte, de snelheid en de temperatuur van de lading verzenden. De trucks worden beheerd door een organisatie met de naam Contoso en zijn verbonden met de verbetering voor de externe bewakingsoplossing. Als Contoso-operator moet u begrijpen waarom één van uw vrachtwagens (delivery-truck-02) een waarschuwing voor een lage temperatuur heeft vastgelegd.

In deze zelfstudie hebt u:

>[!div class="checklist"]
> * De apparaten filteren in het dashboard
> * Telemetrie in realtime weergeven
> * Gegevens verkennen met de verkenner van Time Series Insights
> * Een analyse uitvoeren van de hoofdoorzaak
> * Een nieuwe regel maken op basis van wat u hebt geleerd

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Kies de weer te geven apparaten

Gebruik filters om te selecteren welke verbonden apparaten worden weergegeven op de pagina **Dashboard**. Om alleen de **Truck**-apparaten weer te geven, kiest u het ingebouwde **Trucks**-filter in de vervolgkeuzelijst met filters:

[![Filteren op trucks in het dashboard](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Wanneer u een filter toepast, worden alleen de apparaten die voldoen aan de filtervoorwaarden weergegeven op de kaart en in het deelvenster Telemetrie van het **Dashboard**. U kunt zien dat twee trucks verbonden zijn met de oplossingsversnellers, waaronder **vrachtwagen-02**.

## <a name="view-real-time-telemetry"></a>Telemetrie in realtime weergeven

De oplossingsversneller plot in realtime telemetrie in de grafiek op de pagina **Dashboard**. In de grafiek worden standaard de telemetriegegevens over de hoogte weergegeven. Dit verandert na verloop van tijd:

[![Tekengebied voor telemetriegegevens over hoogte van vrachtwagen](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Als u voor de trucks temperatuurtelemetrie wilt weergeven, klikt u op **Temperatuur** in het **deelvenster Telemetrie**. U kunt zien hoe de temperatuur voor beide vrachtwagens in de afgelopen 15 minuten is veranderd. U kunt ook zien dat er een waarschuwing voor lage temperatuur is geactiveerd voor delivery-truck-02 in het deelvenster met waarschuwingen.

[![RM-dashboard met waarschuwing voor lage temperatuur](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>De gegevens verkennen

Als u de oorzaak van het alarm voor een lage temperatuur wilt identificeren, opent u de telemetriegegevens van de vrachtwagen in de Time Series Insights-verkenner. Klik op een van de koppelingen **Verkennen in Time Series Insights** op het dashboard:

[![RM-dashboard met gemarkeerde TSI-koppelingen](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Zodra de verkenner wordt geopend, ziet u al uw apparaten in een lijst:

[![Initiële weergaven TSI Explorer](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Filter de apparaten door **delivery-truck** in het filtervak te typen en selecteer **temperatuur** als de **Meeteenheid** in het linkerdeelvenster:

[![TSI Explorer temperatuur vrachtwagen](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

U ziet dezelfde weergave als in het dashboard Externe bewaking. Bovendien kunt u nu verder inzoomen op het tijdsbestek waarin de waarschuwing is geactiveerd:

[![Zoomen met TSI-verkenner](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

U kunt ook andere telemetriestreams toevoegen die afkomstig zijn uit de vrachtwagens. Klik op de knop **Toevoegen** in de linkerbovenhoek. Er wordt een nieuw deelvenster weergegeven:

[![TSI Explorer met nieuw deelvenster](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

In het nieuwe deelvenster wijzigt u de naam van het nieuwe label in **Apparaten** zodat dit overeenkomt met de vorige. Selecteer **hoogte** als de **Meeteenheid** en **iothub-connection-device-id** als de **Splitsen op**-waarde om de hoogtetelemetrie in uw weergave toe te voegen:

[![TSI Explorer met temperatuur en hoogte](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>De waarschuwing diagnosticeren

Wanneer u de streams in de huidige weergave bekijkt, kunt u zien dat de hoogteprofielen voor de twee vrachtwagens verschillend zijn. Daarnaast treedt het temperatuurverval in **delivery-truck-02** op wanneer de vrachtwagen een grote hoogte bereikt. U bent verbaasd over deze resultaten, omdat was gepland dat de vrachtwagens dezelfde route zouden nemen.

Als u wilt bevestigen dat de vrachtwagens andere routes hebben afgelegd, voegt u in het zijpaneel een nieuw deelvenster toe met behulp van de knop **Toevoegen**. In het nieuwe deelvenster wijzigt u de naam van het nieuwe label in **Apparaten** zodat dit overeenkomt met de vorige. Selecteer **lengtegraad** als **Meeteenheid** en **iothub-connection-device-id** als de **Scheiden op**-waarde om de lengtegraadtelemetrie in uw weergave toe te voegen. U kunt zien dat de vrachtwagens inderdaad een andere route heeft gevolgd door naar het verschil tussen de **lengtegraad**-streams te kijken:

[![TSI Explorer met temperatuur, hoogte en lengtegraad](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Een nieuwe regel maken

Hoewel vrachtwagenroutes doorgaans van tevoren zijn geoptimaliseerd, realiseert u zich dat verkeerspatronen, het weer en andere onverwachte gebeurtenissen tot vertragingen kunnen leiden en dat vrachtwagenchauffeurs naar eigen inzicht op het laatste moment nog de route kunnen veranderen. Aangezien de temperatuur van uw assets in het voertuig echter essentieel is, moet u een aanvullende regel in uw externe bewakingsoplossing maken. Deze regel moet ervoor zorgen dat u een waarschuwing krijgt als de gemiddelde hoogte in een interval van 1 minuut boven 350 feet komt:

[![Tabblad Regels voor externe bewaking voor instellen hoogteregel](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Als u wilt leren hoe u regels maakt en bewerkt, bekijkt u de vorige zelfstudie over [het detecteren van apparaatproblemen](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u de hoofdoorzaak van een waarschuwing kunt diagnosticeren met behulp van de Time Series Insights-verkenner in combinatie met de versneller voor de externe bewakingsoplossing. Voor informatie over het gebruik van de oplossingsversneller voor het detecteren van problemen met uw verbonden apparaten, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Apparaatwaarschuwingen gebruiken om problemen te identificeren en verhelpen met apparaten die zijn verbonden met uw bewakingsoplossing](iot-accelerators-remote-monitoring-maintain.md)
